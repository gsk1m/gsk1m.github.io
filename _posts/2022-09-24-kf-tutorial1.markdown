---
layout: post
title:  "🌈 Error-state Kalman Filter 이야기"
date:   2022-09-23 23:00:00 +0000
categories: SLAM
---

# Error-state Kalman Filter 란?
- 최근 SLAM 학/업계에서는 visual은 [OpenVINS](https://github.com/rpng/open_vins), lidar는 [Fast-LIO](https://github.com/hku-mars/FAST_LIO) 가 대세가 된 듯하다.
  - 이 둘은 모두 __`on-manifold EKF`__ 혹은 __`error-state Kalman filter`__ 라고 불리는 방식을 사용하고 있다.
- 그래서 Error-state Kalman Filter (ESKF) 에 대해 공부해보았다.
- 최대한 쉽게 큰그림 위주로 이해해보자.

## 큰 그림
- 일단 ESKF에 대해 이야기 하려면
  1. EKF (Extended Kalman Filter)를 알아야 하고
  2. 그 전에 KF (Kalman Filter)를 알아야 한다.
  3. 그리고 GN (Gauss-Newton) Opt 에 대해서도 이해해야 한다.
- 뭔가 많아 보이지만, 큰 그림에서 수학없이 이야기해보는 것이 이 포스트의 목적이다.

### KF 톺아보기
- KF 에 대한 (수학적) 설명을 여기서 늘어놓을 생각은 없다..
  - KF를 약간 무식하게 한줄 요약하자면 1. 저질러보고, 2. 수정하기, 라고 할 수 있다.
       - 예를 들어 현재 벽으로부터 내가 1m 떨어져서 서있음을 안다. 그리고 내 보폭 한 칸은 50cm 정도 되고 내 팔길이는 50cm 라고 하자. 내가 앞으로 한칸 움직이면 나는 벽으로부터 얼마나 떨어져있을까?
            1. 저질러보기: 일단 움직이자! 50cm 라고 치자. 그러면 나는 벽으로부터 100-50 = 50 cm 만큼 떨어져있겠지?
                - 하지만 문제는 보폭이 항상 정밀하게 딱 50cm 일리는 만무하다.
            2. 수정하기: 그래서 팔을 한번 뻗어보았다. 근데 팔을 끝까지 쭉 뻗었는데도 공간이 조금 남네. 그러면 50cm 보다는 일단 덜 간 건 알겠다. 그럼 아까 50cm 떨어져있다고 저질렀던 예측값을 조금 수정해주면 되겠다.
       - 근데 팔길이로 길이를 대충 재는 것도 엄청 정밀하지는 않은데.
          - 나는 벽으로부터 60cm 떨어져있다고 해야 하나..? 55cm 떨어져있다고 해야 하나..? 보폭과 팔길이기반측정 의 결과를 서로 다른 신뢰도에 기반해서 어떻게 적절히 잘 융합할 수 없을까? 그것에 대한 수학적인 기계적 과정을 제공하는 것이 KF라고 할 수 있다.   
  - 이에 대한 공부자료는 [An Introduction to the Kalman Filter (2006 ver)](https://www.cs.unc.edu/~welch/media/pdf/kalman_intro.pdf) 를 추천한다.
      - KF 공부자료야 워낙 많지만... 이게 가장 분량도 적절한 듯하다 (너무 생략되어있거나 너무 쓸데없이 입문자에게 불필요하게 많지않다).
      - 95년에 처음 나온 report 인데 현재 (2022.09) 무려 10000회 인용이 넘었다. 이 report 는 되게 간결한 편이며 더욱 자세한 유도 등은 여기에 reference 들이 잘 추천되어 있으므로 (e.g., Maybeck79) 추가적으로 더 공부하고 싶으면 참고하면 좋다.
- 요약하자면 KF는 prior (저질러 본 것)와 measurement (재 본 것) 사이의 weighted sum (두 결과 토대로 수정한 것)을 해주는 장치이다.
    - 이 때 고정된 상수로 융합하는 게 아니라, __`a posteriori 의 covariance 를 minimize 하는 것을 cost`__ 로 하여 최적 gain 을 결정하는 방법이다 (따라서 Kalman gain 은 `blending factor`라고도 불린다).
    - 암튼 핵심은 KF는 방금 설명했듯 `a posteriori 의 covariance 를 minimize` 하는 것을 목표로 하기 때문에, `optimal estimator` 라고 불린다.
      - 혹은 [위키를 보면](https://en.wikipedia.org/wiki/Kalman_filter), KF는 `linear quadratic estimation (LQE)` 라고도 불린다고 나와있다.
          - covariance 는 그 정의상 random variable error vector의 제곱꼴이기 때문이다.
- 잡썰  
  - ps1. Phil's Lab 의 유튜브 영상도 KF에 대해 정말 이해하기 쉽게 잘 설명해준다. 추천! ([1편](https://www.youtube.com/watch?v=RZd6XDx5VXo), [2편](https://www.youtube.com/watch?v=BUW2OdAtzBw), [3편](https://www.youtube.com/watch?v=hQUkiC5o0JI), [4편](https://youtu.be/7HVPjkWOrLE))
      - 2편에서 상수 weight 비율로 fusion 하는 (e.g,, 0.5 and 0.5) complementary filter 를 먼저 보여주고 EKF로 넘어가는 스토리가 아주 일품이다. 그리고 실제로 IMU 센서 데이터를 통해 결과를 보여주기 때문에 KF 가 현실세계와 동떨어진 무언가로 느껴지지 않는다.
  - ps2. KF에서 prior 라는 용어가 꼭 매우 최초의 어떤 정보만을 의미하지는 않는다. KF는 Bayesian filtering 이며 Bayesian filtering 의 철학은 현재의 최적값이, 다음 턴의 사전정보가 된다, 이기 때문이다. 즉 현재의 posterior 가 다음 턴의 prior 로써 역할하는 것.
    - 그나저나 Bayesian filtering 이야기가 나온 김에... KF는 원래 상당히 최적화 스러운 (== least square optimization 스러운) 느낌으로부터 유도 되었지만 (방금 말했듯 cov를 최소화 하도록), 그 nature 를 들여다보면 Probabilistic한 면이 있다. 이 이야기가 `The Probabilistic Origins of the Filter` 라면서 위에서 추천한 자료에도 나온다. 또는 Särkkä, Simo. Bayesian filtering and smoothing. No. 3. Cambridge University Press, 2013. 이 책의 앞부분을 참고 ([요약 1편](https://gisbi-kim.github.io/blog/2021/03/09/bayesfiltering-1.html), [요약 2편](https://gisbi-kim.github.io/blog/2021/03/09/bayesfiltering-2.html)).

### EKF 톺아보기
- 이제 우리는 KF에 대해 알았다. 그럼 EKF로 넘어가보자.
  - (E-, ES-) KF류를 구성하기 위해서는 가장 먼저 system model이 정의되어야 한다.   
      - 1. process model 과 2. measurement model 이 있다.
  - 앞서 공부자료 [An Introduction to the Kalman Filter (2006 ver)](https://www.cs.unc.edu/~welch/media/pdf/kalman_intro.pdf) 의 식 1.1, 1.2 에서처럼 이 모델들이, 우리가 알고싶은 state 나 measurement vector 와 그저 matrix 곱으로 이루어져 있으면 linear 하다라고 한다.  
  - 그런데 현실세계에서는 다양한 이유들로 인해 nonlinearity가 생긴다.
      - state 가 nonlinear 할 수 있다 (e.g., rotation 을 추정하고 싶은 경우)
      - 혹은 measurement model 이 nonlinear 할 수 있다 (e.g., 제곱, exponential 등등이 들어가는 복잡한 형태)
  - 그래서 이런 nonlinear 한 process model $f(\cdot)$ or observation model $h(\cdot)$ 을 테일러 급수로 편 다음에 1차 근사 (== linear term만 남김) 해서 쓰겠다는게 EKF이다.

### ESKF 톺아보기 ... 전에
- 그럼 EKF와 ESKF는 무엇이 다른가?
- 이 이야기를 하기 전에 우리는 잠시 KF 라는 것 자체로부터 멀어질 필요가 있다.
- 즉 ESKF 와 EKF는 이렇게 달라, 하고 그 물질적인 면을 보고 외우는 것보다, 무엇보다 ESKF가 왜 필요하게 되었는지를 살펴보자는 것이다.
  - 키워드 힌트: EKF on manifold
    - 즉, manifold 에서 EKF를 하려면 뭔가 좀 특별하게 care 해줘야 할 게 있다는 것인데. 이게 뭔소린가 하니 차차 알아보자.
- 앞서 KF도 optimal estimator 라고 했다. 즉 covariance 라는 squared loss 를 최소화 하는 최적화기기 인 것이다.
  - 즉 이부분에서 KF는 least square optimization 의 대표주자인 Gauss-Netwon optimization (GN) 을 닮았다.
      - GN (or can be LM, just damping 이 추가된 GN)이 변화량이 어느정도보다 작아질 때까지 계속 iteration 을 반복해서 $\delta \textbf{x}^*$ 를 찾아가는 것이라면 (for details, see the post: [Nonlinear ICP 밑바닥부터 구현해보기](https://gsk1m.github.io/slam/2022/07/09/symforce_icp.html)),
      - KF는 최적의 weight 비율을 한방에 딱 찾아내서 딱 한번만 update 해주는 GN optimizer 라고 생각해볼 수 있다는 것이다 (완전히 같다는 게 아니라, 그 느낌만 비교하자..).
        - ps. 물론 KF에서도, 한번의 propagate 에 대해 여러번의 (수렴할 때까지) update 를 해주는 방식으로 iterative 하게 구성할 수 있긴하다 ([예시: se3_localization_iekf](https://github.com/artivis/manif/blob/devel/examples/se3_localization_iekf.cpp))
  - 그래서 GN 이야기를 좀 더 해보자.

### GN 톺아보기
- GN에서 개념 자체는 너무 간단하다.
  - 기본적인 개념에 대해서는 늘 추천하는 [Grisetti 교수님의 ICRA tutorial 자료](http://www.diag.uniroma1.it//~labrococo/tutorial_icra_2016/icra16_slam_tutorial_grisetti.pdf)를 보고 오기를 추천... 하고 여기서는 GN 에 대해서는 설명하지 않기로 하고.
  - 결과만 이야기하자면 GN이란 그저 $J^{T}J \delta \textbf{x} = -J^{T}e$ 를 푸는게 전부이다.
      - 이걸 풀어서 나온 $\delta \textbf{x}^{*}$ 를 기존 해 $\textbf{x}$ 에 더해주면 된다. 그리고 $\delta \textbf{x}$ 가 충분히 작다면 수렴한 것으로 보고 iteration 을 종료하면 된다. 이걸 from scratch (밑바닥부터)로 구현해본 것이 the previous post: [Robust Optimization Tutorial](https://gsk1m.github.io/slam/2022/09/01/robust-opt-tutorial1.html).
  - 그런데 이부분은 정말 기계적인 부분이고. 중요한 지점은 저 $J$ 란게 뭐냐, 하는 것이다.

- TBA
