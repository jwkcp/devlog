---
layout: post
title: Django(장고)에서 5분만에 Sentry(센트리) 붙이기
tags: django sentry
comments: true
---

# 개요

Sentry는 여러 응용 어플리케이션의 상태나 예외를 모니터링할 수 있는 도구입니다. 굉장히 많은 플랫폼을 지원하는데 그중에서 아래 플랫폼들이 인기라고 합니다.  

- .NET
- GO
- JAVA
- JAVASCRIPT
- ANGULAR
- REACT
- VUE
- NODE.JS
- EXPRESS
- PHP
- LARAVEL
- PYTHON
- DJANGO
- FLASK
- REACT NATIVE
- RUBY
- RAILS

---

# 회원가입 및 프로젝트 생성

[Sentry 사이트](https://sentry.io)에 회원가입을 하고 왼쪽 탐색 사이드바를 보면 `Projects` 라는 항목이 있습니다. 이걸 누르고 나타나는 페이지에서 오른쪽 상단에 `Create Project`를 누르면 새 프로젝트 생성까지 완료입니다.  

---

# Django에 Sentry 설치

`sentry` `django`라는 키워드로 검색을 해보면 굉장히 많은 글이 나오는데 대부분이 `raven`을 설치해서 사용하는 글이었습니다. 그러나 이제 `raven` 대신 `sentry-sdk`를 씁니다. 이 부분만 주의하시면 달리 문제가 생길 부분이 없습니다. 기본적 설치 방법은 위 `create project`를 누르면 친절하게 나오지만 이 페이지를 인용해서 아래 간단히 설명해 보겠습니다.  

### 설치(1/2)

*raven 대신 sentry-sdk 설치*  
{% highlight django+python %}
pip install --upgrade sentry-sdk
{% endhighlight %}

### settings.py(2/2)

*아래 내용을 settings.py에 추가*  
{% highlight django+python %}
import sentry_sdk
from sentry_sdk.integrations.django import DjangoIntegration

sentry_sdk.init(
    dsn=센트리에서이부분의값은자동생성해줍니다,
    integrations=[DjangoIntegration()],
    traces_sample_rate=1.0,

    # If you wish to associate users to errors (assuming you are using
    # django.contrib.auth) you may enable sending PII data.
    send_default_pii=True
)
{% endhighlight %}

이렇게 하면 끝입니다. 혹시 이게 정말 다야? 제대로 동작하는 것 맞아? 싶은 분들은 아래 코드로 테스트해볼 수 있습니다. 그리고 (로컬에서 개발한다면)`locahost:8000/sentry-debug`를 호출해보면 `Sentry`사이트에 이 예외가 기록된 것을 볼 수 있습니다. 가입한 이메일로도 전송이 됩니다.   

*동작 테스트 코드(선택사항)*  
{% highlight django+python %}
from django.urls import path

def trigger_error(request):
    division_by_zero = 1 / 0

urlpatterns = [
    path('sentry-debug/', trigger_error),
    # ...
]
{% endhighlight %}