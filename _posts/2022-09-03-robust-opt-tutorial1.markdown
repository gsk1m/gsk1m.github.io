---
layout: post
title:  "🌈 [SymForce Tutorial 4편] Robust Optimization Tutorial"
date:   2022-09-01 03:00:00 +0000
categories: SLAM 
---

# Robust Optimization 이란? 
- 현실 세계에서는 다양한 이유로 인해 False Correspondences 가 존재한다. 
- 따라서 이런 잘못된 constraint 로 인해, 전체 해(solution)가 망가질 수 있다. 
- 이런 상황에서도 강건하게 최적화를 수행할 수 있는 방법에 대해 알아보고 실습해보자. 


## Robust Optimization의 필요성
### Recap 
- 지난 포스트 ([Sim(3) ICP]({{ site.baseurl }}{% post_url 2022-07-23-sim3-tutorial %}), [Pose-graph Optimization]({{ site.baseurl }}{% post_url 2022-07-31-pgo-toy %}))에 이어 계속해서, Robotics 에서의 Nonlinear Optimization 을 풀어보는 실습을 해보자. 
    - _[SymForce](https://symforce.org/)_ 를 이용해서!
    - ps. Robotics 에서 `어떤` (Non)-linear problem 들을 Least square optimization 으로 `어떻게` 푸는지에 관해서는 [Grisetti 교수님](https://scholar.google.com/citations?hl=ko&user=yD-SFG4AAAAJ&view_op=list_works&sortby=pubdate)의 [Least squares optimization: From theory to practice](https://arxiv.org/abs/2002.11051) 논문을 읽어보기를 추천함.

### 이상 vs 현실 
- 그런데 앞의 예제들에서는 <a href="/slam/2022/07/23/sim3-tutorial.html#setting"> **True correspondence** </a> 를 가정했었다. 
- 하지만 현실 세계에서는, 센서 노이즈, 유사한 장소, 알고리즘의 한계 등 다양한 이유로 인해 False correspondence가 생기는 것을 피할 수 없다. 
- 예를 들면 아래 그림과 같다. (Full 튜토리얼 영상은 [여기서](https://youtu.be/p54avTphKFw) 볼 수 있다)
    - [앞선 포스트]({{ site.baseurl }}{% post_url 2022-07-23-sim3-tutorial %}) 와 동일한 Sim(3) Registration 문제이다. 
    <p align="center">
         <img src="/assets/data/2022-09-03-robust/main.png" width="1000"/>
    </p>

    - 여기서 제일 왼쪽 그림에서, 초록색 선은 True correspondence 를 의미하고, 빨강색 pair lines 는 False correspondences 를 의미한다. 
        - 즉, 초록 pair 는 실제로 의미적으로 동일한 부분이며, 빨강 pair는 실제로 다른 부분이기 때문에 이 두 부분이 같아져서는 안된다. 
    - 하지만 Solver 입장에서는 어떤 pair 가 true 이고 false 인지 알 수 없기 때문에, 이들 모든 constraints 를 동시에 만족하려고 하다보면, solution이 망가진다. 중앙의 그림은 그런 예시이다. 
    - 반면, 이 Robust optimization 을 통하면 오른쪽 그림처럼, 회색 용이 하늘색 용의 수준까지 복구가 된 것을 알 수 있다. 
    - 이제부터 어떻게 하는지 간단 이론 + 실습 을 통해 알아보자!
    
## Robust optimization 을 위한 접근 두 종류 



