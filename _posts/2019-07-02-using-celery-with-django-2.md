---
layout: post
title: 장고(Django)에서 셀러리(Celery) 사용하기 2편
tags: django celery
comments: true
---

```
이 문서는 [Celery 공식 문서](http://docs.celeryproject.org/en/latest/django/)를 번역한 것입니다.
목차는 아래와 같습니다.

목차
1. 장고에서 셀러리 사용하기
2. 확장 라이브러리
3. 워커(worker) 프로세스 시작하기
4. 그 다음엔 무엇을 해야 할까요?
```
     
---

[원문주소: http://docs.celeryproject.org/en/latest/django/first-steps-with-django.html]

---

이 문서는 현재 안정화된 버전인 Celery 4.3을 기준으로 설명합니다. 최신(그러나 작업 중인) 버전은 [여기](http://docs.celeryproject.org/en/master/django/first-steps-with-django.html)를 참고하세요.   

---

**이 글의 시리즈 글:**  
- [장고(Django)에서 셀러리(Celery) 사용하기 1편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-1)   
- [장고(Django)에서 셀러리(Celery) 사용하기 2편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-2)   
- [장고(Django)에서 셀러리(Celery) 사용하기 3편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-3)   
    
# 2편

---

## 장고(Django) 프로젝트에서 셀러리(Celery) 사용하기
     
> 이전 버전까지는 셀러리를 장고에서 사용하려면 별도의 라이브러리가 필요했으나 셀러리 3.1 이후로는 그럴 필요가 없게 됐습니다. 장고가 기본적으로 지원되기 때문에 이 문서에서는 셀러리와 장고를 통합하는 기본적인 방법에 대해서만 설명합니다. 장고를 사용하든, 사용하지 않든 모두 똑같은 API를 쓰기 때문에 [셀러리 처음 시작하기](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-1) 튜토리얼을 먼저 읽어보는 것을 추천합니다. 어느 정도 동작하는 예제가 있다면 [더 나아가기](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-3) 을 읽어보는 것도 좋습니다.  

> 알아두기:   
셀러리 4.0은 장고 1.8 또는 그 이상의 최신 버전을 지원합니다. 장고 1.8 미만의 버전을 사용하시는 분은 셀러리 3.1 버전을 사용해주세요.   

장고 프로젝트에서 셀러리를 사용하기 위해서는 ```app``` 이라고 부르는 셀러리 라이브러리 인스턴스를 가장 먼저 정의해야 합니다.    

이런 장고 프로젝트 레이아웃이 있다면:   

```
- proj/
  - manage.py
  - proj/
  - __init__.py
  - settings.py
  - urls.py
```

셀러리 인스턴스 정의를 위해 proj/proj/celery.py 위치에 파일을 만드세요. 다른 위치에 celery.py 파일을 만들 수도 있지만 이 위치에 만들기를 권장합니다. 아래 celery.py 파일의 내용을 살펴보겠습니다.  

**file:** ```proj/proj/celery.py```   

{% highlight python %}
from __future__ import absolute_import, unicode_literals

import os
from celery import Celery

#'셀러리' 프로그램을 위해 기본 장고 설정파일을 설정합니다.
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'proj.settings')

app = Celery('proj')

#여기서 문자열을 사용하는 것은 워커(worker)가 자식 프로세스로 설정 객체를 직렬화(serialize)하지 않아도 된다는 뜻입니다.  
#뒤에 namespace='CELERY'는 모든 셀러리 관련 설정 키는 'CELERY_' 라는 접두어를 가져야 한다고 알려줍니다.  
app.config_from_object('django.conf:settings', namespace='CELERY')  

#등록된 장고 앱 설정에서 task를 불러옵니다.  
app.autodiscover_tasks()  

@app.task(bind=True)
def debug_task(self):
    print('Request: {0!r}'.format(self.request))
{% endhighlight %}

그 다음 ```proj/proj/__init__.py```에서 이 ```app```을 임포트합니다. 이렇게 하면 장고가 시작될 때 @shared_task 데코레이터가 사용할 앱을 자동으로 로드시켜 줍니다.  

**file:** ```proj/proj/__init__.py```       

{% highlight python %}
from __future__ import absolute_import, unicode_literals

#이 구문은 shared_task를 위해 장고가 시작될 때 app이 항상 임포트 되도록 하는 역할을 합니다.
from .celery import app as celery_app

__all__ = ('celery_app', )
{% endhighlight %}

한 가지 알아두어야 하는 점은 지금 이 문서에서 다루는 예제 프로젝트의 구성이나 레이아웃은 규모있는 프로젝트에 적합하다는 것입니다. 간단한 프로젝트에서는 이 방법을 쓰지 말고 [셀러리 처음 시작하기](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-1)의 예와 같이 ```app```과 ```task```를 함께 정의하는 간단한 방법이 적합합니다.  

이제 각 구문들이 무슨 역할을 하는지 하나씩 뜯어 봅시다. 아래는 우리가 작성한 ```celery.py``` 모듈이 라이브러리들과 충돌하여 문제가 발생하지 않도록 돕습니다. 
{% highlight python %}
from __future__ import absolute_import
{% endhighlight %}
   
커맨드라인에서 셀러리를 편하게 사용하기 위해 ```DJANGO_SETTINGS_MODULE``` 환경 변수를 기본 값으로 설정했습니다.  
이 구문이 꼭 필요한 것은 아니지만 항상 설정(settings) 모듈을 셀러리 프로그램에 전달하도록 해주기 때문에 시간을 절약할 수 있습니다. 다음에 나올 ```app``` 인스턴스 생성보다 반드시 앞서 설정해야 한다는 점, 기억하세요.  
{% highlight python %}
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'proj.settings')
{% endhighlight %}

