---
layout: post
title: apt에서 ppa 저장소 추가 후 "Unable to locate package .." 에러가 발생하는 경우
tags: ubuntu
comments: true
---
    
php7.3과 같이 ppa를 추가해야 하는 경우 ondrej/php 저장소를 자주 이용됩니다. 아래와 같이 추가 할 수 있습니다.    
(혹시 설치가 안된다면 ```sudo apt install software-properties-common```을 실행하여 ```add-apt-repository```기능을 이용할 수 있게 하자.)     
    
```
$ sudo add-apt-repository ondrej/php
```
    
그런데 이렇게 한 후에도 ```sudo apt install php7.3```을 하면 아래와 같이 에러가 발생하는 경우가 있습니다. 왜 일까요?     
    
```
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package php7.3
E: Couldn't find any package by glob 'php7.3'
E: Couldn't find any package by regex 'php7.3'
```
      
### 해결방법
```sudo apt update```를 통해 저장소의 패키지 인덱스를 업데이트 해줘야 합니다. 위의 ```sudo add-apt-repository ondrej/php``` 명령은 저장소를 등록만 해 준것이지 패키지의 인덱스까지 업데이트를 해준 것이 아니기 때문에 ```sudo apt install php7.3```를 실행하더라도 패키지를 찾을 수 없어 에러가 발생하는 것입니다.
     