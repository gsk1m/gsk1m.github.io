---
layout: post
title:  "🌈 R2LIVE Reprojection Error Jacobian 유도해보기 "
date:   2024-05-25 01:00:00 +0000
categories: SLAM
---

# 자코비안 유도
- 어렵지 않아요 

## 개론 
- 자코비안, 야코비안, ... 다양하게 불리는 이 녀석
- 그냥 multi-dimensional 기울기라고 생각하면 된다.
- Gauss-Newton optimization 을 하기 위해서는 기울기 값을 알아야 하기 때문. 
    - Kalman Filter 에서도 쓰인다. (ps. 사실 iterative KF는 GN 과 같다. ref: 1993 The Iterated Kalman Filter Update as a Gauss-Newton Method)
- 어떤 cost function $c$ 가 있고, 보통 이건 SLAM에서 $c := h(\textbf{x}) - \textbf{z} = \textbf{H}\textbf{x} - \textbf{z}$ 이런 형태로 많이 쓰인다. H 는 measurement function (현재 상태에서의 예측 측정 값을 위한 모델) 이고, z 는 실제 측정 값, 그리고 cost 란 그 둘의 차이 를 의미하는 게 되겠다. 
- 그리고 당연히 (model이 잘 설정되어 있고) 현재 state 값 $\textbf{x}$ 가 truth 에 가깝다면, 예측 측정값은 실제 측정값과 거의 같게 (== 작은 cost) 나올 것이다. 
- 그리고 반대로 생각하면, 그 cost 가 작아지게 하도록 현재 state 값 $\textbf{x}$ 를 조절할 수 있으면, 현재 state 값 $\textbf{x}$ 을 더 truth 에 가깝게 만들었다고 말할 수 있겠다.  
- 그런데 실제 세계의 문제에서는 cost 도 vector 이고, state 도 vector 이다. 
    - 예를 들어서, reprojection error 의 cost 는 가로 pixel 차이, 세로 pixel 차이 의 2-dimension vector이고, state 는 정하기 나름이지만 가장 간단하게는 position 과 rotation (attitude) 를 concat 한 6-dimension vector 라고 할 수 있다. 
- 따라서 cost 의 각 성분마다, state 의 각 성분에 대해 미분한 것이 scalar 로 나오게 되므로 
- vector cost에 대한 vector state 의 자코비안은 matrix 가 되게 된다. 
- 그러면 이 때 자코비안의 shape 은 어떻게 될까요?
- 자코비안은 이렇게 기억하면 된다. matrix 네모의 세로(즉, row 들) 는 cost, 가로(즉, column들)는 state.
    - 그럼 위 예시에서는 2x6 모양의 자코비안 매트릭스가 구해지게 된다. 
- 이제 특정 상황 (즉, 특정 state 와 특정 cost) 에서의 자코비안 매트릭스가 알려지면, 그 다음 단계는 완전한 계산기이다. 
    - GN은 normal equation 을 풀면 되고, 
    - KF는 유명한 KF equation 따라서 풀면된다. 


## 개론 2 
- 그런데 세상은 nonlinear 하므로, 
- 우리는 vector space 에 살고 있지 않은 $\textbf{x}$ 를 최적화 할 수 없고, 
- 대신 vector space에 살고 있는 $\delta\textbf{x}$ 를 최적화 해야 한다.
    - 사실 SLAM의 거의 모든 경우에서 state의 rotation 에 한정된다. $\mathbf{R} = \textbf{Exp}(\delta{r})$ 이 얘긴데 ... rotation formulation 이야기까지 하면 너무 길어지니 이것은 다른 게시글 [앞의 블로그 튜토리얼]({{ site.baseurl }}{% post_url 2023-12-16-nano-lie-theory %}) 로 링크를 남긴다 .. 
