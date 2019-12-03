---
layout: post
title: Django에서 Postgresql 데이터베이스 설치 후 No module named psycopg2 발생하는 경우 
tags: django postgresql
comments: true
---

## 문제     
Django의 DATABASES의 엔진을 django.db.backends.postgresql로 지정 후 ```python manage.py makemigrations```하면 ```No module named psycopg2```에러가 발생한다. 

    
## 해결방법
```pip install psycopg2```를 설치한다. 설치과정에서 다른 에러가 발생하는 경우, 아래의 패키지를 설치해주면 된다.
     
```
sudo apt install libpq-dev postgresql postgresql-contrib
```

      

