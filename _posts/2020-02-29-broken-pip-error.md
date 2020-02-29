---
layout: post
title: No module named 'pip' 에러와 함께 pip가 갑자기 안되는 경우 
tags: python
comments: true
---

## 문제

몇 분 전까지만 해도 잘 되던 `pip`가 갑자기 `No module named 'pip'`메시지를 뿌리면서 실행되지 않는다.  

---  

## 원인

아마 직전에 pip를 업그레이드 `pip install --upgrade pip` 하려고 했을 것이다. 대부분 `pip`가 동작하지 않는 문제는 이 과정에서 일어난다. `pip`를 업그레이드하려면 삭제 후 새버전을 설치하게 되는데 삭제 후 재설치가 정상적으로 되지 않은 것이다.  

대략 이런 메시지가 나온다.  

> ModuleNotFoundError: No module named 'pip'  

(개인적 경험을 빌어보면) 맥이나 리눅스보다는 윈도우에서 이런 경우가 더 자주 발생하는 것 같다. 아마 윈도우 터미널(cmd.exe)를 관리자 권한으로 실행하지 않은 상태에서 `pip`를 업그레이드하려고 하면 삭제는 문제없이 잘 되는데, 설치 시 권한 문제로 실패하면서 `pip`가 삭제된 상태로 남아 이런 현상이 자주 생기는 것으로 보인다.  

---  

## 해결방법  

`pip`를 재설치해주면 된다. [PyPA](https://www.pypa.io)를 이용하면 쉽다.  

> [PyPA](https://www.pypa.io)라고 Python Packaging Authority 사이트가 있다. 파이썬 패키징에 사용되는 핵심 프로젝트 묶음을 유지, 관리하는 워킹 그룹이다.  

아래와 같이 `curl`을 이용해 다운받고,  

`curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`  

아래 명령을 실행해 설치 할 수 있다.  

`python get-pip.py`  

---

## 더 알아보기

[더 자세한 PyPA 설치 안내 페이지](https://pip.pypa.io/en/stable/installing/)
