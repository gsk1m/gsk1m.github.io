---
layout: post
title:  "🌈 직관적으로 Lie theory 수식 이해해보기"
date:   2023-12-03 23:00:00 +0000
categories: SLAM
---

<!-- publishing data is future means no publishing, and wip backup -->

# 대학교 1학년 수준으로 설명해보기
- [IMU Preintegration on Manifold](http://www.roboticsproceedings.org/rss11/p06.pdf) 논문의 II.Preliminaries 에 나오는 수식을 직관적으로 이해해보자.
- 느낌적인 느낌을 알면 쉽다.


## 이 글을 쓰는 이유
- SLAM 공부를 하다보면 유명한 논문 [IMU Preintegration on Manifold for Efficient Visual-Inertial Maximum-a-Posteriori Estimation](http://www.roboticsproceedings.org/rss11/p06.pdf) 을 마주하게 된다. 여기에 보면 section II부터 (SLAM 입문자 입장에서는) 갑자기 처음보는 수식들이 별다른 설명없이 몰아치는데 ...
- 그것에 대해서 조금 더 친절하게 설명하고 있는 문서가 [Quaternion kinematics for the error-state Kalman filter](https://arxiv.org/abs/1711.02508)다.
- 위 문서는 조금은 길기 때문에 (90쪽 정도), 읽기 전에 큰 그림으로 정리하고 넘어가면 좋을 이야기들을 (수식없이, 혹은 대학교 1학년 수준으로) 해보고자 한다.
- 최대한 수학없이 맥락을 이야기해보자. [앞의 블로그 글]({{ site.baseurl }}{% post_url 2022-09-24-kf-tutorial10 %}) 에서도 이야기한 바 있으나, 여기서는 Lie theory 수식들 위주로 좀 더 직관적으로 설명해보고자 한다.
  - 테일러 익스팬션은 알아야 한다.

## Lie theory 의 필요성

### SLAM은 iterative optimization 이다
- SLAM or state estimation 은 수학적으로 optimization 이다.
- 우리가 알고싶은 a set of quantity (e.g., a robot position, a robot rotation, a landmark position, sensor bias, etc) 들을 a single vector 에 모두 집어넣고 (e.g., n-dim vector), 이 vector $\textbf{x}$ 의 값을 최적화한다.
- state vector 에 포함된 quantity 들 사이의 관계 (constraint, 제약) 들을 모으면, 수학적으로 optimization 이란 $A\textbf{x} = \textbf{b}$ 와 같은 연립방정식 (linear system) 을 푸는 것이다.
- 그런데 어떤 quantity 들은 nonlinear 하고, 혹은 어떤 제약식이 nonlinear 할수도 있으므로, 위의 제약 $A$ 는 linearization 을 거친 근사치이다.
  - 뒤에서 이야기하겠지만 Rotation이 바로 nonlinear 하다. Rotation 을 잘 다루기 위해서 Lie theory 이야기를 하는 것.
- 이 때 linearization 을 하는 $\textbf{x}$ 의 위치(값) 에 따라 $A$ 의 값이 바뀔 수 있으므로, 실제와의 차이가 커질 수 있게 된다. 이 경우 linearization error 로 인해 $\textbf{x}$ 의 최적해는 실제 해(solution)과 멀어질 수 있다.
- 따라서 실제로 engineering 에서는 $\textbf{x}$의 최적값을 구하는 것이 아니라 $\delta\textbf{x}$의 최적값을 구하는 접근법을 택한다. 이  $\delta\textbf{x}$의 최적값을 직전 $\textbf{x}$에 더해주는 식으로 $\textbf{x}$의 최적값을 결정해나간다. 그리고 이  $\delta\textbf{x}$ 가 어느 정도 변화가 없게 되면 수렴한 것으로 보고 $\textbf{x}$ 의 최종 해를 확정한다.
- 이것이 [Gauss-newton optimization 이고, 혹은 iterative error-state kalman filter](https://faculty.cc.gatech.edu/~bboots3/STR-Spring2018/readings/IKF_GaussNewton.pdf) 의 핵심 컨셉이다. 그래서 SLAM을 iterative optimization 이라고 부를 수 있겠다.



### 그런데
- 그런데 iterative optimization 을 위해서는 미분값이 필요하다.
  - Gauss-Newton optimization 의 아주 기본적인 이야기이므로 여기서는 자세한 설명은 생략한다. The iterative GN opt에 관한 자세한 설명및코드는 [앞의 블로그 글]({{ site.baseurl }}{% post_url 2022-07-10-symforce_icp%}#iterative-update) 을 참고.
- 그런데 미분이라 함은 Euclidean spaces 위에서만 이루어질 수 있다 (e.g., $\mathbb{R}^{n}$).
  - 수학적으로 엄밀하게 까지는 잘 모르겠고... 적어도 엔지니어링에서 코드로 구현함에 있어서.
