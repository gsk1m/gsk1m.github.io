---
layout: post
title:  "🌈 Row-major, Column-major, 그리고 컴파일러 최적화"
date:   2023-06-23 23:00:00 +0000
categories: Productivity
---

# Compiler 가 해주는 것과 안해주는 것에 대해 알아보자
- 컴파일러 (와 ChatGPT)는 나의 동료다.

## 개요 
- 일반적인 코딩 팁 같은 이야기는 인터넷에 이미 많으므로 원래 잘 안쓰려고 하지만 ...
	- 나름대로 실험해본 바가 있어 기록 차원에서 정리해두려고 한다. 

## 문제 상황 
- Matrix 가 있을 때 익숙히 알려진 꿀팁이 
	- Matrix 전체 elmenet 들을 순회하려면 for loop 두번을 돌아야 하는데, 
	- 이 때 row-major 가 column-major 보다 성능이 좋다는 것이다. 

## 배경 지식 
- 왜 그런지는 관련 자료는 많으므로 키워드만 정리하고 넘어간다 ~~자세한 설명은 생략한다~~ . 
	- 키워드: `Cache Locality`, `Cache Line`, `Cache Miss`
		- 결국 Cache Cache Cache! 이게 matrix 순회 예제에 그치면 안되고, Cache Locality 라는 개념과 HW (`CPU` 와 `Memory` 구조)에 대해 전체적으로 이해하고 넘어가야 실제로 공부를 한 것이라고 할 수 있겠다. 
	- 추천 자료
		- [Evaluating array reference performance: row-major vs. column-major order](https://onestepcode.com/array-reference-performance-row-major-vs-column-major-order/)
		-  [The impact of cache locality on performance in C through matrix multiplication](https://levelup.gitconnected.com/c-programming-hacks-4-matrix-multiplication-are-we-doing-it-right-21a9f1cbf53)
		- [CppCon 2016: Timur Doumler “Want fast C++? Know your hardware!"](https://www.youtube.com/watch?v=BP6NxVxDQIs&t=2429s)
		- [false sharing. 병렬 프로그램 필수 상식](https://www.youtube.com/watch?v=SbaiAQDUi9A)
		- [Data-oriented Design (데이터 지향 설계, DoD)](https://tsyang.tistory.com/68)

## 실습		
### Hands-on Experience
- 늘 강조하는 것: 그냥 읽어보고 그림만 보는 것과 직접 from-scratch로 구현해서 자기 데이터에서 실험해서 직접 run script 쳐서 직접 결과를 보는 것은 실습자 입장에서 천지 차이 이다.
- 그러므로 직접해보자. 

### 구현 
- 코드 
	- [rowmajor_colmajor_and_functional.cpp](https://github.com/gisbi-kim/cpp_study/blob/master/main_20230624_rowmajor_colmajor_and_functional.cpp) 
- 설명 
	- 다음과 같이 시간을 측정해주는 팩토리를 만들고
	  <p align="center">
	    <img src="/assets/data/2023-06-24-rowcolfunc/factory.png" width="560"/>
	  </p>
	- 팩토리클래스에 다음과 같은 서로다른 3종의 람다함수머신들을 넣어주면 된다. 
	  <p align="center">
	    <img src="/assets/data/2023-06-24-rowcolfunc/machines.png" width="900"/>
	  </p>

### 실행 
- 실습환경 
	- cpp web compiler 라고 치면 간단하게 실습해볼 수 있는 곳들이 많이 나온다. 
		- 나는 [onlinegdb](https://www.onlinegdb.com/online_c++_compiler) 에 코드를 붙여넣기 해서 실행해보았다. 
	- Tip
		- O3 로 build 하려면, 
			- 직접 리눅스 머신에서 실험한다면 `g++ -std=c++17 -O3 main.cpp -o main` 와 같이 빌드해주면 되고, 
			- 위의 [onlinegdb](https://www.onlinegdb.com/online_c++_compiler) 사이트에서 적용하려면 아래 그림과 같이 한 다음 OK누르고 실행 해주면 된다. 
			  <p align="center">
				    <img src="/assets/data/2023-06-24-rowcolfunc/o3.png" width="700"/>
			  </p>

### 결과 
- matrix dimension SIZE를 달리해가며 실험하였다. 
- 결과는 다음과 같았다 (각 SIZE에 대해 1회씩만 측정하였다).
	- 여기서 가로축의 Size 는 한 축의 dimension을 의미한다. 즉, SIZE=10000 이면, 10000 by 10000 matrix 를 순회하는 것이다.
  <p align="center">
    <img src="/assets/data/2023-06-24-rowcolfunc/plot.png" width="800"/>
  </p>

- 위 ablation study의 의도
	- __Question 1:__ row-major 와 column major 의 from-scratch 구현체 결과를 비교하였다. (see `파랑실선 <=> 빨강실선`)
	- __Question 2:__ row-major의 from-scratch 구현체와 row-major 의 std::algorithmic(i.e., functional)한 구현체 결과를 비교하였다. (see `파랑실선 <=> 초록실선`)
	- __Question 3:__ compiler optimization option (-O3) 을 안넣고 넣고 차이를 비교하였다 (see `실선 <=> 점선`) 

### 결과 해석 
- Answer of Question 1: 
	- 당연히 익히 알고 있는대로 row-major 가 훨씬 빠르다  (see `파랑실선 <=> 빨강실선`).
- Answer of Question 2: 
	- std::algorithm을 최대한 활용하면 functional 한 디자인이 자연스럽게 강제됨으로써 유지보수성이 좋아진다. 그래서 우리는 최대한 std::algorithm을 활용하고 싶다.
	- 하지만 no optimization 환경에서는 raw 한 double nested loops 보다 느린 성능을 보여주었다 (see `파랑실선 <=> 초록실선`). 
		- 사실 n이 작을 때 (e.g., `~` SIZE=10000 => 10000*10000 회의 순회) 는 차이가 크지 않다.
- Answer of Question 3: 
	- -O3 옵션을 넣어주니 std::algorith을 활용한 functional한 code 가 (적어도 이 예제의 이 실험에서는) raw 한 nested for loop 보다 (조금이나마) 더 빨랐다. 


## 결론
### Lessons 
- 위의 결론으로부터 다음과 같은 레슨을 도출할 수 있겠다. 
	1. 괜한 성능고민~~아집~~하지 말고 `std::algorithm`의 `functional` 한 interface를 최대한 활용하자. 
		- 코드의 readability, maintainability, and testability 를 확보하는 것이 더욱 중요하다. 
	2. 그런 다음, 최적화를 위해서는 O3 등 컴파일러를 믿자 (컴파일러는 나의 동료! 버그도 찾아주고 성능도 올려준다). 
		- 휴먼리더블 코드의 구현이 어떻고 저떻고에 대한 토의를 아무리 해도 실제 구동과 다를 수 있다. 즉, 코드의 성능에 관해 이야기 하려면, 컴파일러 최적화 까지 적용된 것에 대해서에만 논의해야 의미가 있다. 
	3. 그런데 애초에 설계 자체가 잘못되(==column-major)면 아무리 컴파일러를 믿어도 안 되는 것은 안 되는 것이다.~~안돼 돌아가~~
		- 예를 들어, 위의 결과 그림에서, O3 optimization을 적용한 빨강점선은 opt 를 적용하지 않은 초록실선 결과보다 여전히 매우 느렸다. 
	4. 따라서 HW를 이해하고 근원적으로 올바른 개념을 작성하는 것은 여전히 프로그래머의 몫이라고 할 수 있겠다. 
		- HW에 대한 이해는 많은 것들이 추상화 되고 프로그래밍이 입문하기 점점 더 쉬워지는 요즘에도 여전히 중요하다. 
	5. 그리고 측정하자! 
		- 측정한 것으로 논의해야 한다.
- ps.
	- 벤치마크는 여러번 돌려봐야 하는데 이 포스팅에서는 편의상 1번만 수행하였다. 그리고 그 소요시간도 머신마다도 다를 수 있다. 경향만 참고를 하자. 게다가 위에서는 onlinegdb라는 사이트에서 실행하였는데, 자원의 경쟁이 있는지..? 다른 시각에 같은 코드도 실행하니 엄청 더 느릴 때도 있었다.
	- 위의 matrix예제 같은 문제에서는 애초에 쌩으로 돌 생각보다는 [vectorization and parallel algorithms](https://en.cppreference.com/w/cpp/algorithm/execution_policy_tag_t)을 고려하는 것이 좋을 것이다.
	- 그나저나 ChatGPT(4) 에게 [위의 코드 주석에 있는 결과 서머리](https://github.com/gisbi-kim/cpp_study/blob/master/main_20230624_rowmajor_colmajor_and_functional.cpp#L1) 를 복사해서 주고 파이썬으로 비교 플롯 그려줘 라고 하고 색깔 정도만 지정해줬더니 위의 결과 plot을 그리는 파이썬 코드를 작성해주었다!! 불과 몇 개월만에 이제 신기하지않고 이정도는 이제 당연히 해주겠지? 라는 마음으로 적응해가고 있는 듯하다. 그리고 해준다!






