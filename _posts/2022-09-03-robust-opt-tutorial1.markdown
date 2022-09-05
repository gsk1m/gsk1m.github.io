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
- 이상: 그런데 앞의 예제들에서는 <a href="/slam/2022/07/22/sim3-tutorial.html#setting"> **True correspondence** </a> 를 가정했었다. 
- 현실: 하지만 현실 세계에서는, 센서 노이즈, 유사한 장소, 알고리즘의 한계 등 다양한 이유로 인해 False correspondence가 생기는 것을 피할 수 없다. 
    - 예를 들면 아래 그림과 같은 상황이 발생할 수 있다. (Full 튜토리얼 영상은 [여기서](https://youtu.be/p54avTphKFw) 볼 수 있다)
        - [앞선 포스트]({{ site.baseurl }}{% post_url 2022-07-23-sim3-tutorial %}) 와 동일한 Sim(3) Registration 문제이다. 
        <p align="center">
             <img src="/assets/data/2022-09-03-robust/main.png" width="1000"/>
        </p>

        1. 여기서 제일 왼쪽 그림에서, 초록색 선은 True correspondence 를 의미하고, 빨강색 pair lines 는 False correspondences 를 의미한다. 
            - 즉, 초록 pair 는 실제로 의미적으로 동일한 부분이며, 빨강 pair는 실제로 다른 부분이기 때문에 이 두 부분이 같아져서는 안된다. 
        2. 하지만 Solver 입장에서는 어떤 pair 가 true 이고 false 인지 알 수 없기 때문에, 이들 모든 constraints 를 동시에 만족하려고 하다보면, solution이 망가진다. 중앙의 그림은 그런 예시이다. 
            - ps. 여기서는 과장해서 절반 (50%) 이 outlier 인 상황을 가정했지만, 본 튜토리얼에서 설명하는 robust kernel 을 적용하지 않으면, 단 5%의 outlier 만 있더라도 해가 수렴하지 않을 수 있다 (뒤에서 실습에서 알아볼 예정).
                - ps2. 사실 실제상황에서 50% 까지는 _low_ outlier 라고 여겨지는 듯.
                    - Robotics 에서 Robust optimization 연구를 많이 하는 MIT의 Luca Carlone 교수님의 최근 논문 Estimation Contracts for Outlier-Robust Geometric Perception 을 보면 이런 말이 나온다... (이 교수님은 사전에 outlier 의 비율이 알려져있지 않을 때에도, 그리고 99%의 outlier 가 있을 때에도 어떻게 강건하게 최적화를 할 것인가.. 이런 연구들을 하신다) 
                    ```
                        For the case with low-outlier rates (i.e., β << 0.5), ...
                    ```
        3. 암튼 한편, Robust optimization 을 통하면 오른쪽 그림처럼, 회색 용이 하늘색 용의 수준까지 올바르게 정합이 된 것을 알 수 있다. 
- 이제부터 (이런 outlier correspondences 가 있을 때에도) 어떻게 안전하게 최적화 하는지 간단 이론 + 실습 을 통해 알아보자!
    
# Robust optimization 을 위한 접근 두 종류 
- 실습에 앞서, 먼저 간단하게 필요한 개념에 대해 알아보자. 

## Least square optimization 
- Least square optimization 문제의 원형은 궁극적으로 모두 아래와 같은 모양을 따른다. 
    <p align="center">
         <img src="/assets/data/2022-09-03-robust/prob.png" width="300"/>
    </p>
    - 어떤 모델 (cost function == observation model과 measurement 의 차이) $\text{x}$ 가 있고, 우리는 이것들의 총 합을 최소화 하는 parameter $\theta$ 의 최적값을 알고싶다. 
        - ps. 이 때 보통 robotics 에서의 cost function 혹은 observation model 들은 nonlinear 한 경우가 많아서, 저 $\text{x}$ 의 미분인 자코비안을 계산해야 하는데 이부분을 SymForce 가 알아서 잘 해준다... 에 대한 내용을 지난 튜토리얼들에서 계속 강조하였었다 (그래서 이번에는 그 설명은 넘어가도록 하고).
    - 이 때 저 index 변수 $i$라는 것은, 예를 들어, 위의 예시 point cloud 그림에서 용은 1000개의 포인트로 구성되어 있고, 이 때 1000개의 correspondence 가 존재한다면, 이것들의 index를 의미한다. 그 중 어떤 i에 대해서는 true correspondence 이고, 그 중 다른 i에 대해서는 false correspondence 가 섞여 있는 것이다. 
- 우리의 목표는 false correspondence 의 영향력을 없애는 것이다. 어떻게 할 수 있을까? 
    1. 먼저, 누가 false correspondence 인지 확실하게 **찾아서 제거**하는 방법이 있을 수 있다. 이른바 explicit removal 이라고 부를 수 있겠다.
    2. 그런데 그것이 만약 어렵다면, false correspondence '일 것 같은' term 앞에 **아주 작은 weight** 를 곱해주면 되겠다. 이른바 deweighting 방법이라고 할 수 있겠다.
    
- 이 두 부류에 관한 설명은 [Scale-Variant Robust Kernel Optimization for Non-linear Least Squares Problems 논문](https://arxiv.org/abs/2206.10305)의 1쪽에 잘 설명되어 있다 (1쪽만 읽어도 충분함). 
    - 여기서도 간단하게 정리를 해보자.


## 1. Explicit Removal 
- `RANSAC`으로 대표되는 계열.
    - 이 부류에 해당하는 Robotics applications 에서의 (2022년 8월까지의) 논문들 역시 방금 소개한 논문 [Scale-Variant Robust Kernel Optimization for Non-linear Least Squares Problems 논문](https://arxiv.org/abs/2206.10305) 에 잘 정리되어있으니 참고. 
    - 그래서 (+RANSAC은 너무 유명하니) 이 포스트에서는 자세한 설명은 생략한다. 
    - 굳이 Robotics에서의 최근 논문 중 하나만 꼽자면 2018 ICRA Pairwise consistent measurement set maximization for robust multirobot map merging 논문을 추천. 
        - ps. 여기 나오는 `maximal clique` 라는 용어에 대해서도 알아두면 좋기 때문. max clique inlier selection (MCIS) 라고 해서 TEASER++ (20 TRO Teaser: Fast and certifiable point cloud registration), [Quatro](https://github.com/url-kaist/quatro) (22 ICRA A Single Correspondence Is Enough: #Robust Global Registration to Avoid Degeneracy in Urban Environments) 등 최근 논문들에서 많이 등장하는 개념이다. 

## 2. (Implicit) Deweighting 
- `M-estimator`라고 자주 불리는 계열.
    - 앞서 소개한 consensus 기반 outlier rejection 기법들은 효과는 좋지만, 시간이 오래걸린다. 
    - 그리고 현실 세계에서는 어떤 pair 가 false correspondence 인지 판별하는 작업이 여전히 어려울 수 있기 때문에, 앞의 explicit removal 과정을 거쳤더라도, 여전히 false correspondence 가 100% 제거되었음을 기대하기는 어려울 수 있다. 
    - 그래서 최후의 방어라인?!이 필요하다. 
    - 그 역할을 하는 것이 M-estimator 라고도 불리는 계열이며, '[robust loss function (e.g., see Ceres)](http://ceres-solver.org/nnls_modeling.html#lossfunction)', 'robust kernel 을 씌운다' 등등으로 부르기도 한다. 
- 요약
    - False correspondence '일 것 같은' term 앞에 작은 weight 값이 곱해지도록 하자는 것이 핵심이다. 
    - 그런데 어떻게 하냐면:
        - 위의 least square optimization 은 사실 iterative 하게 푸는 것이다. 즉, initial estimate 이 주어져있을 때 어디로 얼마만큼 이동해야 최적해에 가까워질지 "delta" 를 최적화하는 것. 이 부분에 대해서는 늘  <a href="http://127.0.0.1:4000/slam/2022/07/10/symforce_icp.html#grisetti-tuto"> Grisetti 교수님의 tutorial 자료</a> 를 참고하라고 이야기 하는 중.
        - 따라서 initial value 가 적절히 좋다면 (적절히 좋아야만 문제를 풀 수 있다. 우리의 observation model들이 대체로 nonlinear 하기 때문에), false correspondence 의 cost 값은 클 것이라고 예측해볼 수 있다. 
        - 그럼 error 값에 따라 weight 를 결정해주면 되겠다! 라고 생각해볼 수 있겠다. 그런데 그것을 if문으로 도배하는 것이 아니라. 아주 자연스럽게. 어떤 함수 $\rho(\cdot)$ 를 기존 cost function 에 씌워주기만 하면 된다. 이 함수를 kernel 이라고도 부른다.  
    - 자세한 건 아래 슬라이드 참고. 
- 설명 (slides from [SNU talk](https://docs.google.com/presentation/d/1Z1FjnphgP60jTIa6da8vwhaAEzULefLrPQsJ4yBHAJA/edit?usp=sharing))
    <p align="center">
         <img src="/assets/data/2022-09-03-robust/weight1.png" width="1000"/>
    </p>
    <p align="center">
         <img src="/assets/data/2022-09-03-robust/weight2.png" width="1000"/>
    </p>
    - 그래서 위의 슬라이드에서도 소개했듯 1995년 Parameter Estimation Techniques: A Tutorial with Application to Conic Fitting 논문을 보면 Huber, Cauchy, ... 뭐 종류가 많다. 
        - 어쨌거나 deweighting 하는 민감도를 조금조금씩 다르게 설정하겠다는 이야기이지 거의 개념은 비슷하다. 
        - 한편, 2019년 CVPR에서 John Barron 님께서 __`이 모든 다양한 Kernel 들은 사실 general 한 수식의 특수예들에 지나지 않음!`__ 이라는 논문을 냈다.
            - 2019 CVPR, A general and adaptive robust loss function
            - 그리고 이것이 SymForce 에서 `BarronNoiseModel` 이라는 이름으로 이미 구현이 되어 있기 때문에, 아래 실습에서 우리는 이를 사용해볼 것이다. 
            - 톺아보면, SymForce의 `noise_models.py` 에서 이러한 주석을 볼 수 있다.  
            ```python
            # see the class BarronNoiseModel(ScalarNoiseModel) definition in noise_models.py
                """
                    alpha: Controls shape and convexity of the loss function. Notable values:
                        alpha = 2 -> L2 loss
                        alpha = 1 -> Pseudo-huber loss
                        alpha = 0 -> Cauchy loss
                        alpha = -2 -> Geman-McClure loss
                        alpha = -inf -> Welsch loss
                    delta: Determines the transition point from quadratic to robust. Similar to "delta" as used
                        by the pseudo-huber loss function.
                    scalar_information: Scalar representing the inverse of the variance of an element of the
                        unwhitened residual. Conceptually, we use "scalar_information" to whiten (in a
                        probabalistic sense) the unwhitened residual before passing it through the Barron loss.
                    x_epsilon: Small value used for handling the singularity at x == 0.
                    alpha_epsilon: Small value used for handling singularities around alpha.
                """
            ```

    - 이 계열의 방법은 구현하는 입장에서 되게 straightforward 하다는 것이 장점이다. 

# 실습 
- [여기 Jupyter notebook](https://github.com/gisbi-kim/symforce-tutorials/blob/main/nonlinear_icp/3_nonlinear_icp_Sim3_robust/nonlinear_icp_Sim3_outlier_robust.ipynb) 으로 실습을 각자 해볼 수 있다.
    - 전체적인 실습코드의 백본은 전편과 동일하다. 
    - 다만 이제 false correspondences 를 섞어주는 부분, robust kernel을 씌우는 부분 정도 의 수정이 있다. 
    - 기존 ICP 알고리즘 코드가 완전히 동일할 때, 단순히 robust kernel 을 씌우는 것 (최소한의 코드수정!)만으로도 outlier 에 간편하게 대응할 수 있다는 것이 M-estimation 방법의 장점이다!
        - 직접 실습을 통해 느껴보자.
        - ps. 물론 trick들을 좀 섞어주면 좋다. 예를 들어, 위의 실습코드에 보면 최적화 대상이 되는 7-dim state vector 에서 특정 dimension (특히 scale부분) 의 값 크기를 다른 element 대비 좀 작게 rescaling 해주면 수렴이 더욱 안정적으로 된다. 
- 자세한 실습과정은 영상으로 대체함. 
    <p align="center">
        <iframe width="800" height="400" src="https://www.youtube.com/embed/p54avTphKFw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </p>
    
# 결론 
## 요약 
1. Robust optimization 의 두 부류 (1. outlier pruning, 2. deweighting) 에 대해 알아보았다. 
2. Outlier가 있을 때에도 Sim(3) registration 이 잘되는 실습을 해보았다. 
    1. SymForce의 Barron loss 를 사용해서 deweighting 을 쉽게 구현할 수 있다. 
    2. robust loss 없이는 단 5%의 outlier만 있더라도 해가 수렴하지 않는다. 
    3. 반면 50% 의 outlier 가 있어도 해가 깨지지 않고 잘 수렴한다. 
    - note: 하지만 어느정도 수렴 상한선이 있기 때문에, robust loss 를 통해 안전하게 어느정도 estimate 을 예측하고 난 다음에는, correspondence 찾는 과정을 다시 수행해주어서, outlier 비율 자체를 다시 한번 줄여주려는 노력이 현실문제를 풀 때에는 요구될 것이다. 
    
## 여담 
### Pose-graph optimization 에의 적용 
- [예전 포스트]({{ site.baseurl }}{% post_url 2022-07-31-pgo-toy %}) 에서도 알아보았듯이, pose-graph optimization을 통해 누적된 trajectory의 drift를 극복하고 globally consistent 한 map을 만드는 데 기여할 수 있다. 이 예제에서는 간단한 circle 형태의 trajectory였었고, 역시 correspondence 가 주어져있었다. 즉, 한 바퀴를 돌았다는 사실을 이미 알고 있었고, 제일 첫번째 node와 제일 마지막 node 사이를 이어주면 된다는 것이 알려져있었다. 
- 물론, 현실세계에서 그 사실은 그렇게 쉽게 알려지지는 않고 ... 이것에 대해 연구하는 분야가 **place recognition** 이다.
- 현실 세계에서는 perceptual aliasing 이 빈번하기 때문에, 역시 이 place recognition 을 수행할 때에도 false correspondence 가 만들어지기 쉽다. 
    - 즉 예를 들어 아래와 같이 .. 
    <p align="center">
         <img src="/assets/data/2022-09-03-robust/pr.png" width="700"/>
    </p>
- 그래서 pose-graph optimization 을 수행할 때에도 앞서 소개한 Deweighting 을 필수로 적용하는 것이 좋다. 
    - 만약 그렇지 않다면 ... 전체적으로 Trajectory 가 망가지게 된다. 아래와 같이 ... (그림은 13 ICRA Robust map optimization using dynamic covariance scaling)
    <p align="center">
         <img src="/assets/data/2022-09-03-robust/dcs.png" width="500"/>
    </p>
- Deweighting (간단하게 Cauchy kernel) 을 적용하면 아래와 같이, false pair 가 아주아주 많아도 전체 trajectory가 깨지지 않고 잘 구성된 것을 알 수 있다. 
    - 아래 그림은 SC-A-LOAM 을 수행한 결과 예시. 
    <p align="center">
         <img src="/assets/data/2022-09-03-robust/scaloam.png" width="900"/>
    </p>

### The three important things in computer vision 
- 여담으로 재미있는 이야기 하나. 
- CMU 박사를 졸업한 [Xiaolong Wang 교수님](https://scholar.google.com/citations?hl=ko&user=Y8O9N_0AAAAJ&view_op=list_works&sortby=pubdate) 의 박사학위논문을 보면 이런말이 나온다. 
    <p align="center">
         <img src="/assets/data/2022-09-03-robust/corres3.png" width="700"/>
    </p>
    - Computer vision 에서 그 유명한 Takeo Kanade (Lucas-Kanade 의!) 교수님이 한 말이라고 (믿거나 말거나) 한다. 
    - Computer vision 이 다루는 태스크 중 제일 중요한 세 가지를 꼽으라면 correspondence와 correspondence와 correspondence 라고 ... 
    - 이번 포스트에서 방어적 역할을 하는 m-estimation 기반의 robuist optimization에 대해 알아보았지만, 어쨌든 이는 방어적인것이고.. 앞의 포스팅들에서 살펴보았듯 correspondence 가 true 면 최적화는 이론적으로 잘 될수밖에 없기 때문이다. 
    - 최근 딥러닝이 정말 잘하는 부분도 바로 이런 부분이 되겠다. 
        - 예를 들어 SuperPoint, SuperGlue, [LoFTR](https://huggingface.co/spaces/kornia/Kornia-LoFTR) 로 대표되는 딥 매칭 방법들. 
        - 그래서 Robotics 에서 또 중요한 문제 중 하나인 **Visual Localization** (== camera's 6D pose estimation) 에서도 [SuperGlue 에 기반한 방법이 Sota 를](https://github.com/cvg/Hierarchical-Localization) 찍기도 하였다. 
        - 그래서 역시 visual localization에서도 correspondence만 잘 예측되면, 실제로 최적화 자체는 계산기이고 결과적으로 대체로 좋은 품질의 해를 얻을 수 있다는 것 
            - 딥러닝 초기 (2015년) 에는 네트워크에 (input, output) data 만 주고 end-to-end 로 학습시키면 뭔가 그안에서 마법같은 일이 일어나서 새로운 input이 들어가도 그것의 pose 를 잘 알려줄 거야 (15 CVPR Posenet: A convolutional network for real-time 6-dof camera relocalization) 라는 믿음이 있었는데 .., 
            - 이걸 해보면 생각보다 잘 안된다. 이런 심증이 연구자들 사이에서 계속 있다가, 
            - 이후 CVPR 2019에 와서 Understanding the Limitations of CNN-based Absolute Camera Pose Regression 라는 논문에서 그 믿음이 제대로 지적당한다. 그리고 [SuperGlue저자의 Hloc](https://github.com/cvg/Hierarchical-Localization) 이 실험적으로도 보여주게 된다: 딥러닝은 feature correspondence 만 잘 지어주면 된다. 2D-3D matches 만 잘 생성되면 그 이후는 기존에 잘 정립된 수학인 PnP 만으로도 충분하다 (이 부분은 딥이 할 게 아니라는 것).. 라는 이야기.
                - 물론 이런 conventional 한 geometric pipeline (e.g., svd, pnp, ...) 자체를 differentiable 하게 구성하는 것은 옳은 방향이다! 그 노력이 바로 [Kornia](https://github.com/kornia/kornia) 라는 프로젝트.
    - 사설이 길었으니 Localization 이야기는 나중에 또 따로 해보도록 하자. 


