---
layout: post
title: 파이참에서 로컬 앱(app)을 불러올 때 'Unresolved reference ...' 에러가 발생하는 경우
tags: pycharm
comments: true
---
     
## 문제
파이참(Pycharm)에서 로컬에서 만든 앱(app)의 모델 클래스 등을 아래와 같이 불러올 때 빨간 밑줄이 생기면서 'Unresolved reference ...'하는 에러가 발생하는 경우가 있다.   
    
```
# 이런식으로 불러올 때 myapp밑에 그리고 myclassname 밑에 빨간 밑줄이 그어진다.
from myapp.models import myclassname
```
    
```python manage.py runserver```를 통해 실행해보면 정상적으로 앱 경로를 찾는 것 보니 후순위로 앱의 경로를 탐색해서 어쨌든 탐색에 성공하는 것 같은데 파이참 코드 에디터에서는 못찾는 것 같았다.    
  
---
   
## 해결방법
파이참에서 소스코드 루트 디렉토리를 ```Sources Root```로 지정해주면 빨간 밑줄이 사라진다. 예를 들어, ```python django-admin.py startproject myproject```로 생성했다면 myproject를 ```Sources Root```로 지정해준다. ```Sources Root```로 지정하는 방법은 해당 디렉토리에서 오른쪽 버튼을 누른 후, ```Mark Directory As```에서 ```Sources Root```를 선택하면 된다.
     