인스턴스를 만듭니다. 인스턴스를 여러 개 만들 수 있지만 장고를 사용하는 경우에는 굳이 그럴 필요없이 하나로 충분합니다.  
{% highlight python %}
app = Celery('proj')
{% endhighlight %}
  
장고 설정을 셀러리 설정으로 사용하기 위한 구문입니다. 셀러리용으로 별도 설정을 만들지 않고 장고의 설정 파일을 바로 읽어 사용할 수 있습니다. 별도 설정으로 하고 싶다면 그렇게 해도 됩니다.  
네임스페이스에 대문자로 표시된 것은 셀러리 설정 옵션이 반드시 대문자로 지정되어야 하며, ```CELERY_```로 시작해야 한다는 뜻입니다. 예를 들어 ```task_always_eager``` 항목의 경우 ```CELERY_TASK_ALWAYS_EAGER```로 표시되어야 하고, ```broker_url```의 경우 ```CELERY_BROKER_URL```과 같이 써야 합니다.  

문자열을 전달하는 대신에 설정 객체를 직접 전달하는 방법을 쓸 수도 있지만 워커(worker)가 객체를 직렬화하지 않아도 되기 때문에 문자열 전달 방식이 좀 더 낫습니다. ```CELERY_``` 네임스페이스도 필수적이진 않지만 장고 설정과 구분이 되기도 하고 장고 설정을 덮어쓸 염려도 없기 때문에 사용을 권장합니다.  
{% highlight python %}
app.config_from_object('django.conf:settings', namespace='CELERY')
{% endhighlight %}

보통 많은 경우에 재사용 가능한 ```app```을 만들기 위해서는 모든 ```task```를 별도의 분리된 tasks.py 모듈에 정의하는 것이 좋습니다. Celery는 아래 구문을 통해 ```task```를 자동으로 탐색합니다.  
{% highlight python %}
app.autodiscover_tasks()
{% endhighlight %}

위 구문으로 셀러리는 설치된 ```app```에서 ```task```를 자동으로 탐색합니다. 아래는 일반적으로 ```tasks.py```가 어떤 디렉토리 구조로 위치하는지 보여줍니다. 이렇게 하면 개별 모듈을 일일이 ```CELERY_IMPORTS```에 추가할 필요 없이 자동으로 인식합니다.  
```
- app1/
  - tasks.py
  - models.py
- app2/
  - tasks.py
  - models.py
```

마지막으로 ```debug_task```의 셀러리 3.1에서 새로 소개된 ```bind=True```옵션을 사용하면 ```task``` 인스턴스를 참조해 자신의 request 정보를 쉽게 뽑아낼 수 있습니다.

---

## @shared_task 데코레이터 사용하기
여러분이 작성한 ```task```는 아마 십중팔구 재사용 가능한 앱 안에 있을 가능성이 높고 각 재사용 가능 앱은 프로젝트 자체에 의존성이 없으므로 ```celery.py```의 ```app```인스턴스를 직접 임포트할 수 없습니다. ```@shared_task``` 데코레이터는 어떤 구체화된 ```app``` 인스턴스 없이도 ```task```를 만들 수 있게 해줍니다.  

