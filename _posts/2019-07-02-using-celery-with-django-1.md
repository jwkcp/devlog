---
layout: post
title: 장고(Django)에서 셀러리(Celery) 사용하기 1편
tags: django celery
comments: true
---

```
이 문서는 [Celery 공식 문서](http://docs.celeryproject.org/en/latest/django/)를 번역한 것입니다.
목차는 아래와 같습니다.

목차
1. 브로커 선택하기
2. 셀러리 설치하기
3. 애플리케이션
4. 서버에서 셀러리 ```worker``` 실행하기
5. ```task``` 호출하기
6. 결과 다루기
7. 설정
8. 이제 무엇을 해야 할까요?
9. 트러블 슈팅
```
     
---

[원문주소: http://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html#first-steps]

---

이 문서는 현재 안정화된 버전인 Celery 4.3을 기준으로 설명합니다. 최신(그러나 작업 중인) 버전은 [여기](http://docs.celeryproject.org/en/master/django/first-steps-with-django.html)를 참고하세요.   
    
# 1편

---

## 셀러리 처음 시작하기
셀러리는 동작에 필요한 모든 것이 내장된 작업 큐입니다. 어떻게 동작하는지에 대해 학습할 필요없이 바로 시작할수 있도록 사용하기 쉽게 되어 있습니다. 여러 언어와 함께 확장 및 통합이 쉽게 가능하도록 베스트 프렉티스를 고려하여 디자인되었고 운영에 필요한 툴과 지원도 함께 제공됩니다.  

이 튜토리얼은 셀러리를 사용함에 있어 가장 기본적인 내용에 대해 다룹니다.  

배우는 내용:  
- 메시지 전송자인 브로커 선택과 설치 방법
- 셀러리 설치와 첫번째 ```task``` 만들기
- ```worker``` 시작과 ```task``` 호출하기
- 상태에 따른 ```task```변화 추적과 리턴 값 살펴보기

셀러리의 첫인상은 우리를 주눅들게 합니다. 이것저것 배워야 하는 것도 많아 보입니다. 하지만 걱정하지 마세요. 이 튜토리얼을 따라하면 어느새 셀러리를 쓰고 있을 것입니다. 이 문서는 고급 기능들이 여러분을 혼란스럽게 만들지 않도록 의도적으로 여러 상황과 조건을 간단하게 만들고 제약했습니다. 이 튜토리얼을 끝내면 여기서 다루지 않은 내용이라도 필요한 내용을 찾아서 쓸 수 있게 될 것입니다. 예를 들어 [장고(Django)에서 셀러리(Celery) 사용하기 2편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-2)은 셀러리의 가용성에 대해 살펴 볼 수 있는 튜토리얼입니다. 그러니 걱정말고 따라오세요. : )

---

## 브로커 선택하기
셀러리를 사용하려면 메시지를 주고 받을 수 있는 어떤 방법이 필요합니다. 우리는 이것을 메시지 브로커(Message broker)라고 부르며 일반적으로 별도의 서비스 형태를 띠고 있습니다. 메시지 브로커는 아래 열거하는 것들을 포함해 여러 선택지가 있습니다.  

### RabbitMQ
```RabbitMQ```는 필요 기능이 완비되어 있고 안정적이며 오류가 적고 설치하기 쉬운 메시지 브로커 중 하나입니다. 실 운영 환경에도 제격이지요. 셀러리와 함께 RabbitMQ를 사용하는 것에 대해 더 알고 싶다면 [여기](http://docs.celeryproject.org/en/latest/getting-started/brokers/rabbitmq.html#broker-rabbitmq)를 참고하세요.  

우분투나 데비안을 이용하고 있다면 아래와 같이 설치할 수 있고,  
```
$ sudo apt-get install rabbitmq-server
```

도커를 쓴다면 이렇게 할 수 있습니다.  
```
$ docker run -d -p 5462:5462 rabbitmq
```

명령어 실행이 끝나면 브로커가 백그라운드에서 실행되어 메시지를 전달 준비를 하고 있을 것입니다. 우분투나 데비안 리눅스를 사용하지 않을 경우 [여기](http://www.rabbitmq.com/download.html)에서 설치 방법을 찾아 볼 수 있습니다.  

### Redis
레디스 역시 필요한 기능이 모두 들어 있습니다. 다만 갑작스런 이벤트 중단 등으로 데이터 손실에 좀 더 민감합니다. 자세한 사항은 [레디스 사용하기](http://docs.celeryproject.org/en/latest/getting-started/brokers/redis.html#broker-redis)페이지를 참고하세요.  

```$ docker run -d -p 6379:6379 redis```로 도커를 통해 실행시킬 수도 있습니다.   

### 기타 브로커
이외에도 아마존 SQS를 포함해 기타 선택 가능한 브로커들이 있습니다. 전체 목록은 [여기](http://docs.celeryproject.org/en/latest/getting-started/brokers/index.html#broker-overview)에서 확인할 수 있습니다.  

---

## 셀러리 설치하기
셀러리는 PyPI(파이썬 패키지 인덱스)에 있기 때문에 pip나 easy_install 등으로 설치할 수 있습니다.  

```
$ pip install celery
```
---

## 애플리케이션
셀러리를 설치한 다음 가장 먼저 셀러리 인스턴스를 얻어야 합니다. 우리는 이것을 셀러리 애플리케이션(Celery application) 이나 그냥 짧게 앱(```app```)으로 부릅니다. 이 인스턴스가 여러분이 셀러리로 하려고 하는 모든 작업, 예를 들어 ```task```를 만들거나 ```worker```를 관리하는 등, 의 시작점으로 쓰이게 됩니다. 따라서 다른 모듈에서 사용할 수 있도록 임포트되어야 합니다.   

이 튜토리얼에서는 설정 등이 한 모듈에 모두 포함된 형태를 사용하지만 프로젝트가 좀 커지고 하면 [장고(Django)에서 셀러리(Celery) 사용하기 2편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-2)를 참조하여 전용 모듈을 구성하는 방법 사용하는게 좋습니다.  

이제 첫 번째 tasks.py를 만들어보죠.  

{% highlight python %}
from celery import Celery

app = Celery('tasks', broker='pyamqp://guest@localhost//')

@app.task
def add(x, y):
    return x + y
{% endhighlight %}

```Celery```의 첫 번째 인자로 전달된 문자열은 현재 모듈의 이름입니다. ```task```가 ```__main__``` 모듈에 정의 되어 있을 경우 이름을 이용해 자동 생성을 할 때 필요합니다.  

두 번째 인자는 여러분이 사용하려는 브로커의 URL값 입니다. 여기서는 (기본값이기도 한)RabbitMQ를 사용하고 있습니다.  

축하합니다. 여러분은 이 간단한 몇 줄로 두 숫자를 더한 값을 돌려주는 ```task```를 만들었습니다.  

---

## 서버에서 셀러리 ```worker``` 실행하기
이제 아래와 같이 ```worker``` 인자를 통해 작성한 프로그램을 실행할 수 있습니다. 정상적으로 실행되지 않을 경우 아래 트러블 슈팅 섹션을 참고하세요.  
```
$ celery -A tasks worker -loglevel=info
```

실 서버에서는 ```worker```를 백그라운드로 돌리고 싶을텐데 이렇게 하기 위해서는 supervisord 같은 플랫폼에서 제공하는 툴을 써야 합니다. 이것에 관한 내용은 [여기](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#daemonizing)를 참고하세요.  

```worker``` 관련 사용 가능한 커맨드 라인 옵션은  ```$ celery worker --help```로 볼 수 있고 다른 커맨드 라인 지원 명령은 ```$ celery help```로 볼 수 있습니다.  

---

## ```task``` 호출하기
```task```를 호출하기 위해서는 ```delay()``` 메서드를 사용해야 합니다. 이 명령은 더 많은 제어 옵션을 제공하는 ```apply_async()``` 명령의 단축 버전입니다. (```task``` 호출하기 관련 내용은 [여기](http://docs.celeryproject.org/en/latest/userguide/calling.html#guide-calling)를 참고하세요.)    
{% highlight python %}
>>> from tasks import add
>>> add.delay(4, 4)
{% endhighlight %}

기억하시죠? ```task```는 이미 여러분이 앞서 시작 시킨 ```worker```에 의해 실행되고 있습니다. ```worker```의 콘솔 출력을 확인하면 잘 돌고 있는지 여부를 확인할 수 있습니다.  

```task```의 호출은 ```AsyncResult``` 인스턴스를 리턴합니다. 이 인스턴스를 통해 ```task```의 상태나 작업이 종료되기를 기다리거나 리턴해야 하는 어떤 결과 값이 있다면 그걸 받을 수도 있습니다.(만약 ```task```가 실패한다면 예외 발생과 함께 스택 추적 정보(traceback)을 돌려줍니다.)  

이 결과값 리턴은 기본적으로 비활성화 되어 있습니다. 원격 프로세저 콜이나 ```task```의 결과를 데이터베이스에서 추적하고 싶다면 셀러리의 백엔드 부분의 URL 부분의 설정에 적절한 값이 있어야 합니다. 이 부분은 다음 섹션에서 다루도록 할께요.  

---

## 결과 다루기
여러분이 ```task```의 결과값을 받아서 어떤 처리를 하려면 셀러리가 상태값을 어딘가에 저장하거나 보낼 수 있도록 해줘야 합니다. 백엔드를 지정해줘야 한다는 말인지요. 여러분이 선택할 수 있는 백엔드의 종류는 많습니다. SQLAlchemy/Django ORM, Memcached, Redis, RPC(RabbitMQ/AMQP), 그리고 여러분이 만든 것도 있을 수 있겠네요.  

이 튜토리얼의 예제에서는 단순히 메시지를 리턴하는 *rcp* 백엔드를 사용합니다. 백엔드 설정은 ```Celery``` 객체 생성 시 인자를 통해 설정하거나 설정 모듈을 통해 설정할 수 있습니다.   
```
app = Celery('tasks', backend='rpc://', broker='pyamqp://')
```

또는 인기있는 구성인 백엔드로 Redis, 메시지 브로커로 RabbitMQ로 사용하려면 이렇게 합니다.  
```
app = Celery('tasks', backend='redis://localhost', broker='pyamqp://')
```

백엔드에 대해 더 알고 싶다면 [여기](http://docs.celeryproject.org/en/latest/userguide/tasks.html#task-result-backends)를 눌러주세요.  

이제 백엔드 설정까지 마쳤다면 ```task```를 다시 호출해보겠습니다. 이번에는 리턴 값을 변수에 받아 보겠습니다.  
```
>>> result = add.delay(4, 4)
```

```ready()``` 메서드는 ```task```가 종료되었는지 여부를 돌려줍니다.
```
>>> result.ready()
False
```

결과 완료 시까지 기다릴 수는 있지만 비동기 호출을 동기 호출로 사용하는 방법이기 때문에 거의 사용되지 않습니다.  
```
>>> result.get(timeout=1)
8
```

예외가 발생한 경우, ```get()``를 호출하면 예외가 다시 발생합니다. 이 기능을 끄려면 아래와 같이 합니다.   
```
>>> result.get(propagate=False)
``` 

예외 발생 시 아래 명령으로 원본 스택 추적 정보를 얻을 수 있습니다.  
```
>>> result.traceback
```

> **경고**  
백엔드는 결과값을 저장하거나 전송하기 위해 리소스를 사용합니다. 리소스가 해제(release)되지 않았다는 것을 보장하기 위해 ```task``` 호출 후 ```AsyncResult```인스턴스가 리턴될 때 마다 ```get()``` 또는 ```forget()```를 마지막에 호출해줘야 합니다.  

리턴 결과에 대해 더 알고 싶다면 [여기](http://docs.celeryproject.org/en/latest/reference/celery.result.html#module-celery.result)를 참고하세요.  

---

## 설정
셀러리는 마치 가전제품과 같습니다. 사용하기 위해서 별도의 큰 설정이 필요없기 때문입니다. 입력이 있고 그에 맞는 결과가 있을 뿐이지요. 입력은 브로커와 연결되어야 하고, 결과는 필요에 따라 백엔드를 설정해 사용합니다. 단순히 브로커로 입력받아 결과없이 원하는 동작을 하도록 하는 경우도 많습니다. 하지만 좀 더 자세히 들어야 보면 약간 달라집니다. 전화기를 예를 들면 거기에는 버튼도 있고, 수화기도 있지요. 천천히 뜯어보면 간단한 가전제품을 구성하고 있는 각 항목들이 있기 마련입니다. 이걸 셀러리에서는 설정(Configuration)이라고 합니다.  

기본 설정은 대부분 경우에 적합하게 구성되어 있습니다. 따로 건드릴 것이 없지요. 하지만 특별히 원하는 동작이 있다면 그에 맞게 동작할 수 있도록 많은 설정 옵션을 제공하고 있습니다. 셀러리가 제공하는 많은 옵션들에 대해 읽어보는 것은 설정을 어떻게 해야 하는지 스스로 익숙해질 수 있는 좋은 방법입니다. [여기](http://docs.celeryproject.org/en/latest/userguide/configuration.html#configuration)를 누르면 설정과 기본값에 대해 더 알 수 있습니다.  

설정은 직접 ```app```에 입력하거나 전용 모듈 파일을 만들어 적용할 수 있습니다. 예를 들어 직렬화 방법에 대해 설정하려면 ```task_serializer``` 설정을 ```app.conf.task_serializer = 'json'```과 같이 하면 됩니다.   

여러 설정을 한번에 하려면 아래와 같이 하면 됩니다.  
```
app.conf.update(
    task_serializer='json',
    accept_content=['json'],  # Ignore other content
    result_serializer='json',
    timezone='Europe/Oslo',
    enable_utc=True,
)
```

큰 프로젝트에서는 전용 설정 모듈을 사용하는 것이 좋습니다. 주기적으로 동작해야 하는 작업과 라우팅 옵션 등이 하드 코딩되어 있는 건 별로 좋은 생각이 아니기 때문이죠. 이런 설정을 한곳에 두면 좋습니다. 사용자가 원하는 작업을 적절히 통제할 수 있도록 도와줍니다. 이렇게 한곳에 설정을 모아두면 여러분이나 여러분의 시스템 관리자가 시스템에 문제가 생겼을 때 보다 단순 명료하게 원인을 분석하고 해결할 수 있습니다.  

전용 설정 구성 시 사용되는 메서드로 ```app.config_from_object()```가 있습니다. 이 메서드는 셀러리 인스턴스에게 설정을 불러오는 방법을 알려주는 역할을 합니다. 이렇게 씁니다.   
```
app.config_from_object('celeryconfig')
```

이 모듈의 이름을 보통 "celeryconfig"라고 쓰지만 뭐, 다른 이름을 써도 상관 없기는 합니다.  

위의 예에서 ```celeryconfig.py```는 현재 디렉토리나 파이썬 경로 안에 반드시 위치해야 합니다. 내부는 이렇게 생겼습니다.  

**celeryconfig.py**   

```
broker_url = 'pyamqp://'
result_backend = 'rpc://'

task_serializer = 'json'
result_serializer = 'json'
accept_content = ['json']
timezone = 'Europe/Oslo'
enable_utc = True
```

설정 파일의 각 항목이 문법에 맞게 올바르게 입력되어 있는지는 ```$ python -m celeryconfig```로 확인할 수 있습니다. 파이썬 **-m** 옵션은 뒤에 **.py** 확장자 없이 사용한다는 점 주의하세요. 설정에 관한 더 많은 내용은 [여기](http://docs.celeryproject.org/en/latest/userguide/configuration.html#configuration)를 참고하세요.  

설정이 얼마나 강력해질 수 있는지 보여주기 위해, 어떤 잘못된 동작을 전용 큐로 라우팅하는 것을 보여드리겠습니다.   

**celeryconfig.py**   

```
task_routes = {
    'tasks.add': 'low-priority',
}
```

라우팅을 하는 대신 작업 처리 비율을 제한할 수도 있습니다. 아래는 분 당 10개 작업만 처리하도록 하는 예제입니다.  

**celeryconfig.py**  
```
task_annotations = {
    'tasks.add': {'rate_limit': '10/m'}
}
```

만약 여러분이 RabbitMQ나 Redis를 브로커로 사용한다면 직접 이 설정을 런타임에서 입력할수도 있습니다.   
```
$ celery -A tasks control rate_limit tasks.add 10/m
worker@example.com: OK
    new rate limit set successfully
```

더 많은 정보를 알고 싶다면 아래 글을 참고하세요.  
- [장고(Django)에서 셀러리(Celery) 사용하기 2편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-2)
- [장고(Django)에서 셀러리(Celery) 사용하기 3편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-3)

---

## 이제 무엇을 해야 할까요?
더 배우고 싶은 분들인 [장고(Django)에서 셀러리(Celery) 사용하기 2편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-2)를 읽어보세요. 더 더 배우고 싶은 분들은 [사용자 가이드](http://docs.celeryproject.org/en/latest/userguide/index.html#guide)를 보면 좋습니다.  
---

## 트러블 슈팅
[FAQ](http://docs.celeryproject.org/en/latest/faq.html#faq)에서도 트러블 슈팅의 사례와 해결방법을 찾아볼 수 있습니다.  

### ```worker```가 시작되지 않습니다. Permission Error 발생했어요.  
- 여러분이 우분투나 데비안 계열을 리눅스 배포판을 사용하고 계시다면 ```/dev/shm``` 경로가 최근에 ```/run/shm```로 바뀌었습니다. 간단히 이 문제를 해결하는 방법은 이렇게 심볼링 링크를 생성하는 것입니다. (```# ln -s /run/shm /dev/shm```)   

- 여러분이 ```--pidfile```이나 ```--logfile```, ```--statedb``` 인자값을 설정해 사용하고 있다면 지정된 위치의 파일이나 디렉토리에 ```worker```가 읽기 혹은 쓰기 권한이 있는지 확인해봐야 합니다.   

### 결과값을 받을 백엔드가 정상적으로 동작하지 않아요. ```task```가 항상 PENDING 상태에요.   
모든 ```task```의 기본 상태는 PENDING입니다. 기본 상태값이 "unknown"과 같은 이름이었으면 좋았을 뻔 했어요. 셀러리는 ```task```가 보내졌을 때 상태값을 업데이트 하지 않습니다. 또 히스토리가 없는 모든 ```task```는 PENDING 상태라고 가정합니다.   

1. ```task```가 ```ignore_result``` 값이 활성화되어 있지 않은지 확인하세요. 이 옵션이 활성화되어 있으면 ```worker```는 상태 업데이트를 무시합니다.   

2. ```task_ignore_result``` 설정이 활성 상태가 아닌지 확인해주세요.   

3. 예전에 실행되어 돌고 있는 ```worker```가 없는지 확인해주세요.   
   - 실수로 여러 ```worker```가 실행되기 쉽기 때문에 새로운 ```worker```를 실행하기 전에 다른 실행 중인 ```worker```가 잘 종료되었는지 확인해야 합니다.   

   - 백엔드가 적절히 설정되어 있지 않은 오래된 ```worker```가 돌고 있을 수 있고 이 ```worker```가 ```task```를 가로챘을 수 있습니다.    

   - ```-pidfile``` 인자를 절대 경로로 설정하여 이런 문제를 예방할 수 있습니다.   

4. 클라이언트에 올바른 백엔드가 설정되어 있는지 확인해주세요.   
어떤 이유로 인해 클라이언트가 ```worker```와 서로 다른 백엔드로 설정되어 있을 경우, 결과를 받을 수 없게 됩니다. 백엔드가 의도한대로 잘 설정되어 있는지 아래 방법으로 확인하세요.   
```
>>> result = task.delay()
>>> print(result.backend)
```