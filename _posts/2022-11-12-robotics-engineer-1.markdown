---
layout: post
title:  "🌈 Matrix-illustrated Fast Kalman Gain Computation"
date:   2022-11-26 23:00:00 +0000
categories: SLAM
---

# FAST-LIO가 말 그대로 Fast 한 이유
- Kalman Gain 계산에는 matrix inversion 이 포함되어 있다.
- 근데 Matrix inversion 은 비싸다.
- FAST-LIO의 K 계산 과정에서의 Matrix 모양을 그림으로 그려서 알아보자.
