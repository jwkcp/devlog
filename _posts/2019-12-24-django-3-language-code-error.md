---
layout: post
title: Django 3.0 설치 후 LANGUAGE_CODE 관련 에러가 나는 경우 해결 방법
tags: django
comments: true
---

## 문제

Django(장고) 3.0이 얼마 전 릴리즈 되었다. Django를 신규 설치했거나 업그레이드하면 LANGUAGE_CODE 관련하여 아래와 같은 에러가 발생한다.  

`(translation.E004) You have provided a value for the LANGUAGE_CODE setting that is not in the LANGUAGES setting.`  
  
---

## 원인

Django에서 언어 설정 부분은 업데이트하면서 LANGUAGE_CODE가 참조하는 값이 달라져서 그렇다. [이 페이지](https://github.com/django/django/blob/master/django/conf/global_settings.py)를 방문해보면 Django가 기본적으로 내장하고 있는 언어 설정을 볼 수 있다. 한글도 내장된 언어 중 하나이며 `ko`로 표현한다.  

---

## 해결방법

기존의 `ko-kr`을 `ko`로 바꿔준다.  
