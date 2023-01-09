---
layout: post
title:  "🌈 IMU와 LiDAR를 퓨전해야 하는 이유 2 (Feat. PyPose and Open3D)"
date:   2023-01-09 00:00:00 +0000
categories: SLAM
---

# Loosely-coupled Lidar-Inertial odometry 실습
- <a href="/slam/2023/01/01/imu-lidar-fusion1.html"> 저번 블로그 글 </a> 에서 IMU만으로 odometry (즉, relative motion 추정) 를 하기 위해서는 global attidue가 중요함을 알아보았다 (물론 PVA: position, velocity, attitude 가 모두 중요하다).
- LiDAR scan matching (e.g., ICP) 이 PVA correction 을 해줄 수 있다. 좋은 global pose를 공급받은 IMU는 더 좋은 IMU odometry 를 생성한다. 🠒 이는 다음 턴의 ICP가 잘 수행되기 위한 좋은 이니셜이 된다. 🠒 좋은 이니셜을 공급받은 좋은 registration이 IMU의 state (i.e., PVA) 를 더 잘 보정한다 🠒 좋은 IMU pose 는 좋은 IMU odometry를 생성한다 🠒 ICP가 잘 된다 🠒 IMU의 pose 가 또 잘 보정된다 🠒 ...
- Python만 이용해서 간단하게 **loosely-coupled lidar-inertial odometry** (a.k.a mini-pyllio) 를 구현해보자.

## Before and After
- Comming soon ...
