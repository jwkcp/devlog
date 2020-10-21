---
layout: post
title: 장고(Django)에서 Choices의 값을 템플릿 안에서 표시하는 방법
tags: django
comments: true
---

아래와 같은 방법으로 Choices의 값을 템플릿에서 표시할 수 있습니다.  

---

## 모델 예시
{% highlight django %}
class MyModel(models.Model):
    MY_CHOICES = (
        ('A', 'Apple'),
        ('B', 'Berry'),
    )

    my_type = models.CharField(max_length=1, choices=MY_CHOICES)
{% endhighlight %}

## 템플릿에서 표시하는 방법
아래 `object`는 뷰에서 넘겨주는 컨텐스트 오브젝트 이름입니다. 아래에서는 기본값 그대로 `object`로 표현했습니다. 잘 보시면 `get_필드이름_display` 형식인 것을 알 수 있습니다.   

{% highlight django %}{% raw %}
{{ object.get_my_type_display }}
{% endraw %}{% endhighlight %}
