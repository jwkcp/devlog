---
layout: post
title: DRF 사용 시 Object of type CharField is not JSON serializable 가 발생한다면
tags: django django-rest-framework
comments: true
---

## 문제

DRF(djangorestframework)를 통해 모델을 생성하고 뷰셋을 만들었는데 post 전송 시 모델의 컬럼이 나오지 않고 `TypeError: Object of type CharField is not JSON serializable` 이란 에러가 발생한다.

## 원인

모델 필드에 문제가 있어 시리얼라이즈 할 수 없다는 건데 CharField가 문제일리가 없다. 따라서 모델 생성 자체가 잘못되었을 수 있다. 예를 들어 아래와 같은 코드는 위 에러를 유발한다. 사소한 문제일 수 있으나 발견하기 쉽지 않을 수 있다.

{% highlight python %}
class News(models.Model):
title = models.CharField(max_length=100),  
 created_at = models.DateTimeField(auto_now_add=True)
updated_at = models.DateTimeField(auto_now=True)
{% endhighlight}

## 해결 방법

모델이 올바르게 생성되었는지 확인한다. 위의 예에서는 `title` 생성 시 해당 라인 맨 마지막에 콤마(,)가 있을 것을 볼 수 있다. 이러면 모델 생성이 올바르게 되지 않고 튜플을 리턴하면서 DRF가 시리얼라이즈할 때 JSON 시리얼라이즈가 불가능한 타입이란 에러가 발생한다.
