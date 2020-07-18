---
layout: post
title: Django에서 권장하는 사용자 모델 참조 방법(AUTH_USER_MODEL)
tags: python django
comments: true
---

Django에서 사용자(User) 모델을 참조하려면 여러 방법이 있습니다. 가장 먼저 떠오르는 것이 `from django.contrib.auth.models import User`입니다. 하지만 이렇게 직접 `User`를 참조하면 나중에 모델이 바뀌거나 하는 일이 생기면 코드의 많은 부분을 고쳐야 합니다. 유연성이 떨어집니다. 그래서 `django`는 아래 2가지 방법을 더 제공합니다.  

1. `get_user_model()`, from django.contrib.auth import get_user_model로 참조
2. `AUTH_USER_MODEL`, settings.py에서 AUTH_USER_MODEL 지정 후 from django.conf import settings로 참조

추천하는 방법은 `AUTH_USER_MODEL`을 이용하는 2번 방법입니다. `settings.py` 파일에 아래와 같이 지정합니다.

{% highlight python %}
AUTH_USER_MODEL = 'auth.User'
{% endhighlight %}

---

### 왜 AUTH_USER_MODEL을 추천하나요?

**get_user_model()**  
`get_user_model()`는 객체 인스턴스를 리턴합니다. Django 앱이 로드되는 그 순간에 실행되기 때문에 반드시 유효한 사용자 모델 객체를 리턴한다는 보장을 하지 않습니다. None이 리턴될수도 있는 것이죠. INSTALLED_APPS가 변경되는 등 캐시에 있는 앱이 다시 로드되는 경우 문제가 생길 확률은 더 높아집니다.  
  
**AUTH_USER_MODEL**  
`AUTH_USER_MODEL`는 외래키 모델을 전달할 때 문자열로 전달합니다. 외래키가 임포트될 때 모델 클래스 탐색에 실패하면 모든 앱이 로드될 때까지 실제 모델 클래스의 탐색을 미룹니다. 그렇기 때문에 항상 올바른 사용자 모델을 얻을 수 있습니다.  

---

*참고링크: https://stackoverflow.com/questions/24629705/django-using-get-user-model-vs-settings-auth-user-model*
  