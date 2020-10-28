---
layout: post
title: 장고(Django)에서 시간 저장을 어떻게 하는 것이 좋을까
tags: django
comments: true
---

장고(Django)에서 시간 저장은 가볍지만 무거운 문제입니다. 한번 저장되기 시작한 데이터를 모조리 수정한 다는 것은 생각보다 쉽지 않은 일이니까요.  

---

## 장고의 시간 처리 패키지
장고에서 시간을 다루는 패키지는 크게 2개입니다.  
1. `from datetime import datetime`
2. `from django.utils import timezone`  

그리고 현재 시간을 얻을 땐 아래와 같이 쓸 수 있습니다. 편의 상 현재 시간이 `2020년 10월 28일 00:34`일 경우 아래 패키지로 시간을 출력해보면 아래와 같습니다.  

### datetime 패키지
{% highlight python %}
from datetime import datetime

datetime.now()
# 결과: datetime.datetime(2020, 10, 28, 0, 33, 21, 553440)
{% endhighlight %}

### timezone 패키지
{% highlight python %}
from django.utils import timezone

timezone.now()
# 결과: datetime.datetime(2020, 10, 27, 15, 34, 16, 677327, tzinfo=<UTC>)

timezone.localtime()
# 결과: datetime.datetime(2020, 10, 28, 0, 34, 23, 350281, tzinfo=<DstTzInfo 'Asia/Seoul' KST+9:00:00 STD>)
{% endhighlight %}

---

## settings.py의 설정은 어떤 영향을 주나

보통 모델에서 생성, 수정 시간을 기록하기 위해 아래와 같이 `auto_now_add`와 `auto_now`를 사용합니다. 이렇게 하면 해당 컬럼이 생성, 수정되었을 때 시간이 자동으로 기록됩니다. 

{% highlight django+python %}
create_date = models.DateTimeField(auto_now_add=True)
update_date = models.DateTimeField(auto_now=True)
{% endhighlight %}

이때 아래와 같이 `settings.py`의 설정을 해주었다면 DB에는 어떤 값이 들어갈까요. DB마다 다를 수 있지만 제가 경험한 DB에서는 UTC 시간이 들어갔습니다. `TIME_ZONE` 설정이 `Asia/Seoul`인데도 그렇습니다. 이걸 강제로 한국 시간으로 저장하기 위해 `USE_TZ`을 `False`로 설정하는 방법을 쓰기도 합니다. 하지만 개인적으로 추천하고 싶은 방법은 아닙니다.  

{% highlight django+python %}
LANGUAGE_CODE = 'ko-kr'
TIME_ZONE = 'Asia/Seoul'
{% endhighlight %}

---

## 추천하는 시간 저장 방법
개인적으로 시간 저장은 UTC로 하고 보여주기를 한국이면 한국 등 각 나라의 위치에 맞게 보여주는 것을 추천합니다. 장고의 경우 위와 같이 TIME_ZONE 설정을 해두면 DB에 UTC로 저장되어 있다고 하더라도 `object.update_date`와 같이 템플릿에서 보여주면 `settings.py`의 `TIME_ZONE`인 한국 시간에 맞게 자동으로 변환되어 보여집니다. 수동으로 `DateTimeField`를 업데이트해야 한다면 `timezone.now()`를 쓰시면 되고, 특별히 한국 시간(TIME_ZONE의 시간)이 필요하다면 `timezone.localtime()`을 쓰시면 됩니다.  