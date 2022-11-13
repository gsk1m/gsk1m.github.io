---
layout: post
title:  "🌈 Robotics Engineer 로서 자주하는 생각들"
date:   2022-11-12 23:00:00 +0000
categories: Daily
---

# 연구 및 일 하는 데 도움이 되었던 생각들
- 개인적으로 도움이 되어온 생각들을 까먹을까봐 기록해둔다.
- 바뀔 수 있음.

## 0. Mind 편
1. Time is the first resource we need to take care of.
  - 물론 어떤 길을 가도 다 말이되기야 하겠지만, 우리는 시간이 없기 때문에... 좋아보이는 것이 아니라 위대한 것이 필요하다. 브라이언 트레이시의 '겟 스마트' 를 읽으며 많이 solid 해진 생각.
2. Occam's Razor.
  - 같은 효과를 거둔다면 단순한 것이 좋은 것이다.
3. Do not overengineering.
  - 닭잡는 데 소잡는 칼 쓰(느라 시간낭비 하)지 말기.
4. Do not overclaim.
  - 실제보다 부풀려 말하지 않기. 특정 상황에서만 잘 되는 것을 제네릭한 단어로 포장하지 않기. 논문 리뷰할 때 많이 지적하는 부분.
4. Be a social: Talk, share, and rearrange.
  - 속한 조직에 정말로 필요한 게 뭔지 알아내려면 많이 이야기를 나누어야 한다. 이야기를 진행시키기 위해 자주 많이 공유해야 한다. 그리고 방향을 재조정해야 한다. 일은 최적화와 비슷한 듯. 중요한 것은 방향과 보폭. 방향과 보폭 --즉, gradient-- 은 누가 알려주는가?: overclaim 하지 않은 share + 동료들과의 대화.  

## 1. 엔지니어링 편
- Engineering is (mathematically) optimization.
- All optimization is composed of twp steps: **1. Problem building (front-end)** and **2. Problem solving (back-end)**.

### 1-1. Front-end 편
1. Outliers are inevitable.
  - 그래서 나는 시뮬레이션을 (그것이 성립하는 게임 등을 예외로 하면) 별로 안 좋아한다.
2. Correspondence, correspondence, and correspondence.
  - computer vision 분야의 유명한 교수님인 [Takeo Kanade 께서 이 말을 하셨다고 함](https://arxiv.org/pdf/1903.07593.pdf).
  - 그만큼 correspondence association (outlier 들이 inevitable 하기 때문에) 이 어렵다는 뜻이겠고, 그만큼 correspondence association 만 풀린다면 다 풀린다는 걸 의미한다고 생각한다.

### 1-2. Back-end 편
1. The world is non-linear
  - 어쩔 수 없는 진리.
2. Initial is all you need.
  - 세상은 nonlinear 하지만 결국 linearize해서 풀려고 하기 때문.
  - 이는 위의 0.2 와 0.3 과도 통한다. 복잡한 이론을 개발하는 것보다, 약간의 돈+공수 를 들여 Initial 을 개선할 수 있다면 문제를 쉽게 풀어 시간을 절약할 수 있는 경우가 많음.
3. Gauss-Newton: Gradient is then you need.
  - 대학에서 딱 한 가지만 배워야 한다면 Gauss-newton algorithm 과 이를 위한 auto-diff 를 꼽고 싶다.


## 2. 구현 편
1. SW guy must know HW.
  - 개인적으로도 부족한 부분.
2. Not only estimate, but also measure.
  - 특히 time cost 리포트할 때, 측정하지 않고 넘겨짚으면 0.1나 0.2를 어기는 상황이 벌어진다.
3. Not table, but demo.
  - [AJ Davison 교수님께서 하셨던 말](https://twitter.com/ajddavison/status/1359565780095029249).
  - Table은 그 데이터셋에서는 잘 했다는 걸 의미하고 여전히 안되는 건 안된다는 걸 의미한다. 근데 Demo는 아무튼 잘 된다는 걸 의미한다.  

# 결론
- 그리고 행동이 더욱 중요하다.
