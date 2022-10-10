---
layout: post
title:  "🌈 Nerf 실습 (Feat. Nerfstudio)"
date:   2022-10-09 23:00:00 +0000
categories: SLAM
---

# Nerfstudio 를 이용해서 직접 뉴럴 렌더링을 해보자
- Nerf로 대표되는 뉴럴렌더링이 요새 유행이다 (Summaries: [ECCV 22](https://markboss.me/post/nerf_at_eccv22/), [CVPR 22](https://dellaert.github.io/NeRF22/), [ICCV 21](https://dellaert.github.io/NeRF21/)).
- 최근 [nerfstudio](https://docs.nerf.studio/en/latest/) 라는 오픈소스+툴이 나왔다.
- 직접 해보자!

## 뉴럴 렌더링이란
- **뉴럴 렌더링**이란
  - [Advances in Neural Rendering (EUROGRAPHICS 2022)](https://arxiv.org/abs/2111.05849) 에서 설명한 말을 옮겨오자면    
    - 실세계 관측으로부터, 새로운 실감나는 이미지 (synthesizing a novel photo-realistic view) 를 생성해내는 과정 및
    - 이를 위해 컴퓨터 그래픽스와 머신러닝을 결합하는 작업이라고 한다.
- 이론은 (나도 잘 모른다..) 여기서는 그만 알아보도록 하고 ..

## (두괄식) 요약
- (Nerf 계열 한정) <U>practical 하게 요약</U>하자면
  1. **Input**: discrete 한 images 들의 poses 를 알고 있으면
  2. **Output**: 임의의 coordinate 에서 그 scene 을 바라본 view 를 얻을 수 있다.
- 바로 실전 예시를 통해 알아보자.

### Input
- 예를 들어, 아래 이미지들은 Galaxy S22+ 로 대충 찍어온 비디오(mp4) 에서 3hz로 추출한 사진들(png) 이다.
  - ps. 가을이 되면 카이스트 옆 유성구청 앞에서는 국화축제를 한다. 예쁘게 잘 꾸며놓는다.

<p align="center">
  <iframe width="366" height="651" src="https://www.youtube.com/embed/J9-yWZRUvtY" title="ggumdori raw data for nerfstudio" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>    

### Output
- 단순히 위의 (not continuous한) 이미지 외에 아무런 사전정보도 주어지지 않았음에도,
- 임의의 뷰포인트에서 포토리얼리스틱 한 이미지를 생성할 수 있다면,
- 우리는 최종적으로 아래와 같은 smooth and continuous 한 video 를 얻을 수 있다. 이 때 이 video 의 trajectory를 사용자가 임의로 설정할 수 있다.
  <p align="center">
    <iframe width="1020" height="570" src="https://www.youtube.com/embed/YeEscls0DGA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </p>    
- 이 때 "임의"의 뷰포인트에서 그 씬을 본 사실적인 뷰를 얻을 수 있으므로, 위의 비디오 외에 아래와 같이 또 다른 느낌의 비디오를 만들어볼 수도 있겠다.
  - 꿈돌이가 로켓을 타고 이륙하는 느낌을 좀 더 살려보았다.
  <p align="center">
    <iframe width="488" height="488" src="https://www.youtube.com/embed/ylgb1Vrf_Ec" title="ggumdori launch" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </p>    
- ps. 위의 novel view 에서 blurry 하거나 white noise 같은 부분들은 실제 관측치가 부족했던 부분이다 (예시: 꿈돌이 뒷편을 보는 이미지가 없었음). 따라서 더 많은 이미지가 있었다면 더 사실적인 뷰를 생성할 수 있을 것이다.


## Nerfstudio 사용법
- 이제 위의 결과를 어떻게 (이론 몰라도) 얻을 수 있었는지 알아보자.
- 막 최근(Oct, 2022)에 나온 [Nerfstudio](https://docs.nerf.studio/en/latest/) 를 이용해보았다.
  - CLI (터미널에서 커맨드 몇번 만으로) 로 nerf 를 수행하는 툴(+라이브러리)이다.
  - 웹앱으로 뷰어도 제공하고 있어, 사용자가 **코딩없이** 뉴럴렌더링 결과물을 얻을 수 있는 ready-to-use product 여서 좋았다.

### 단계 1: Pose 얻기 (command: ns-process-data)
- Nerf 계열의 뉴럴렌더링은 image 들의 pose 가 주어져있을 때, novel view 를 synthesize 한다.
- 따라서 먼저, unordered images 들의 pose 를 얻어야 한다. 이미지들의 poses 를 알기 위해서는 [colmap](https://colmap.github.io/) 이라는 software 를 이용하면 된다.
  - 이 COLMAP 부분 역시 nerfstudio 내부에 통합되어 있다. 사용자 입장에서 이부분이 매우 편했다.
  - 그래서 [여기](https://docs.nerf.studio/en/latest/quickstart/custom_dataset.html#nerfstudio-dataset) 에 나와있는대로 `ns-process-data` 라는 커맨드 한 줄 만으로도 이미지들의 poses 를 얻을 수 있다. 이렇게 얻어진 결과물의 format 은 바로 nerfstudio 의 뒷 단계에 쓰일 수 있는 compatible 한 format 이므로, `ns-process-data` 를 이용해서 colmap을 수행하기를 추천한다.
  - 그러면 아래와 같은 결과가 생성된다.
      ```ruby
      # {PROCESSED_DATA_DIR} (i.e., colmap output directory)
      ├── colmap
      │   ├── database.db
      │   └── sparse
      │       └── 0
      │           ├── cameras.bin
      │           ├── images.bin
      │           ├── points3D.bin
      │           └── project.ini
      ├── images
      │   ├── frame_00001.png
      │   ├── frame_00002.png
      │   ├── ...
      ├── images_2
      │   ├── frame_00001.png
      │   ├── frame_00002.png
      │   ├── ...
      ├── images_4
      │   ├── frame_00001.png
      │   ├── frame_00002.png
      │   ├── ...
      ├── images_8
      │   ├── frame_00001.png
      │   ├── frame_00002.png
      │   ├── ...
      └── transforms.json
      ```
- ps. COLMAP software 를 별도로 사용하게 되면 ..
  - 크게 아래 3 단계로 이루어진다.
    1. feature extraction
    2. feature matching
    3. bundle adjustment
  - 그러면 아래와 같이 sparse 한 scene structure 와 위 이미지들의 pose (position+orientation) 를 얻을 수 있다.
      <p align="center">
           <img src="/assets/data/2022-10-09-nerfstudio-example1/colmap.png" width="950"/>
      </p>
      - 여기서 빨강 view 들이 poses 이고, 분홍색 lines 는 covisible feature 를 가진 views 들 사이의 관계를 보여준다.
  - ps. 위의 결과를 얻는데 역시 코딩 1도 하지 않으니 비전공자라고 겁먹을 필요는 없다. GUI에서 클릭 몇번이면 끝난다.
    - 그나저나 nerfstudio 내부에 colmap data 를 생성하는 부분이 command line 한줄 (`ns-process-data`) 로 지원되고 있으므로 colmap software gui 를 켤 일 자체가 없기도 하다.
  - ps2. 위의 결과를 보면 reconstructed 된 scene이 매우 sparse 함을 알 수 있다.
    - MVS + meshing 을 거쳐서 위의 map을 더 denser 하게 만들 수 있겠으나, 보통 여전히 photo-realistic 함과는 거리가 멀다.
      - 그래서 (이 한계를 해소할 수 있어서) 뉴럴 렌더링이 각광받고 있는듯하다. 물론 pose가 주어져야 하지만 이는 기존 traditional geometric methods (e.g., colmap's sparse reconstruction and pose estimation) 가 충분히 잘 하는 일이므로 상호보완적이고 시너지가 잘 맞다!

### 단계 2: Nerf model 학습 시키기
- 역시 커맨드라인 한줄로 이루어진다
- 예를 들어,
  ```sh
    $ ns-train nerfacto --data {PROCESSED_DATA_DIR}
  ```
  - 이는 nerfacto 라는 모델(방법론)을, 앞서 생성한 image 와 pose 를 이용해서, 학습시겠다는 것을 의미한다.
    - nerfacto 는 '사실상의 표준'을 의미하는 디팩토와 nerf 를 결합한 워딩같다. mip-nerf 와 instant-ngp 의 장점을 섞었다고 하며 nerfstudio 제작팀이 제안하는 방법인 듯하다.
  - 그러면 자동으로 `outputs/{PROCESSED_DATA_DIR}/nerfacto/{TIME}`의 디렉토리가 생기고 아래에 다음과 같은 log 파일들과 weight 가 저장된다.
    ```ruby
    .
    ├── camera_path.json
    ├── config.yml
    ├── nerfstudio_models
    │   └── step-000019788.ckpt # saved when after 19788 iterations
    └── viewer_log_filename.txt
    ```
    - config.yml 에는 여러 세팅들이 담겨있으며, command line 에서 함께 전달할 수 있다.
      - 예를 들어, 예전에 학습시키던 웨이트로부터 시작하고 싶다면 아래와 같이 해주면 되겠다.
    ```sh
      $ ns-train nerfacto --data {PROCESSED_DATA_DIR} --trainer.load_dir {YOUR_WEIGHT_SAVED_DIR}
    ```
- 그러면 학습이 시작되고, 터미널에서 webapp URI를 알려준다.
  - 예를 들어 `https://viewer.nerf.studio/versions/22-10-07-0/?websocket_port=7007`
  - 들어가면 아래와 같이 실시간으로 학습되는 품질 (저해상도 한정) 을 볼 수 있다.
    <p align="center">
         <img src="/assets/data/2022-10-09-nerfstudio-example1/webapp1.png" width="950"/>
    </p>
  - 그래서 학습이 진행됨에 따라 어느정도 품질에까지 도달했는지를 대충 파악해볼 수 있다.
    - 위의 output 에서 소개한 비디오들은 15000 iterations 정도를 학습한 웨이트에서 얻은 결과이다.
      - 1080ti 기준 + 위의 꿈돌이 데이터셋 기준 10 iteration 에 1초정도 소요되었다.

### 단계 3: trajectory planning 및 비디오 제작
- 웹앱에서 또한 trajectory 도 planning 할 수 있다. 자세한건 [이 튜토리얼 영상을 참고](https://www.youtube.com/watch?v=nSFsugarWzk).
  - 그러면 synthesize 하고 싶은 set of images 들의 trajectory 가 셋업되는데, 이 역시 커맨드를 바로 복사할 수 있도록 해줘서 매우 편리하다. 예를 들어, 다른 터미널을 켜고 `ns-render --load-config outputs/3hz_ns/nerfacto/2022-10-10_122640/config.yml --traj filename --camera-path-filename outputs/3hz_ns/nerfacto/2022-10-10_122640/camera_path.json --output-path renders/output.mp4` 이런식으로 해주면 비디오가 그저 슥슥 생성된다.
- 그 결과 최종적으로 앞서 보인 비디오 같은 결과를 얻을 수 있었다 :)
  - 이 때 웹앱에서 해상도 역시 조절할 수 있다.       
    - 1080 x 1920 으로 제작할 때 메모리를 거의 10G 먹고, 생성속도는 15-20초에 한장 수준이었다 (1080ti 기준).
    - 600x600 에서는 메모리 6-7G 정도, 생성속도는 4-5초에 한장 수준이었다.
    - 느리긴 하다.

## 결론
- Nerfstudio 를 이용해서 코딩없이+이론 몰라도 nerf 를 체험해볼 수 있었다!

### TODO
- mip-nerf 와 instant-ngp 를 공부해보자.  
