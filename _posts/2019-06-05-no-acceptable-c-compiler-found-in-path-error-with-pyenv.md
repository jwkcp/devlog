---
layout: post
title: pyenv로 파이썬 설치 시 no acceptable C compiler found in $PATH 에러가 발생하는 경우 
tags: python 
comments: true
---

## 문제     
pyenv로 파이썬을 설치하려고 ```pyenv install 3.6.8```과 같이 명령을 입력해보면 아래와 같은 에러가 발생하는 경우가 있다.    
     
```
configure: error: no acceptable C compiler found in $PATH
```
    
## 해결방법
build-essential 패키지를 설치해준다.    
     
```
sudo apt install build-essential
```

      