- 즉, $c = \textbf{H}\textbf{x} - \textbf{z}$ 대신, $\textbf{H}(\textbf{x} + \delta\textbf{x}) - \textbf{z}$ 에 대한 자코비안을 구한다. 
- 뭔소리냐면, $\frac{d(\mathbf{H}\mathbf{x} - \mathbf{z})}{d\mathbf{x}}$ 대신에, $\frac{d(\mathbf{H}(\textbf{x} + \delta\textbf{x}) - \mathbf{z})}{d\delta\mathbf{x}}$ 를 구해야 한다는 것이다. 
- 어려워보이지만 그냥 cost 와 state 가 $\mathbf{x}$ 에서  $\delta\mathbf{x}$ 로 바뀐것이 전부이다. $\delta\mathbf{x}$ 라는 문자가 괜히 어려워 보이면 $\mathbf{x}_d$ 뭐 이런식으로 써볼 수도 있겠다. $\delta\mathbf{x}$ 도 그저 vector이다. 많은 텍스트북에서 $\delta\mathbf{x}$ 라고 쓴다. 
- 이 때 핵심은, $\frac{d(\mathbf{H}(\textbf{x} + \delta\textbf{x}) - \mathbf{z})}{d\delta\mathbf{x}}$ 라는 미분에서, $\mathbf{x}$ 에 대해 미분하는 것이 아니라는 점이다. 즉, 이 때는, $\mathbf{x}$ 는 상수값이다. 예를 들어 직전 시점의 $\mathbf{x}$ 가 어떤 값 $\mathbf{b}$ 였다고 하자. 그럼 방금 전의 미분 식을 좀 더 심리적으로 장벽이 덜한 형태로 써보면 $\frac{d(\mathbf{H}(\textbf{b} + \textbf{x}_d) - \mathbf{z})}{d\mathbf{x}_d}$ 뭐 이렇게도 써볼 수 있겠다. 좀 더 마음이 편안한 형태를 위해서 괄호를 풀어서 써보면 $\frac{d(\mathbf{H}\textbf{x}_d - (\mathbf{z} - \mathbf{H}\textbf{b}))}{d\mathbf{x}_d}$ 이렇게 써볼 수도 있겠다. 즉, 원래 거랑 모양새는 똑같으니 쫄 필요 없다.
- $\frac{d(\mathbf{A}\mathbf{x})}{d\mathbf{x}} = \mathbf{A}$ 이므로 $\frac{d(\mathbf{H}\textbf{x}_d - (\mathbf{z} - \mathbf{H}\textbf{b}))}{d\mathbf{x}_d} = \mathbf{H}$ 이다. 참 쉽죠? 
- 즉, 미분대상의 1차 linear term __만__ 있으면 편미분은 매우 쉽고 자명해진다. 
- SLAM 논문들의 대부분의 자코비안 유도에서는 이 트릭을 이용하고 있다. 
- 왜냐하면, 
    - 미분을 해야하는 대상은 보통 일반적으로 position, rotation (attitude), velocity, bias 등인데, 
    - 여기에서 rotation 외에는 모두 이미 vector space 에서 살고 있다. 
    - vector space 에 사는 항목들은 위의 $\frac{d(\mathbf{A}\mathbf{x})}{d\mathbf{x}} = \mathbf{A}$ 라는 사실을 이미 적용할 수 있다.  
        - 예를 들어서 
            - 어떤 position term에 대한 cost function 이 position 에 대한 linear combination (다항차수) 로 구성되어 있다고 하더라도, 
            - iterative optimization 에서는 미소변위를 최적화 하기 때문에 미소변위 position 의 2차 이상들은 거의 0으로 근사할 수 있다, 이렇게 해버리게 되면 
            - 결국 남는 것은 1차 term으로 간소화 할 수 있다. 
    - 하지만 rotation term 의 미소변위인 rotation vector (== lie algebra) 는 default 형태로는 1차 term만이 남지 않기 때문에, 
    - 몇 가지 트릭들을 더 더해줘서, $\frac{d(\mathbf{A}\mathbf{r})}{d\mathbf{r}} = \mathbf{A\}$ 과 같이 위의 기본 트릭을 동일하게 적용할 수 있게 해주는 것이 일반적인 과정이다. 
- 그 몇 가지 트릭들이 어떤 것인지 실제 예시를 통해 알아보자. 

