---
layout: post
title:  "🌈 IMU와 LiDAR를 퓨전해야 하는 이유 2 (Feat. PyPose and Open3D)"
date:   2023-01-09 00:00:00 +0000
categories: SLAM
---

# Loosely-coupled Lidar-Inertial odometry 실습
- <a href="/slam/2023/01/01/imu-lidar-fusion1.html"> 저번 블로그 글 </a> 에서 IMU만으로 odometry (즉, relative motion 추정) 를 하기 위해서는 global attidue가 중요함을 알아보았다.
  - 물론 PVA (position, velocity, attitude) 가 모두 중요하다.
<!-- - LiDAR scan matching (e.g., ICP) 이 PVA correction 을 해줄 수 있다. 좋은 global pose를 공급받은 IMU는 더 좋은 IMU odometry 를 생성한다. => 이는 다음 턴의 ICP가 잘 수행되기 위한 좋은 이니셜이 된다. => 좋은 이니셜을 공급받은 좋은 registration이 IMU의 state (i.e., PVA) 를 더 잘 보정한다 => 좋은 IMU pose 는 좋은 IMU odometry를 생성한다 => ICP가 잘 된다 => IMU의 pose 가 또 잘 보정된다 => ... -->
- 하지만 값비싼 GPS+INS 없이도, LiDAR 센서를 이용해서 이러한 보정을 해줄 수 있다.
- Python만 이용해서 간단하게 **loosely-coupled lidar-inertial odometry** (a.k.a mini-pyllio) 를 구현해보자.

## 개요
- <a href="/slam/2023/01/01/imu-lidar-fusion1.html"> 저번 블로그 글 </a> 에서 IMU만으로 odometry (즉, relative motion 추정) 를 하기 위해서는 global attidue가 중요함을 알아보았다.
- KITTI dataset 에서 제공하는 값비싼 GPS+INS 센서 (== oxts) 가 제공하는 Global rotation 을 공급한 경우, 이 값으로 중력을 잘 보상해줄 수 있었고, 결과적으로 유효한 가속도를 계산할 수 있게 되고, 이는 velocity 와 position 예측을 더 잘 할 수 있게 해주었다.
- 하지만 값비싼 GPS+INS 시스템을 사용할 수 없다면 어떻게 해야 할까? 혹은 실내에서 GPS 신호가 공급되지 않을 때에는 어떻게 해야할까?
- LiDAR 를 이용해서 이 역할을 대신해보자.

## 구현
### 목적
- Python 만을 이용해서 최소한의 코드로 구현하는 것이 목적이었다.
  - IMU propagation 은 Pypose 를 사용하였고, LiDAR scan-to-scan registration 은 Open3D 를 사용하였다.

