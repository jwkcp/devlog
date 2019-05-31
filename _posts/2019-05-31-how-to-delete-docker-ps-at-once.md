---
layout: post
title: 터미널 명령으로 여러 개의 도커(Docker) 컨테이너 한 번에 지우기
tags: docker ssh
comments: true
---
     
도커(Docker) 이미지와 컨테이너를 여러 개 생성하면서 연습하다보면 불필요하게 이미지와 컨테이너가 많이 늘어난다. 터미널 명령으로 한 번에 삭제하는 방법을 소개한다. 세부 조건들은 아래 명령을 응용해서 쓰면 된다.    
     
## 도커 이미지, 컨테이너 조회
```
# 도커 이미지 조회
$ sudo docker images

# 도커 실행중인 컨테이너만 조회
$ sudo docker ps

# 도커 모든 컨테이너 조회
# sudo docker ps -a
```
      
## 도커에 이미지, 컨테이너 삭제하기
도커에서 이미지를 삭제하려고 하면, 컨테이너가 있어서 안된다고 나오는 경우가 있다. 컨테이너를 지우려고 보니 연습 중이라 너무 많이 생성되어 있다. 이 때 터미널 명령 awk와 파이프라인을 이용하면 편하게 지울 수 있다.    
     
```
$ sudo docker ps -a | awk 'NR>1 {print $1}' | xargs sudo docker rm
```
     
위 명령이 실행되는 순서는 파이프(|) 기준으로 순차적이다.     
```sudo docker ps -a```이 실행한 결과를 ```awk 'NR>1 {print $1}'```에 넘기고, 여기서 나온 결과를 ```sudo docker rm```의 매개변수로 쓴다(xargs) NR>1은 헤더를 빼고 출력하라는 말이다.    
     
예를 들어, 아래와 같이 2개의 중지된 도커 컨테이너가 있다면
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
84828d59737d        hello:0.1           "/bin/bash"         24 minutes ago      Exited (0) 22 minutes ago                       elastic_hodgkin
7039986064e7        hello:0.1           "/bin/bash"         25 minutes ago      Exited (0) 25 minutes ago                       xenodochial_villani
```
     
```sudo docker ps -a | awk 'NR>1 {print $1}'```명령 결과는 아래와 같다. (```print $2```로 하면 두 번째 열이 나오게 된다.)
```
84828d59737d
7039986064e7
```
    
만약 ```NR>1``` 이란 조건을 빼면 아래와 같이 나온다. 이건 우리가 원하는 결과가 아니다.    
```
CONTAINER
84828d59737d
7039986064e7
```
    
이 결과를 ```xargs``` 명령을 이용해 ```sudo docker rm```의 매개변수로 넘기는 것이다.    
     