## R2LIVE reprojection error 
- [R2LIVE 논문의 appendix](https://arxiv.org/pdf/2102.12400) 의 reprojection error 유도 과정을 조금 더 상세하게 풀어서 써보는 게 이 포스트의 목적이다.

$$
\begin{align*}
\mathbf{P}_{\mathbf{C}} (\check{\mathbf{x}}_{k+1} \boxplus \delta \check{\mathbf{x}}_{k+1}, {}^{\mathbf{G}}\mathbf{P}_s) = \left( {}^{\mathbf{G}}\check{\mathbf{R}}_{I_{k+1}} \textbf{Exp} \left( {}^{\mathbf{G}}\delta \check{\mathbf{r}}_{I_{k+1}} \right) {}^{\mathbf{I}}\check{\mathbf{R}}_{C_{k+1}} \textbf{Exp} \left( {}^{\mathbf{I}}\delta \check{\mathbf{r}}_{C_{k+1}} \right) \right)^T {}^{\mathbf{G}}\mathbf{P}_s - {}^{I}\check{\mathbf{p}}_{C}\\ 
- {}^{\mathbf{I}}\delta \check{\mathbf{p}}_{\mathbf{C}} - \left( {}^{\mathbf{I}}\check{\mathbf{R}}_{\mathbf{C}} \textbf{Exp} \left( {}^{\mathbf{I}}\delta \check{\mathbf{r}}_{\mathbf{C}} \right) \right)^T \left( {}^{\mathbf{G}}\check{\mathbf{p}}_{I_{k+1}} + {}^{\mathbf{G}}\delta \check{\mathbf{p}}_{I_{k+1}} \right)
\end{align*}
$$

- 위 수식을 각 미소변위 subvector 들에 대해서 편미분 하면, 그 subvector에 대한 submatrix (block) Jacobian들을 얻을 수 있다. 이걸 그저 잘 쌓아서 하나의 Jacobian matrix 를 만들면 된다.
- 예를 들어서, world 좌표계에서의, position 에 대한 미소변위 ${}^{\mathbf{G}}\delta \check{\mathbf{p}}\_{I_{k+1}}$ 에 대해 위의 cost (reprojection error) 를 미분하면,
    - $\mathbf{M}_{\mathbf{B}}$ 라는 블록을 얻을 수 있다. 
    <figure align="center">
        <img src="/assets/data/2024-05-25-reprojection-jacobian-r2live/visual_diff_pos.png" width="1000">
        <figcaption></figcaption>
    </figure>
    - ps. dp 입장에서 dr 은 0으로 무시할 수 있기 때문에, 이 경우에, $\textbf{Exp} \left( {}^{\mathbf{I}}\delta \check{\mathbf{r}}\_{\mathbf{C}} \right)$ 은 $\mathbf{I}$ 로 근사해서 저렇게 되었다. 
- 아무튼 이미 vector space 에 살고 있는 position term에 대해서는 cost 가 1차 term만 이미 예쁘게 남겨져있을 때, 편미분하여 자코비안 블록을 얻어내는 것이 매우 자명함을 알 수 있다. 
- 그렇다면 이제 rotation 쪽 term에 대한 추가 트릭을 알아보자는 것이다. 
- 즉, 아래의 $\mathbf{M}_{\mathbf{A}}$ 는 도대체 어떻게 얻어지는 것인가? 
    <figure align="center">
        <img src="/assets/data/2024-05-25-reprojection-jacobian-r2live/visual_diff_rot.png" width="1000">
        <figcaption></figcaption>
    </figure>
- 일단 rotation term에 대한 cost (초록색 박스) 에서, 미분대상인 ${}^{\mathbf{I}}\delta \check{\mathbf{r}}\_{\mathbf{C}}$ 가 1차 term으로 존재하고 있지 않다. 
- 따라서 저 초록색 박스를 지지고 볶아서 ${}^{\mathbf{I}}\delta \check{\mathbf{r}}\_{\mathbf{C}}$ 에 어떤 매트릭스가 그저 단순하게 matmul로 곱해진 형태로 만드는 것이 이제 목표이다.
- 일단 논의를 단순화 하기 위해 
    - extrinsic parameter 는 고정하자. 즉, IMU에 대한 camera 의 relative rotation 에 대한 미소변화량이 없는 것이다. 조정하지 않을 것이기 때문에. 즉, ${}^{\mathbf{I}}\delta \check{\mathbf{r}}\_{C_{k+1}}$ 을 $\mathbf{0}$ 이다. 그럼 $\textbf{Exp} \left( {}^{\mathbf{I}}\delta \check{\mathbf{r}}\_{C_{k+1}} \right)$ 는 $\mathbf{I}$ 가 된다. 
    - 그러면 $\left( {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}} \textbf{Exp} \left( {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}} \right) {}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}} \textbf{Exp} \left( {}^{\mathbf{I}}\delta \check{\mathbf{r}}\_{C\_{k+1}} \right) \right)^T {}^{\mathbf{G}}\mathbf{P}\_s$ 는 
    - 이렇게 $\left( {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}} \textbf{Exp} \left( {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}} \right) {}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}} \right)^T {}^{\mathbf{G}}\mathbf{P}\_s$ 가 된다.
- 트릭 1: $\delta \mathbf{r} $ 이 작을 때, $\textbf{Exp} (\delta\mathbf{r}) \approx \mathbf{I} + \left[ \delta\mathbf{r}\right]\_{\times}$ 이다.
    - 그러면 $\left( {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}} \textbf{Exp} \left( {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}} \right) {}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}} \right)^T {}^{\mathbf{G}}\mathbf{P}\_s$ 는 
    - 이렇게 $\left( {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}} \left(\mathbf{I} + \left[ {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}}\right]\_{\times}\right) {}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}} \right)^T {}^{\mathbf{G}}\mathbf{P}\_s$ 가 된다.
