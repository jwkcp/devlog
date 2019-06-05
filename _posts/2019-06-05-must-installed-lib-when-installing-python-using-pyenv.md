---
layout: post
title: pyenv로 파이썬 설치 시 발생하는 오류와 해결 방법 (이거 하나면 끝!)
tags: python 
comments: true
---

## 문제     
pyenv로 파이썬 설치 시 아래와 같은 에러가 나오는 경우가 있다.    
```
WARNING: The Python bz2 extension was not compiled. Missing the bzip2 lib?
WARNING: The Python readline extension was not compiled. Missing the GNU readline lib?
WARNING: The Python sqlite3 extension was not compiled. Missing the SQLite3 lib?
```
      
그리고 이런 에러도 종종 나온다.    
```
configure: error: no acceptable C compiler found in $PATH
ModuleNotFoundError: No module named '_ctypes'
```

---

## 해결방법
아마 aws 등으로 django를 호스팅하기 위해 새로 서버를 만들었을 것이다. 그리고 파이썬 설치를 하는 과정에서 이 경과와 에러들을 만나는 경우가 많다. 왜냐하면 관련 라이브러리가 설치되어 있지 않은 상태에서 파이썬을 설치하려 했기 때문에 그렇다. 아래 각 경고와 에러 별로 설치해야 하는 라이브러리에 대해 알아보고 문제를 해결해보자. 우분투 기준으로 설명한다.      

---
      
### STEP 1
아래 에러가 발생하면 파이썬이 설치되는 도중 멈춘다.     
     
```
# 해결방법: sudo apt install build-essential
configure: error: no acceptable C compiler found in $PATH
```
```
# 해결방법: sudo apt install libffi-dev
ModuleNotFoundError: No module named '_ctypes'
```

---
      
### STEP 2
아래는 에러가 아닌 경고다. 파이썬 설치는 되지만 문제가 있다는 말로 무시하고 그냥 지나가면 ```python manage.py makemigrations```를 할 때 에러를 만나게 된다. 어차피 나중에 이 경고를 해결하고 파이썬을 재설치 해야 하기 때문에 무시하지 말고 해결하고 가자.    
    
```
WARNING: The Python bz2 extension was not compiled. Missing the bzip2 lib?
```
해결방법: sudo apt install libbz2-dev    
(설치하는 김에 zlib1g-dev도 함께 설치하자. 둘 다 압축 관련 라이브러리다.)    

```
WARNING: The Python readline extension was not compiled. Missing the GNU readline lib?
```
해결방법: sudo apt install libreadline-dev       
(CLI에서 편집 및 입력 관련 라이브러리다.)    

```
WARNING: The Python sqlite3 extension was not compiled. Missing the SQLite3 lib?
```    
해결방법: sudo apt install libsqlite3-dev      
(SQLite 관련 라이브러리다.)     
    