```demoapp/tasks.py:```   
{% highlight python %}
from __future__ import absolute_import, unicode_literals
from celery import shared_task

@shared_task
def add(self):
    return x + y

@shared_task
def mul(x, y):
    return x * y

@shared_task
def xsum(numbers):
    return sum(numbers)
{% endhighlight %}

> **이것도 보세요:**  
장고 예제에 대한 전체 소스 코드는 [여기](https://github.com/celery/celery/tree/master/examples/django/)를 참고하세요.  

> **상대적으로 동작하는 임포트:**  
```task``` 모듈을 임포트할 때 일관성이 있어야 합니다. 예를 들어, ```project.app```과 같이 ```INSTALLED_APPS```에 적었다면 ```task``` 임포트시에도 ```project.app```으로 적어야 합니다. 그렇지 않으면 결국에는 이름 때문에 문제가 발생하게 될 것입니다. (참고: [http://docs.celeryproject.org/en/latest/userguide/tasks.html#task-naming-relative-imports](http://docs.celeryproject.org/en/latest/userguide/tasks.html#task-naming-relative-imports))     

---

# 확장 라이브러리
## ```django-celery-results```
result backend(결과를 받아볼 대상을 지칭합니다.)로 장고의 ORM/Cache 사용하기

```django-celery-results```는 ```result backend```, 그러니까 작업 후 결과를 돌려 줄 대상으로 장고의 ORM이나 Cache(캐시) 프레임워크를 사용할 수 있게 해주는  확장 라이브러리입니다.  

이 라이브러리를 사용하기 위해서는 아래 절차되로 진행하면 됩니다.  

1.  확장 라이브러리 [django-celery_results](https://pypi.python.org/pypi/django-celery-results/)를 설치합니다.
{% highlight python %}
pip install django-celery-results
{% endhighlight %}

2. 장고의 ```settings.py```의 ```INSTALLED_APP```에 ```django_celery_results```를 추가합니다. 하이픈이 아니라 언더스코어를 사용하고 있는 점에 주의하세요.
{% highlight python %}
INSTALLED_APPS = (
    ...,
    'django_celery_results',
)
{% endhighlight %}

3. 데이터베이스 마이그레이션을 통해 셀러리가 사용할 데이터베이스 테이블을 생성해줍니다.   
{% highlight python %}
python manage.py migrate celery_results
{% endhighlight %}
   
4. ```django-celery-results``` 백엔드를 사용하도록 셀러리를 설정합니다.  
셀러리가 장고의 ```settings.py```를 사용하고 있다는 가정 하에 아래의 설정을 추가해줍니다.   
{% highlight python %}
CELERY_RESULT_BACKEND = 'django-db'
{% endhighlight %}
 
만약 캐시를 백엔드로 사용한다면 아래와 같이 설정합니다.   
{% highlight python %}
CELERY_CACHE_BACKEND = 'django-cache'
{% endhighlight %}

## ```django-celery-beat```
Admin 인터페이스와 함께 데이터베이스 지원 가능한 주기 작업 사용하기  

[커스텀 스케줄러 클래스 사용하기](http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html#id8)를 참고하세요.  

---

# 워커(worker) 프로세스 시작하기
실제 운영 환경에서는 셀러리 워커(worker)가 데몬과 같이 백그라운드로 동작하게 하고 싶으실 겁니다. 여기 [데몬으로 동작시키기](https://devlog.jwgo.kr/2019/07/05/celery-daemonization/)를 참고하세요. 하지만 단순 테스트나 개발 시에는 셀러리 워커 관리 커맨드를 사용해 워커(worker) 인스턴스를 시작시키는 것도 유용한 방법입니다.  

{% highlight python %}
celery -A proj worker -l info
{% endhighlight %}

사용 가능한 모든 커맨드 라인 옵션을 보려면 ```help``` 커맨드를 사용하세요.  
{% highlight python %}
celery help
{% endhighlight %}

---

## 그 다음엔 무엇을 해야 할까요?
셀러리의 기본 동작에 대해 더 알고 싶다면 [남은 튜토리얼](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-3)을 더 학습해보세요. 그리고 난 다음 [사용자 가이드](http://docs.celeryproject.org/en/latest/userguide/index.html#guide)를 공부하시면 됩니다.