- 트릭 2: $\left( \mathbf{A} \mathbf{B} \mathbf{C} \right)^{T} = \mathbf{C}^{T} \mathbf{B}^{T} \mathbf{A}^{T}$ 를 이용해서 위의 식을 펴주면 
    - 이렇게 $ {}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}}^{T} \left(\mathbf{I} + \left[ {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}}\right]\_{\times}\right)^{T} {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s$ 가 된다. 
- 트릭 3: $\left( \mathbf{A} + \mathbf{B} \right)^{T} = \mathbf{A}^{T} + \mathbf{B}^{T} $ 를 이용해서 위의 식을 펴주면 
    - 이렇게 ${}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}}^{T} {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s + {}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}}^{T} \left[ {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}}\right]\_{\times}^{T} {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s$ 가 된다.
    - 그나저나 첫번째 텀 ${}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}}^{T} {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s$ 는 rotation 미소변위에 대해서는 상수이므로 미분하면 0이 될 것이다. 따라서 그냥 편의상 간단하게 $c$ 라고 하자. 
- 트릭 4: skew-symmetric matrix 의 정의인, $\left[\delta\mathbf{r}\right]_\times^{T} = - \left[\delta\mathbf{r}\right]$ 를 이용하면 
    - 이렇게 $-{}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}}^{T} \left[ {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}}\right]\_{\times} {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s + c$ 가 된다. 
- 트릭 5: skew-symmetric matrix 는 두 벡터의 cross-product 의 matrix-vector 표현식과도 같다. 그렇다면 the cross product is anticommutative 라는 성질 $ \mathbf {a} \times \mathbf {b} =-(\mathbf {b} \times \mathbf {a} )$ 를 이용하면 
    - 위의 식에서 이 부분 $\left[ {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}}\right]\_{\times} {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s = -\left[ {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s \right]\_{\times} {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}} $ 가 된다.
    - 그러면 $-{}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}}^{T} \left[ {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}}\right]\_{\times} {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s + c$ 는 
    - 이렇게 ${}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}}^{T} \left[ {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s \right]\_{\times} {}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}} + c$ 가 된다. 
- 다 왔다!
- 위 식을 ${}^{\mathbf{G}}\delta \check{\mathbf{r}}\_{I\_{k+1}}$ 에 대해 미분하는 것은 이제 그저 $\frac{d(\mathbf{A}\mathbf{x})}{d\mathbf{x}} = \mathbf{A}$ 의 작업이다. 
- 따라서 논문에서 jacobian block, 즉 위의 그림에서 $\mathbf{M}_{\mathbf{A}}$ block 이 ${}^{\mathbf{I}}\check{\mathbf{R}}\_{C\_{k+1}}^{T} \left[ {}^{\mathbf{G}}\check{\mathbf{R}}\_{I\_{k+1}}^{T} {}^{\mathbf{G}}\mathbf{P}\_s \right]\_{\times}$ 였던 것이다. 
- 유도 끝!

## 결론 
- 1
    - 통상적으로 SLAM에서 푸는 state들은 항상 거기서 거기이다. position, velocity, attitude (rotation), bias, gravity, ... 
    - 이들 substate들에 대해서, 자코비안 구하는 것은 거의 공식화되어있다. 즉, 늘 항상 그런방식으로 ... 거기서 거기.
    - The One Ring: 여러 근사(approximations)들과 linear algebera trick 들을 이용해서 cost를 substate 에 대한 1차식으로 표현한다. 끝 
        - rotation을 제외한 substate들은 이미 vector space 에 있으므로 미분이 어렵지 않고, 
        - rotation 이라는 term은 어떤 문제들 풀던 존재하는 요소이므로, 어떤 문제를 풀던 위의 트릭들이 거의 항상 이용된다. 따라서 위에서 요약한 그 이상으로 뭔가 더 필요할 일도 별로 없다. 
- 2
    - 요즘 Symforce 등 manual하게 근사하지 않고 symbolic 하게 (사람이 보기에는 복잡하게) 자동으로 풀어주는 미분기들도 있으나 ... 
    - 위와 같이 manual 하게 자코비안을 구하면 사람이 읽는 코드가 아주 예뻐진다는 장점이 있다. interpretable 한 코드인지도 유지보수관점에서 중요하니까. 
        - 물론 Pytorch나 Ceres 처럼 automatic symbolic 이든 manual symbolic 이든 둘 다 하지않고 계산그래프를 유지해서 미분값 (value) 만을 알아서 계산해주면 자코비안이 어쩌네 다 몰라도 되긴 하지만 ... 알고 쓰는 것과 모르고 쓰는 것은 디버깅할 때 차이가 있다고 생각한다. 그리고 여전히 concise 한 code base 들에서는 직접 자코비안을 구해서 간단한 solve 를 직접 수행하는 오픈소스들도 많으므로 알아두면 유용할 것이다 (예: kiss-icp). 모르면 무작정 두렵지만 알면 쉽다. 
