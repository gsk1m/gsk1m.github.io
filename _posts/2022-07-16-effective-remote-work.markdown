---
layout: post
title:  "🌈 원격작업을 도와주는 3가지 방법"
date:   2022-07-08 21:03:36 +0000
categories: 생산성 
---

# 원격작업 생산성 향상을 위하여
- 미래의 나를 위해 .. 원격작업을 할 때 시간과 노력을 절약해줄 수 있는 방법들에 대해 정리해본다. 
1. ssh 접속 후 백그라운드에서 실행 후 빠져나오기  
2. jupyter notebook 이용
3. directory mount 와 vscode를 이용

## 0) disk mount 
- 먼저, 작업할 코드와 데이터들이 저장되는 원격 directory 를 로컬(주로 이 경우 노트북, 집의 데스크탑 등)에 마운트 함
- 이러면 아래에서 소개할 3가지 방법에서 모두 도움이 된다. 
- 방법 
    - /etc/fstab 에 ... 
    - TBA


## 1) ssh 접속 후 백그라운드에서 실행 후 빠져나오기  
- (주로 시간이 오래걸리는) **shell script 를 실행을 시작시키고, 빠져나오기** 위해 사용중이다. 
- 그 중에서도, **도커 컨테이너**와 **tmux** 를 이용한 방법을 사용 중이다.  
- 요약하면 다음과 같다. 
    1. docker 안에서는 tmux 를 디태치모드로 실행한다. 
    2. docker 를 디태치한다. 
    3. ssh로 접속한 terminal을 exit한다. 
- Detail: 
    - 예를 들어 (커맨드예시), \
        `$ ssh {ID}@{IP}` \
        `$ # 도커컨테이너 실행, 예를 들어 $ docker run ...` \
        `$ tmux new-session -d -A -s {process_name} {command}` \ 
        - 이 때 `command` 는 예를 들어 `sh your_cmd.sh` 같은게 될 수 있겠다.
    - 여기까지 하면 도커 컨테이너 안에서 tmux process 가 background 로 실행된다. 
    - `$ tmux ls` 하면 방금 실행한 tmux process 가 존재하는지 확인할 수 있다. 
    - `$ tmux attach -t {process_name}` 하면 방금 실행한 프로세스에 다시 들어가서, 거기에서 나오는 stdout 출력을 볼 수 있다. 
        - 다시 빠져나가려면 control+b 이후 d 를 해주면 된다. 
    - 따라서 도커 컨테이너를 이제 디태치(==백그라운드로 실행하도록 놔두고 나오기) 해주면 된다. 디폴트로는 control+p+q 를 해주면 빠져나올 수 있다. 
        - 다시 들어가고 싶으면 `$ docker exec -ti {your_ps_id} bash` 해주면 된다 (이 때 the ps id 는 `docker ps` 하면 알 수 있다).
- 작업이 다 끝나면 mount 한 directory 에서 바로 파일을 copy 해와도 되고, \
    혹은 `$ scp -r {ID}${IP}:{ABSOLUTE_PATH} {YOUR_LOCAL_PATH}` 와 같이 복사해와서 결과물을 확인할 수 있겠다.

## 2) jupyter notebook 이용
- **좀 더 interactive 한 개발을 할 때** 사용 중이다. 
- TBA

## 3) directory mount 와 vscode를 이용
- **원격에서 코드를 좀 더 빠르게 훑어보고 작성하고 수정할 때,** \
    여기서 소개 할 vscode 에서 원격 ip를 마운트하는 방법을 이용하면 유용했다. 
- TBA


