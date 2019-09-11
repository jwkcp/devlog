---
layout: post
title: LoginRequiredMixin 이 정상적으로 동작하지 않을 때
tags: django
comments: true
---

## 문제

장고(Django)에서 로그인이 필요한 뷰를 처리할 때 함수형 뷰의 경우 데코레이터를, 클래스 뷰의 경우 믹스인을 쓰게 된다. 여기서는 클래스 뷰를 사용할 때 LoginRequiredMixin이 안먹을 때다. 분명히 아래와 같이 잘 넣어줬는데 에러가 뜬다. 왜 그럴까?

{% highlight python %}
from django.contrib.auth.mixins import LoginRequiredMixin

... 중략 ...

class MyClass(CreateView, LoginRequiredMixin):
... 중략 ...

{% endhighlight %}

---

## 원인

이미 눈치챈 분들도 계시겠지만 순서 때문이다. LoginRequiredMixin은 as_view 함수를 오버로딩하여 내부에서 view를 login_required로 싸주는 역할을 하기 때문에 첫번째 매개변수로 **LoginRequiredMixin**을 써줘야 한다.

위 코드를 다시 써보면 아래와 같다.

{% highlight python %}
from django.contrib.auth.mixins import LoginRequiredMixin

... 중략 ...

class MyClass(LoginRequiredMixin, CreateView):
... 중략 ...

{% endhighlight %}

---

## 참고

**Django에서 사용자 인증 여부를 확인하는 2가지 방법**에 대해 간단하고 쉬운 설명이 필요하다면 [여기]({% post_url 2016-11-26-django-login-validation-check %})를 더 살펴보세요.