### 구현체
- [https://github.com/gisbi-kim/mini-pyllio](https://github.com/gisbi-kim/mini-pyllio)
  - 실습하는 방법은 github readme 를 참고.

### 톺아보기
- 메인부분만 보면 다음과 같이 간단하다.
  ```ruby
    # state estimation for a KITTI dataset raw data sequence (e.g., 2011_09_30_0018)
    llio = LLIO("cfg.yml")
    llio.init_integrator()

    for idx, data in enumerate(llio.dataloader):
        state = llio.propogate(data)
        state = llio.correct(data, llio.cfg["use_lidar_correction"])

        llio.append_log(data, state)

    llio.visualize_traj()
  ```
  - 제일 마지막에 결과를 그리도록 하였다 (`visualize_traj()`).

- 단계별로 요약해보자면:
  1. 준비 단계
    - `LLIO` (Loosely-coupled LiDAR Inertial Odometry) 객체를 생성하면 dataset을 load 한다.
    ```ruby
        def load_dataset(self):
        self.dataset = KittiDataloader(
            self.cfg["input"]["dataroot"],
            self.cfg["input"]["dataname"],
            self.cfg["input"]["datadrive"],
            duration=self.cfg["step_size"],
            step_size=self.cfg["step_size"],
        )
    ```
    - 교육용 목적이기 때문에 lidar scan 을 한번에 다 읽어서 메모리에 올리도록 간단하게 구현하였다. 그래서 수 초가 소요되고 (데이터셋의 크기에 따라) 수G 정도의 여분의 메모리가 필요할 수 있다 (ps. 2011_09_30_0020 가 frame이 1000개 정도되는 꽤 짧은 시퀀스이기 때문에 이걸로 실습하면 편리할 것이다).
    - 위의 configuration에서 `step_size` 는 lidar aiding 을 받을 간격을 의미한다.
          - 예를 들어 5로 설정하면, LiDAR 는 5 frame 마다 한번씩만 보정을 한다. 보정하지 않는 나머지 4 프레임 간격에 대해서는 보정없이, imu only로 propagation 을 한다라고 생각하면 된다.
          - 실제로도, IMU (보통 100hz 이상) 보다 LiDAR 센서 (10hz) 의 frame 이 더 sparse 하게 들어오기 때문에, correction 은 sparse 하게 일어나고, propagation 은 더 자주 일어나는 상황이 일반적이다. 그래서 보통은 모든 lidar frame 에 대해서 correction을 수행할 수 있다 (그것보다 이미 imu hz가 더 높기 때문에).
          - 다만 이 실습예제에서는 [KITTI dataset 의 raw data](https://www.cvlibs.net/datasets/kitti/raw_data.php) 를 사용하고 있는데, 여기에서는 imu data (in the `oxts` directory) 가 lidar frame 에 동기화되어 있다 (미리 relative frame 사이의 angular velocity 와 linear acceleration을 preintegration해둔 값을 제공하고 있다 라고 보면 되겠다). 따라서 같은 개수의 파일이 존재한다. 이런 상황에서 `step_size` 를 5로 설정한다면, 앞서 말했듯, LiDAR 는 5 frame 마다 한번씩만 보정을 한다. 보정하지 않는 나머지 4 프레임 간격에 대해서는 보정없이, imu only로 propagation 을 한다라고 생각하면 된다. 10hz lidar 인 경우, 0.5초 동안 imu로만 odometry를 수행하는 것과 동일하다. 그렇게 에러가 누적될 때쯤, lidar scan-to-scan matching 을 수행해서 IMU state 의 PVA (position, velocity, attitude) 를 보정해준다. 그러면 IMU는 다시 좋은 상태를 갖게 되고, 다시 한동안 (물론 0.5초와 같이 여전히 짧은 시간 (IMU입장에서는 다소 긴시간일지도) 내에서 말이다) lidar (가 들어오지 않거나) 보정을 받지 못하더라도 어느정도 잘 egomotion estimation 을 해줄 수 있기를 우리는 기대할 수 있다.
  2. Main
      1. **Propagation**
        - Propagation 을 위해서는 Pypose의 `IMUPreintegrator` 를 사용한다.
          ```ruby
          def propogate(self, data):
                  propagated_state = self.integrator(  # == forward()
                      dt=data["dt"],
                      gyro=data["gyro"],
                      acc=data["acc"],
                      rot=self.get_rotation(data)
                  )
          ```
              - 더 구체적인 구현 디테일은 [이 라인](https://github.com/gisbi-kim/mini-pyllio/blob/f0b054d0431d0ff4ade14af50903c08b499769f3/python/llio.py#L97)을 참고.
      2. **Correction**
      - Correction 단계에서는 먼저 scan-to-scan matching 을 통해서 (imu propagation only보다 정밀해진) relative motion 을 계산 한다. 그리고 이를 직전 pose 에 compose 해서 global pose 를 correct해준다.
        ```ruby
              # registration and get a more precise relative motion
              pcd = utils.downsample_points(data["velodyne"][0], self.cfg)
              dx_imu = self.registration(source=pcd, target=self.pcd_previous)

              # correct the current global pose
              pose_corrected_prev = self.pose_corrected
              pose_corrected = pose_corrected_prev @ dx_imu

              # update the state of the estimator
              dt_batch = data["dt"][..., -1, :] * self.cfg["step_size"]  # sec
              corrected_state = self.update_state(dt_batch, pose_corrected)
        ```

          - 이 과정에서 어짜피 ICP해서 relative motion 을 구할거면 IMU가 왜 필요한가? 라는 질문을 할 수 있겠다.
            - 하지만 ICP는 initial guess 가 좋을 때에만 정답으로의 수렴을 보장할 수 있다.
            - 만약 로봇이 고속으로 주행하고 있거나, 혹은 회전하고 있거나, 혹은 동적물체가 너무 많거나 등 다양한 이유로 인해 주변 환경의 structure 가 locally converge 할 위험이 많다.
            - 특히 위의 예시에서 step_size 를 5로 설정하고 있는데, 즉 0.5초 전후의 스캔을 정합하려고 하는 것이다. KITTI dataset에서의 자동차의 경우 0.5초 만에 수 meter 를 진행하게 되는데, 수 meter 정도의 translation 이 있을 때 initial guess 없이 (== using identity) ICP 가 수렴하기를 기대하기는 일반적으로 어렵다 (아래 **실험결과**에서 정량적으로 알아보았다).
            - 그래서 아래와 같이 IMU 가 propagate 한 relative motion 을 이용해서 initial guess 를 공급해주면 ICP가 더 잘 수행될 수 있기 때문에, IMU가 LiDAR에게 필요한 것이다.
            ```ruby
                dx_imu_initial = self.relative_pose_propagated
                dx_lidar_initial = i2v @ dx_imu_initial @ v2i # important!
                icplib = o3d.pipelines.registration
                dx_lidar = icplib.registration_generalized_icp(
                    source,
                    target,
                    self.cfg["icp_inlier_threshold"],
                    dx_lidar_initial,
                    icplib.TransformationEstimationForGeneralizedICP(),
                ).transformation
            ```
              - Tip: 이 때 `error = np.linalg.inv(dx_lidar) @ dx_lidar_initial` 와 같이 initial guess 와 ICP로 부터 얻은 transform 사이의 차이를 계산해보면, IMU가 얼마나 좋은 initial 을 공급해주는 지 알아볼 수 있다. step size 를 1이나 2로 설정할 경우 거의 수 cm 수준만의 차이를 보이는 것을 알 수 있다.
              - PS: 물론 IMU없이 LiDAR 만으로도 robust 한 local keypoint matching 을 통해 좋은 registration initial 을 만들어 줄 수도 있다 (예시: [Open3D's global registration](http://www.open3d.org/docs/release/tutorial/pipelines/global_registration.html), or [Quatro ICRA22](https://github.com/url-kaist/Quatro)). 하지만 IMU 가 갖는 장점은, 이런 방식들에 비해 (propagation하는 것은) 추가적인 비용이 거의 들지 않는다는 것이다! 물론 어느쪽이 항상 더 낫다 라고 말할 수는 없는 것이고, point cloud 센서 데이터만 덩그러니 주어지는 상황인가, 혹은 a robot 이 real-time 으로 recursively state 를 예측해나가는 상황인가, 에 따라 엔지니어가 자원과 요구사항을 고려해서 적절하게 선택해야 할 것이다.
          - 이렇게 계산한 보정된 relative motion 을 이용해서 global pose 를 correction 해준다.
        ```ruby
        def update_state(self, dt_batch, pose_corrected):
                # prepare
                global_pos = torch.tensor(pose_corrected[:3, -1]).unsqueeze(0)

                pose_corrected_prev = self.pose_corrected
                global_delta_pos = torch.tensor(
                    pose_corrected[:3, -1] - pose_corrected_prev[:3, -1]).unsqueeze(0)
                global_vel = global_delta_pos / dt_batch

                global_rot = pp.SO3(R.from_matrix(pose_corrected[:3, :3]).as_quat())

                # update (loosely coupled, thus do explicit replacement) the engine state
                self.integrator.pos = global_pos
                self.integrator.vel = global_vel
                self.integrator.rot = global_rot
        ```
            - 이 과정에서 integrator 내부의 state 를 그냥 바꿔치기 하는 셈이므로 **loosely coupled** lidar-inertial odometry system 이라고 말할 수 있다. correction 과정에서 IMU는 LiDAR에게 initial 을 독립적으로 제공하고, 실제로 보정치는 LiDAR registration 만으로 이루어졌기 때문이다.


## 실험 결과
- 세팅
  - LiDAR registration할 때 point cloud 는 [0.5m 로 voxel downsampling](https://github.com/gisbi-kim/mini-pyllio/blob/f0b054d0431d0ff4ade14af50903c08b499769f3/python/cfg.yml#L20) 하였다.
  - `2011_09_30_0018` 시퀀스에 대해 실험하였다.
- **Ablations for each comparison methods**
  <p align="center">
    <img src="/assets/data/2023-01-09-imu-lidar-fusion2/result1.png" width="1500"/>
  </p>
- **Z 비교**
  <p align="center">
    <img src="/assets/data/2023-01-09-imu-lidar-fusion2/result2.png" width="900"/>
  </p>
- **요약**
  - LiDAR only 는 scan과 scan 사이 간격이 멀어질 수록 initial guess 없이는 fail하는 것을 볼 수 있다.
  - GT rotation 을 공급한 것보다, sparse 하더라도 lidar 로 보정한 것이 더 안정적인 결과를 보여주는 것을 알 수 있다.

# 결론
- IMU only odometry 는 외부 도움 없이는 금방 발산한다.
- Python 만 이용해서 간단하게 Loosely-coupled LiDAR + IMU 기반 Odometry system을 구현해보았다.
- LiDAR가 rough 하게만 쓰여도 (i.e., 0.5m resolution, 0.5sec 마다 한 번만 보정), IMU state 를 잘 보정할 수 있음을 실험적으로 알아보았다.
- 이렇게 PVA가 잘 보정된 IMU는 그 다음턴의 LiDAR registration 을 위한 좋은 initial guess 를 생성해주고, 결과적으로 선순환이 반복된다.
