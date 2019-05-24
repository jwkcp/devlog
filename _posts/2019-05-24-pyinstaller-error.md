---
layout: post
title: pyinstaller 설치 후 OSError Python library not found 가 발생하는 경우
tags: python
comments: true
---

파이썬을 윈도우 실행파일로 만들기 위해서 pyinstaller가 사용된다.    
    
```
# pyinstaller 설치 방법
pip install pyinstaller
```
    
---

### 문제
그런데 설치 후 실행하면 아래와 같은 에러를 뿜으며 멈추는 경우가 있다.     
     
```
OSError: Python library not found: libpython3.6.so.1.0, libpython3.6m.so.1.0, libpython3.6mu.so.1.0
This would mean your Python installation doesn't come with proper library files.
This usually happens by missing development package, or unsuitable build parameters of Python installation.

* On Debian/Ubuntu, you would need to install Python development packages
  * apt-get install python3-dev
  * apt-get install python-dev
* If you're building Python by yourself, please rebuild your Python with `--enable-shared` (or, `--enable-framework` on Darwin)
```
    
읽어보면 ```libpython3.6.so.1.0```, ```libpython3.6m.so.1.0```, ```libpython3.6mu.so.1.0``` 파이썬 라이브러리를 찾을 수 없고, 이는 개발 패키지가 누락됐거나 파이썬 설치 시 알맞지 않은 빌드 파라메터를 써서 그럴 수 있단다. 그러면서 데비안이나 우분투는 ```python3-dev```, ```python-dev``` 를 설치해보라고 안내하고 있다.    
     
그런데 ```python3-dev```, ```python-dev``` 를 설치해봐도 똑같은 에러가 발생한다. T.T
     
---

### 해결방법
pyinstaller가 ```libpython3.6.so.1.0```, ```libpython3.6m.so.1.0```, ```libpython3.6mu.so.1.0``` 파일을 찾아서 쓸 수 있게 해주면 된다. 여기서는 아나콘다에 있는 lib 디렉토리를 이용한 방법을 소개한다.
     
```
# 그냥 지금 쉘에서만 하려면 아래와 같이 실행. (당연히 자기 아나콘다 경로를 입력해야 한다.)
export LD_LIBRARY_PATH=~/anaconda3/lib

# 계속 모든 쉘에 적용해서 쓰려면 .bashrc에 등록해준다.
sudo vi .bashrc
export LD_LIBRARY_PATH=~/anaconda3/lib   <-- 이 부분 맨 아래 추가
```

---

### 참고
pyinstaller와 관련된 더 자세한 정보는 [여기](https://pyinstaller.readthedocs.io)에서 찾을 수 있다.