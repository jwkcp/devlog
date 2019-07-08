---
layout: post
title: 장고(Django)에서 셀러리(Celery)로 크론탭처럼 주기적으로 작업 시키키 초간단 정리
tags: django celery
comments: true
---

# 기본 디렉토리 구조
```
PROJECT_DIR
  - PROJECT_DIR
    - celery.py
    - settings.py
  - APP_DIR
    -tasks.py
```

---

# celery.py
{% highlight python %}
from __future__ import absolute_import, unicode_literals
import os

from celery import Celery


os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'PROJECT_DIR.settings')

app = Celery('PROJECT_DIR')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()


@app.task(bind=True)
def debug_task(self):
    print('Require: {0!r}'.format(self.request))
{% endhighlight %}

---

# \_\_init\_\_.py
{% highlight python %}
from celery import app as celery_app

__all__ = ('celery_app', )
{% endhighlight %}

---

# settings.py
{% highlight python %}

CELERY_BROKER_URL = 'pyamqp://localhost:5672' 혹은 'redis://localhost:6379'
CELERY_RESULT_BACKEND = 'rpc://' 혹은 'pyamqp://localhost:5672' 혹은 'redis://localhost:6379'
CELERY_ACCEPT_CONTENT = ['application/json']
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_TIMEZONE = 'Asia/Seoul'

# 아래 CELERY_BEAT_SCHEDULE는 하드코딩으로 beat를 이용해 주기 작업을 설정하는 방법이고
# django_celery_beat를 이용하면 아래 하드코딩 없이 장고 어드민에서 동적 작업 가능
CELERY_BEAT_SCHEDULE = {
    'task-number-one': {
        'task': 'myapp.tasks.printing',
        'schedule': crontab(minute=1),
        # 'schedule': 2.0,
        # 'args': (4, 4)
    }
}
{% endhighlight %}

---

# tasks.py
{% highlight python %}
from celery import shared_task


@shared_task
def add(x, y):
    return x + y


@shared_task
def mul(x, y):
    return x * y


@shared_task
def printing():
    print("just printing")    
{% endhighlight %}

---

# 셀러리 실행
```
1. 자기가 선택한 백엔드 (RabbitMQ 또는 Redis)가 실행된 상태여야 합니다.
2. 셀러리 실행은 이렇게 - 
   : 개발 시,   
   celery -A proj worker -l info -B

   : 운영 시,   

   # 워커만
   celery -A proj worker -l info
   # beat를 이용한 주기 작업
   celery -A proj beat -l info

   # beat를 이용한 주기 작업 but, django_celery_beat 라이브러리를 이용해서 장고 어드민으로 작업 동적 생성, 조작 가능
   # django_celery_beat 사용하려면 INSTALLED_APP에 django_celery_beat 추가하고 python manage.py migrate로 주기 작업을 위한 테이블을 생성해 줌. 그리고 어드민 들어가보면 주기 작업 설정할 수 있는 테이블이 생성되어 있음! 짱!
   celery -A proj beat -l info --scheduler django_celery_beat.schedulers:DatabaseScheduler
```

# 셀러리 데몬(백그라운드)으로 돌리기
[데몬으로 셀러리(Celery) 돌리기]({% post_url 2019-07-05-celery-daemonization %}) 글을 참고하세요. 

---

# 팁
- RabbitMQ 사용 시, http://localhost:15672 에 들어가면 관리도구 페이지를 볼 수 있음
