---
layout: post
title:  "🌈 CUDA 프로그래밍: Thrust 실습 1편"
date:   2023-04-08 23:00:00 +0000
categories: SLAM
---

# 모던하게 GPU 병렬처리 하자
- CUDA 직접 짜는 것도 좋지만 
- 좀 더 편하고 싶다. 

## 개요 
- SLAM을 하다보면 센서 데이터로부터 수만개에서 수백만개까지 raw data 들을 다룰 일이 많다. 
    - 예를 들어서, cleaned static point cloud map을 만들기 위해 수행했던 [Removert (IROS 2020)](https://github.com/irapkaist/removert) 를 구현할 때, submap 을 만들고 이를 한 노드의 viewpoint 에 projection해서 projective range image를 얻었어야 했다. 그런데 100m 정도만 쌓아도 서브맵의 포인트 개수는 500만개에서 1000만개가 된다. 이 과정을 빠르게 하기 위해서 OpenMP를 사용하였지만, 여전히 느린게 아쉬웠다 (`removert` 는 여전히 느린 채로 남아 있다).
- 근본적으로 이러한 작업에는 CPU (를 아무리 수십코어 병렬 처리 한다고 하더라도 느리다) 가 아니라 GPU를 사용해야 함을 느끼고 있던 차에 
- Thrust 라는 library를 실습해보았다. 
    - CUDA library를 한번 더 하이레벨로 감싼 라이브러리라고 생각하면 된다. 
    - Modern C++ (C++11 이상) 스타일로 구현가능한 것이 특징이다. std::algorithm 의 문법과 상당히 닮아있다. 
        - 코드 비교 예시 (아래 실습비디오에서 사용한): __숫자 10억개 더하기__
          <p align="center">
            <img src="/assets/data/2023-04-08-thrust-practice-1/src-comp.png" width="1500"/>
          </p>

            - ps. 보다시피 사용법은 너무 쉽고 직관적이다 (빌드하는데 시간이 더 걸렸다 ㅠㅠ).
    - 그리고 CUDA 예제들에서 nvcc 로 커맨드라인에서 빌드하는 경우가 많은데, modern C++ 및 다른 라이브러리들과 함께 thrust 를 사용하기 위해서는 아무래도 cmake를 사용하는 것이 좀 더 깔끔하다. cmake 를 사용해서 실습을 진행해보자 (== CMakeLists.txt 를 만들자). 

# 실습 
- 실습 리포: [gisbi-kim/thrust_examples](https://github.com/gisbi-kim/thrust_examples)

## 준비과정 
1. Thrust 빌드 
    - [공식 github readme](https://github.com/NVIDIA/thrust#developing-thrust) 에 나와있는대로 하면 된다. 
        - 이 때 클린한 호스트 유지를 위해 docker 사용을 권장하고, 아래 예제에서도 docker 로 수행해볼 것이다. 
        - 첫번째 방법인 ci/local/build.bash 를 하니까 뭔가 안되서 그냥 아래 방법대로 스텝바이스텝 (git clone 하고 cmake .. 하고 make install) 으로 직접 빌드하였다. 
        - 대충 아무 시작 이미지 (e.g., `docker pull ubuntu:jammy`) 를 받아두고 docker run 해서 들어가자. 
            - 근데 너무 아무것도 없는 베이스를 땡겨오면 좀 귀찮고, 나는 ci/local/build.bash 를 하니 이것저것이 잘 설치되어있는 이미지가 하나 땡겨와져서 그것을 베이스로 사용했다. 
        - run할 때 gpu 를 잡아주면서 들어가야 한다. 즉, 옵션으로 `--gpus all` 를 넣어주면 된다. 
            - 예시: [docker_run.sh
](https://github.com/gisbi-kim/thrust_examples/blob/main/batch_sum/docker_run.sh)
        - docker run해서 image 안으로 들어간 다음에, 늘 하듯이 git clone 하고 cmake .. 하고 make install 한다. 
            - 그런데 compute_90이 안된다는 어떤 에러가 나와서 ccmake .. 에서 config 를 들어간 다음에 90 옵션을 OFF해주었다. 아마 내가 옛날 아키텍처(6.1)인 gtx1080ti 를 쓰고 있어서 최신 아키텍처 (9번대?) 에서 지원하는 뭔가가 빌드가 안되는 느낌이 든다. 
              <p align="center">
                <img src="/assets/data/2023-04-08-thrust-practice-1/off90.png" width="500"/>
              </p>
        - 라이브러리를 빌드하는 데에 꽤나 긴 시간 (i5 11gen에서 8core로 2-3시간 걸린 듯하다 (example 들을 다 빌드하느라..)) 이 지난 후... 뭔가 라이브러리 make install 은 다 되었는데, 어플리케이션 (아래 소개할) 빌드를 하고 실행하려고보니 forward compatible 한 HW를 찾을 수 없다는 에러가 떠서 안되었다. 
            - cuda toolkit version과 이에 대응되는 nvidia driver 를 다시 설치해주니 해결되었다. 내 경우 gtx1080ti에 cuda 11.4, nvidia driver 470 을 사용하였다. 

2. CMake를 사용해서 main code 빌드 
- 처음 써보는 라이브러리의 CMakeLists.txt 를 만드는 게 다 하고나서 보일러플레이트가 있으면 엄청 쉬운데, 처음 한 번 하기가 매우 일이다. 
    - 역시 이럴 때는 [공홈에 힌트](https://github.com/NVIDIA/thrust/blob/main/examples/cmake/add_subdir/CMakeLists.txt) 가 있다. 이걸 보고 대충 따라하면 아래와 같이 된다. 
    - code: [CMakeLists.txt](https://github.com/gisbi-kim/thrust_examples/blob/main/batch_sum/CMakeLists.txt)
      <p align="center">
        <img src="/assets/data/2023-04-08-thrust-practice-1/cmake.png" width="900"/>
      </p>
      - 처음에 저 `thrust_creat_target` 의 규약을 이해하지 못해서 좀 헤매었다. DEVICE뒤에 CPP를 붙이면 thrust code 를 사용하더라도 cpu만 사용한다 (ps. `DEVICE CUDA`를 `DEVICE CPP`로 바꾸고 `main_gpu_thrust.cu`를 `main_gpu_thrust.cpp` 로 이름바꿔서 직접 빌드 해보라! 분명 thrust 문법을 사용했지만 cpu를 사용할 때와 동일한 시간이 소요됨을 알 수 있다). 
      - `DEVICE CUDA`인 src에 대해서는, Modern C++ 문법을 사용하고 main이 들어있는 파일이지만 `.cu` 확장자로 이름을 지어준다. 왠지모르지만 `.cpp`로 끝나게 하니까 컴파일이 안되었다. 
        - 예를 들어, [공식 리포의 예제 코드들](https://github.com/NVIDIA/thrust/tree/main/examples) 도 모두 각각 main을 가지는 독립된 executables 들이지만 모두 `.cu` 확장자를 가짐을 알 수 있다. 
             - ps. [방금 구축한 CMake project directory](https://github.com/gisbi-kim/thrust_examples/tree/main/batch_sum)를 활용해서 공홈의 다른 예제들을 복사해와서 main_gpu.cu 안에 내용을 바꿔서 추가적으로 더 실습해보라!


## 실습 영상
- 아무튼 위의 과정을 거쳐서 간단한 예제인, 10억개 숫자 더하기를 해보았다. 
    - `std::uniform_int_distribution<> dist(0, 9999)` 와 같이 0에서 9999 사이의 int 들이 10억개 있다. 
<div align="center">
    <iframe width="840" height="473" src="https://www.youtube.com/embed/W8oPN4cNHLQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

## 결과 
- 할 때마다 다르지만, 대충 이정도 나온다. GPU로 돌릴 때 20배 정도 빠르다. 
  <p align="center">
    <img src="/assets/data/2023-04-08-thrust-practice-1/result1.png" width="1000"/>
  </p>
  - 위 영상에서 나오지만, 꼭 샘플이 10억개씩 있지 않더라도, 즉 1억개 정도에서도 여전히 20-30배 빠른 모습을 보여준다. 

### ps. 실습2 
- 참고로 cpp17부터 들어온 execution policy를 통해서 cpu 상에서 병렬처리를 수행해보았다. 
- 다만, 이 문법을 사용할 때는 compiler 가 생각하기에 병렬처리가 가능하면 최선을 다해주겠고, 아니면 안해줄 수도 있다, 라는 '고려는 해보겠다' 정도의 의미로 생각해야 한다. 
    - 메인 블록은 다음과 같다. 
        ```C++
            auto do_sum = [&]() {
                Number init = 0;
                Number sum = std::transform_reduce(
                    std::execution::par, vec.begin(), vec.end(), init,
                    std::plus<Number>(), [](const Number& num) { return num; });
                return sum;
            };

        ```
    - 결과는 다음과 같았다. 3번씩 수행했을 때, 
      <p align="center">
        <img src="/assets/data/2023-04-08-thrust-practice-1/result2.png" width="1000"/>
      </p>
      - 이득이 그리 크지 않음을 알 수 있다. 
        - 역시 (당연히) GPU를 사용해야만 유리한 병렬처리가 있다. 쿠다 설치의 마음의 장벽(귀찮음)을 이제는 허물자!
            - ps. 그나저나 ChatGPT에게 궁금한 걸 물어보았다. 
              <p align="center">
                <img src="/assets/data/2023-04-08-thrust-practice-1/chat.png" width="700"/>
              </p>

# 결론 
- 딥러닝 안해도 GPU 잘 쓰면 좋다. 
- ps. 요즘은 (2018년 이럴 때 대비 ...) nvidia driver 삭제 및 재설치도 금방 편하게 되더라.

## TODO
- [다른 공식 예제들](https://github.com/NVIDIA/thrust/tree/main/examples)도 돌려보자. 
- Robotics 에서 대용량 데이터 처리에 관한 튜토리얼을 만들어 보자. 
