---
layout: post
title: ModuleNotFoundError - No Module named _ctypes 에러가 발생하는 경우
tags: python 
comments: true
---

## 문제     
pyenv나 기타 방법으로 파이썬 3.7.x를 설치하려다 보면 아래와 같은 에러가 발생하는 경우가 있다.    
         
```
ModuleNotFoundError: No module named '_ctypes'
```
    
## 해결방법
libffi-dev 패키지를 설치해준다.     
     
```
# 우분투
sudo apt install libffi-dev 

# 레드헷이나 페도라 리눅스는 아래처럼 설치.
sudo apt install libffi-devel
```
      
      

