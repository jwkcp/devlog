---
layout: post
title: 장고(Django)에서 셀러리(Celery) 사용하기 3편
tags: django celery
comments: true
---

```
이 문서는 [Celery 공식 문서](http://docs.celeryproject.org/en/latest/django/)를 번역한 것입니다.
목차는 아래와 같습니다.

목차
1. 여러분이 만든 애플리케이션에서 셀러리 사용하기
2. ```task``` 호출하기
3. 캔버스(Canvas): 워크플로우 디자인하기
4. 라우팅
5. 원격 조종
6. 타임존
7. 최적화
8. 이제 무엇을 해야 할까요?
```
     
---

[원문주소: http://docs.celeryproject.org/en/latest/getting-started/next-steps.html#next-steps]

---

이 문서는 현재 안정화된 버전인 Celery 4.3을 기준으로 설명합니다. 최신(그러나 작업 중인) 버전은 [여기](http://docs.celeryproject.org/en/master/django/first-steps-with-django.html)를 참고하세요.   

---

**이 글의 시리즈 글:**  
- [장고(Django)에서 셀러리(Celery) 사용하기 1편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-1)   
- [장고(Django)에서 셀러리(Celery) 사용하기 2편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-2)   
- [장고(Django)에서 셀러리(Celery) 사용하기 3편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-3)   
    
# 3편

--

[셀러리 처음 시작하기](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-1) 가이드는 의도적으로 적은 범위로 만들어졌습니다. 이 가이드에서는 여러분이 만든 애플리케이션이나 라이브러리에 셀러리를 적용하는 것을 포함해 셀러리가 어떤 기능을 제공할 수 있는지 더 자세히 보여줄 계획입니다.  
 
이 가이드는 그 목적 상 셀러리의 모든 기능이나 베스트 프렉티스를 다 나열하지 않습니다. 그렇기 때문에 [사용자 가이드](http://docs.celeryproject.org/en/latest/userguide/index.html#guide)를 더 읽어보시길 추천 드립니다.  

---

## 여러분이 만든 애플리케이션에서 셀러리 사용하기
### 프로젝트 구성

#### 프로젝트 레이아웃
```
proj/__init__.py
  /celery.py
  /tasks.py
```

#### proj/celery.py
{% highlight python %}
from __future__ import absolute_import, unicode_literals
from celery import Celery

app = Celery('proj', 
             broker='amqp://',
             backend='amqp://',
             include=['proj.tasks'])

#(선택) 추가설정
app.conf.update(
    result_expires=3600,
)

if __name__ == '__main__':
    app.start()
{% endhighlight %}

이 모듈에서 Celery의 인스턴스를 생성했고 app으로 참조하고 있습니다. 프로젝트 내에서 셀러리를 사용하려면 이 인스턴스를 임포트하면 됩니다.  

- ```broker``` 인자는 사용할 브로커의 URL을 입력합니다.   
브로커에 대해 더 알고 싶다면 [여기](http://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html#celerytut-broker)를 참고하세요.
 
- ```backend``` 인자는 사용할 백엔드의 URL을 지정합니다.  
백엔드는 ```task```의 상태와 결과를 추적하는데 이용됩니다. 기본적으로 결과가 비활성화되어 있지만 여기서는 나중에 어떻게 결과를 탐색하는지 보여주기 위해 RPC 백엔드를 사용했습니다. 각각은 모두 장단점을 가지고 있지만, 결과가 꼭 필요한 것이 아니라면 비활성 상태로 두는 것이 더 좋습니다. 결과는 각각의 ```task```에서 ```@task(ignore_result=True)```옵션 설정을 통해서도 비활성화 될 수 있습니다. 결과를 보존하고 다루는 법이 궁금하다면 [여기](http://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html#celerytut-keeping-results)를 참고하세요.   

- ```include``` 인자 값은 `worker`가 시작되었을 때 포함될 모듈의 목록입니다. `worker`가 ```task```를 찾도록 하기 위해 여기에 작성한 ```task```를 등록할 필요가 있습니다.  

#### proj/tasks.py
{% highlight python %}
from __future__ import absolute_import, unicode_literals
from .celery import app

@app.task
def add(x, y):
    return x + y

@app.task
def mul(x, y):
    return x * y

@app.task
def xsum(numbers):
    return sum(numbers)
{% endhighlight %}

### ```worker``` 시작하기
```celery```명령은 ```worker```를 시작하는데 사용됩니다. 이를 위해서는 앞서 언급한 proj 디렉토리에서 실행해야 합니다.   
  
```
celery -A proj worker -l info
```

```worker```가 시작되면 아래와 같은 배너와 메시지를 볼 수 있습니다.  
```
-------------- celery@halcyon.local v4.0 (latentcall)
---- **** -----
--- * ***  * -- [Configuration]
-- * - **** --- . broker:      amqp://guest@localhost:5672//
- ** ---------- . app:         __main__:0x1012d8590
- ** ---------- . concurrency: 8 (processes)
- ** ---------- . events:      OFF (enable -E to monitor this worker)
- ** ----------
- *** --- * --- [Queues]
-- ******* ---- . celery:      exchange:celery(direct) binding:celery
--- ***** -----

[2012-06-08 16:23:51,078: WARNING/MainProcess] celery@halcyon.local has started.
```

- ```broker```는 셀러리 모듈 안에서 broker 인자로 지정한 URLdlqslek. ```-b``` 커맨드 라인 옵션을 통해 다른 브로커를 지정할 수 있습니다.   

- ```Concurrency```는 한 ```task```가 수행되고 있다면 새로운 ```task```가 기다를 수 밖에 없는데 이럴 때 ```task```를 동시에 수행하기 위해 사전에 포크(fork)된 워커 프로세스의 수입니다.   

동시성 수행을 위한 기본 값은 코어를 포함한 서버의 CPU 개수로 되어 있고, ```celery worker -c``` 옵션을 통해 원하는 숫자를 지정할수도 있습니다. 권장되는 숫자는 특별히 없고, 최적의 숫자는 각 환경의 특징마다 다를 수 있습니다. 하지만 여러분이 작성한 ```task```가 특별히 디스크 I/O가 많다면 이 숫자를 늘리는 것을 고려해볼 수 있습니다. 실험에 따르면 이런 경우 서버의 CPU 개수의 두 배 이상 설정하는 경우 성능이 떨어짐과 동시에 그 효과를 보기 어려웠습니다.  

셀러리는 기본적으로 사전에 포크(port)된 풀(pool)를 포함해 Eventlet, Gevent, 싱글 스레드를 모두 지원합니다. (참고 [동시성](http://docs.celeryproject.org/en/latest/userguide/concurrency/index.html#concurrency))  

- ```Events```는 ```worker```에서 어떤 액션이 발생했을 때 모니터링 메시지를 보내는데 이것에 관한 옵션입니다. ```celery event```나 실시간 셀러리 모니터링 도구인 ```Flower```같은 프로그램에 사용할 수 있습니다. 모니터링에 관련된 내용이 더 알고 싶다면 [여기](http://docs.celeryproject.org/en/latest/userguide/monitoring.html#guide-monitoring)를 참고하세요.   

- ```Queues```는 ```worker```가 처리할 대기열 목록입니다. ```worker```는 여러 큐에서 한번에 작업을 처리하게 될 수 있고, 이 때 서비스 품질, 이슈 사항의 분리, 우선 순위 지정 등이 포함된 라우트 메시지가 사용됩니다. 라우팅에 대한 모든 것은 [여기](http://docs.celeryproject.org/en/latest/userguide/routing.html#guide-routing)를 참고하세요.  

아래 help 플레그를 통해 사용가능한 명령어 목록을 얻을 수 있습니다.
```
celery worker -help
```
옵션에 관한 더 자세한 사항은 [워커 가이드](http://docs.celeryproject.org/en/latest/userguide/workers.html#guide-workers)를 살펴보세요.  

#### ```worker``` 중지하기
```worker```를 중지하기 위해선 간단히 ```Ctrl + c```를 누르면 됩니다. 지원하는 신호(signal)에 대한 목록은 [워커 가이드](http://docs.celeryproject.org/en/latest/userguide/workers.html#guide-workers)에 있습니다.   

#### 백그라운드로 작업하기
실 운영 환경에서는 ```worker```를 백그라운드로 동작시킥고 싶을 것입니다. 이에 관한 내용은 [데몬으로 만들기](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#daemonizing) 튜토리얼을 참고하세요.  

하나 이상의 ```worker```를 백그라운드로 동작시키기 위해서는 ```celery multi``` 명령을 사용합니다.  
```
$ celery multi start w1 -A proj -l info
celery multi v4.0.0 (latentcall)
> Starting nodes...
    > w1.halcyon.local: OK
```

이렇게 다시 시작할수도 있고:  
```
$ celery  multi restart w1 -A proj -l info
celery multi v4.0.0 (latentcall)
> Stopping nodes...
    > w1.halcyon.local: TERM -> 64024
> Waiting for 1 node.....
    > w1.halcyon.local: OK
> Restarting node w1.halcyon.local: OK
celery multi v4.0.0 (latentcall)
> Stopping nodes...
    > w1.halcyon.local: TERM -> 64052
```

중지할수도 있습니다.  
```
$ celery multi stop w1 -A proj -l info
```

중지 명령은 비동기적으로 동작합니다. 따라서 ```worker```가 종료되기를 기다리지 않습니다. 모든 ```task```가 종료되고 중지 명령이 끝나길 원하는다면 ```stopwait``` 명령을 사용하세요.  

```
celery multi stopwait w1 -A proj -l info
```

> **알아두세요**  
```celery multi```는 ```worker```의 정보를 저장하지 않기 때문에 재시작 명령 시 같은 커맨드 라인 인자를 사용할 필요가 있습니다. 중지 시에도 동일한 pidfile과 logfile 인자를 사용해야 합니다.   

기본적으로 현재 디렉토리에 pid 및 log 파일을 만들기 때문에 여러 작업자가 다수의 ```worker```를 실행하는 것을 예방하기 위해서는 아래와 같이 지정한 전용 디렉토리를 쓰는 것이 좋습니다.   

```
$ mkdir -p /var/run/celery
$ mkdir -p /var/log/celery
$ celery multi start w1 -A proj -l info --pidfile=/var/run/celery/%n.pid --logfile=/var/log/celery/%n%I.log
```

아래와 같은 ```multi``` 명령으로 다수의 ```worker```를 실행할 수 있습니다.  
```
$ celery multi start 10 -A proj -l info -Q:1-3 images,video -Q:4,5 data -Q default -L:4,5 debug
```

더 많은 예제는 [여기](http://docs.celeryproject.org/en/latest/reference/celery.bin.multi.html#module-celery.bin.multi)를 참고하세요.  

#### ```-app``` 인자에 대해
```-app``` 은 사용할 ```app``` 인스턴스를 지정하는 인자로 ```module.path:attribute``` 양식을 준수해야 합니다.  

그러나 패키지명만 지정해 사용하는 단축 표현도 지원합니다. 아래와 같은 수서로 ```app```인스턴스를 탐색합니다.   

```-app=proj```의 경우,  
1. proj.app
2. proj.celery
3. 값이 셀러리 애플리케이션인 모듈 proj

그래도 발견되지 않으면 ```proj.celery```로 지정된 이름의 서브 모듈을 탐색합니다.  

4. proj.celery.app
5. proj.celery.celery
6. 값이 셀러리 애플리케이션인 모듈 proj.celery

이러한 방식은 마치 문서 구성에 사용되고 이용되는 방법을 떠올리게 합니다.  

--- 

## ```task``` 호출하기
```delay()``` 메서드를 이용하면 ```task```를 호출할 수 있습니다.  

{% highlight python %}
add.delay(2, 2)
{% endhighlight %}

이 메서드는 실제로 ```apply_async()``` 메서드를 참조 호출한 것입니다.   

{% highlight python %}
add.apply_async((2, 2))
{% endhighlight %}

후자는 카운트다운이나 대기열의 동작 방식 등 실행 옵션을 지정할 수 있습니다.  

{% highlight python %}
add.apply_async((2, 2), queue='lopri', countdown=10)
{% endhighlight %}

위 명령은 lopri라는 큐에 ```task```를 전송하고 10초 후 실행합니다.  

```task```를 현재 프로세스 상에서 직접 실행하게 되면 아무 메시지가 전송되지 않게 됩니다.  

{% highlight python %}
>>> add(2, 2)
4
{% endhighlight %}

```delay(), apply_async(), applying(__call__)```은 셀러리 API 호출이며 서명에도 사용됩니다.   

API 호출에 대해 더 자세히 알고 싶다면 [여기](http://docs.celeryproject.org/en/latest/userguide/calling.html#guide-calling)를 참고하세요.  

```delay```와 ```apply_async``` 메서드는 실행 상태를 추적하고 유지하는데 사용되는 ```AsyncResult``` 인스턴스를 리턴합니다. 하지만 이 값을 추적하고 유지하기 위해서는 어딘가에 상태가 저장될 수 있도록 벡엔드 설정을 해줘야 합니다.  

모든 애플리케이션 꼭 맞는 백엔드란 존재하지 않기 때문에 기본적으로 비활성화 되어 있습니다. 각 백엔드는 모두 각기 다른 장단점이 있기 때문에 자신의 환경을 고려한 선택을 해야 합니다. 셀러리에 의해 처리되는 많은 작업들이 사실 결과값 리턴이 필요없는 경우가 있기 때문에 기본 값으로 어떤 백엔드를 지정한다는 것은 민감한 일이며 유용하지 않습니다. 더구나 백엔드는 셀러리의 ```task```나 ```worker``` 모니터링에 필수 요소가 아닙니다. 셀러리는 모니터링과 관리에 전용 이벤트 메시지를 사용합니다.  

만약 여러분이 백엔드를 설정했다면 ```task``` 수행 후 리턴 값을 아래와 같이 얻을 수 있을 겁니다.  
{% highlight python %}
>>> res = add.delay(2, 2)
>>> res.get(timeout=1)
4
{% endhighlight %}

```id``` 속성을 통해서 ```task```의 id를 얻을 수 있습니다.
{% highlight python %}
>>> res.id
d6b3aea2-fb9b-4ebc-8da4-848818db9114
{% endhighlight %}

또한 예외 발생 시 예외와 트렉백을 검사할 수 있습니다. result.get()은 기본적으로 모든 에러를 전파합니다.   
{% highlight python %}
>>> res = add.delay(2)
>>> res.get(timeout=1)
{% endhighlight %}

```
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
File "/opt/devel/celery/celery/result.py", line 113, in get
    interval=interval)
File "/opt/devel/celery/celery/backends/rpc.py", line 138, in wait_for
    raise meta['result']
TypeError: add() takes exactly 2 arguments (1 given)
```

아래의 설정으로 예외 전파를 비활성화 할수도 있습니다.   
{% highlight python %}
>>> res.get(propagate=False)
TypeError('add() takes exactly 2 arguments (1 given)',)
{% endhighlight %}

이 경우 결과 값에 적합한 failed 또는 successful 메서드를 사용하여 예외를 발생시키는 대신 수행 결과의 True, False 값을 얻을 수 있습니다.  
{% highlight python %}
>>> res.failed()
True

>>> res.successful()
False
{% endhighlight %}

그럼 성공인지 실패인지 어떻게 알 수 있을까요? 상태값을 이용하면 됩니다.   
{% highlight python %}
>>> res.state
'FAILURE`
{% endhighlight %}

```task```는 하나의 상태 값만 가질 수 있고 일반적으로 아래와 같이 변합니다.   
```
PENDING -> STARTED -> SUCCESS
```

```started``` 상태값은 좀 특별한데 ```task_track_started``` 설정이 활성화된 경우나 @task(track_started=True)인 경우에만 기록됩니다.  

```pending``` 상태값은 사실 기록되는 상태값은 아니고 모든 ```task```의 기본 상태값입니다. 아래에 그 예가 있습니다.   

{% highlight python %}
>>> from proj.celery import app

>>> res = app.AsyncResult('this-id-does-not-exist')
>>> res.state
'PENDING'
{% endhighlight %}

```task```가 재시도되는 경우에 상태값 변화는 좀 더 복잡한 편입니다. 아래는 ```task```가 두 번 재시도 되었을 경우에 상태 값 변화 예시입니다.  
```
PENDING -> STARTED -> RETRY -> STARTED -> RETRY -> STARTED -> SUCCESS
```

상태 값에 대해 더 알고 싶다면 사용자 가이드 중 [이 섹션](http://docs.celeryproject.org/en/latest/userguide/tasks.html#task-states)을 참고하세요.  

```task``` 호출에 관한 자세한 내용은 [호출 가이드](http://docs.celeryproject.org/en/latest/userguide/calling.html#guide-calling)에 나와 있습니다.  

--- 

## 캔버스(Canvas): 워크플로우 디자인하기
지금까지 ```delay``` 메서드를 이용해 어떻게 ```task```를 호출하는지 배웠습니다. 사실 이게 여러분이 필요한 전부라고 감히 말할 수 있습니다. 하지만 어떤 경우에는 시그니처(signature)라고 불리는 것을 이용해 다른 프로세스나 함수로 ```task``` 시그니처를 전달하는 방식으로 실행되도록 해야하는 경우가 있을 수도 있습니다.   

시그니처는 한 단일 ```task```의 인자값과 실행 옵션을 묶어 둔 것으로 함수에 전달하거나 직렬화, 다른 곳으로 전송 등을 하는 한 방법입니다.   

아래는 add ```task```에 (2, 2) 인자 값과 countdown 10초를 전달하는 예입니다.  
{% highlight python %}
>>> add.signature((2, 2), countdown=10)
tasks.add(2, 2)
{% endhighlight %}

이렇게 짧게 표현할수도 있습니다.  
{% highlight python %}
>>> add.s(2, 2)
tasks.add(2, 2)
{% endhighlight %}

### 다시 API 호출하기...
시그니처 인스턴스도 ```delay```와 ```apply_async``` API 호출을 지원합니다.  

시그니처가 다른 점은 이미 지정된 인자 값이 있다는 점입니다.   
{% highlight python %}
>>> s1 = add.s(2, 2)
>>> res = s1.delay()
>>> res.get()
4
{% endhighlight %}

아래와 같이 partials(파셜 이라고 발음)이라고 부르는 불완전한 시그니처를 생상할 수도 있습니다.   
{% highlight python %}
#incomplete partial: add(?, 2)
>>> s2 = add.s(2)
{% endhighlight %}

위 s2는 부분 시그니처로 다른 인자 값이 있어야 완전해집니다. 아래와 같이 말이죠.   
{% highlight python %}
#resolves the partial: add(8, 2)
>>> res = s2.delay(8)
>>> res.get()
10
{% endhighlight %}

이렇게 8을 더해주면서 add(8, 2)와 같이 완전한 시그니처의 형태를 갖추었습니다.  

다른 키워드들의 인자도 나중에 추가될 수 있습니다. 이 경우 나중에 추가된 키워드 인자 값이 우선시 됩니다.  
{% highlight python %}
#resolves the partial: add(8, 2)
>>> s3 = add.s(2, 2, debug=True)
>>> s3.delay(debug=False)   # debug is now False.
{% endhighlight %}

아래 시그니처와 같이 API 호출이 지원됩니다.  

```
- sig.apply_async(args=(), kwargs={}, **options)
선택적인 부분 인자와 부분 키워드 인자로 시그니처를 호출합니다. 부분 실행 옵션도 지원합니다.

- sig.delay(*args, **kwargs)
별표(star) 인자를 이용하는 버전의 apply_async 입니다. 키워드 인자는 기존에 존재하는 어떤 키와도 병합될 수 있고, 어떤 인자라도 시그니처의 인자인 것처럼 보이도록 동작할 수 있습니다.  
```

언뜻 보기에는 굉장히 유용해 보이지만 이걸로 뭘 할 수 있을까요? 이에 대한 답을 얻기 위해서는 아래 캔버스 기본 자료형을 소개해야 합니다.    

### 기본 자료형
- group
- map
- chain
- starmap
- chord
- chunks

이 기본 자료형은 그 자체로 시그니처의 객체가 될 수 있습니다. 바꿔 말하면 복잡한 워크플로우의 구성과 함께 다양한 방식으로 결합될 수 있다는 말입니다.  

> 알아두세요.
이 예제는 결과를 탐색하는 방식이기 때문에 진행하기 전에 위의 과정과 같이 백엔드가 설정되어 있어야 합니다.  

이제 몇 가지 예제를 살펴보겠습니다.  

#### 그룹(Groups)
```group```은 ```task``` 리스트를 병렬로 호출합니다. 그리고 결과를 ```group```으로 리턴하고 순서대로 탐색합니다.   
```
>>> from celery import group
>>> from proj.tasks import add

>>> group(add.s(i, i) for i in xrange(10))().get()
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

- 부분 그룹(Partial group)   
```
>>> g = group(add.s(i) for i in xrange(10))
>>> g(10).get()
[10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
```

#### 체인(Chains)
```task```는 서로 연결되어 한 ```task```가 끝나면 다른 ```task```를 호출하도록 할 수 있습니다.   
```
>>> from celery import chain
>>> from proj.tasks import add, mul

# (4 + 4) * 8
>>> chain(add.s(4, 4) | mul.s(8))().get()
64
```

부분 체인(Partial chain):    
```
>>> # (? + 4) * 8
>>> g = chain(add.s(4) | mul.s(8))
>>> g(4).get()
64
```

이렇게 쓰일 수도 있습니다.   
```
>>> (add.s(4, 4) | mul.s(8))().get()
64
```

#### 코드(Chords)
```chord```는 콜백을 사용하는 ```group```입니다.
```
>>> from celery import chord
>>> from proj.tasks import add, xsum

>>> chord((add.s(i, i) for i in xrange(10)), xsum.s())().get()
90
```

다른 ```task```와 연결된 ```group```은 자동으로 ```chord```로 변환됩니다.   
```
>>> (group(add.s(i, i) for i in xrange(10)) | xsum.s())().get()
90
```

이 기본 자료형이 모두 시그니처 타입이기 때문에 거의 모든 경우에 이렇게 결합될 수 있습니다.   
```
>>> upload_document.s(file) | group(apply_filter.s() for filter in filters)
```

워크플로우에 대해 더 알고 싶다면 [캔버스 사용자 가이드](http://docs.celeryproject.org/en/latest/userguide/canvas.html#guide-canvas)를 참고하세요.   

--- 

## 라우팅
셀러리는 AMQP(Advanced Message Queuing Protocol, 메시지 지향 미들웨어를 위한 개방형 표준 응용 계층 프로토콜)의 모든 규약을 지원할 뿐만 아니라 이름 있는 큐로 메시지를 전송하는 간단한 라우팅도 지원합니다.  

```task_routes```를 사용하면 ```task```를 이름 순으로 라우팅하고 한곳에 중앙화하여 유지, 관리할 수 있습니다.  

{% highlight python %}
app.conf.update(
    task_routes = {
        'proj.tasks.add': {'queue': 'hipri'},
    }
)
{% endhighlight %}

```apply_async```에 ```queue``` 인자를 활용하면 런타임에 큐를 지정할수도 있습니다.  

```
>>> from proj.tasks import add
>>> add.apply_async((2, 2), queue='hipri')
```

그 다음 ```celery worker -Q``` 옵션으로 큐를 지정해 ```worker```가 큐에 대기 중인 작업을 처리하도록 만들 수 있습니다.  

```
$ celery -A proj worker -Q hipri
```

```Q```옵션에 콤마를 사용하여 큐 이름을 나열하면 다수의 큐를 ```worker```가 처리하도록 할 수 있습니다. 아래는 기본 큐와 hipri라는 큐를 처리하도록 하는 예입니다. 역사적으로 기본 큐의 이름은 celery로 사용합니다.  

```
$ celery -A proj worker -Q hipri,celery
```

여기에서 콤마로 구분된 큐의 순서는 중요하지 않습니다. ```worker```는 동일한 가중치로 큐를 취급합니다. AMQP 라우팅의 강력한 사용법과 함께 라우팅에 대해 더 알고 싶다면 [라우팅 가이드](http://docs.celeryproject.org/en/latest/userguide/routing.html#guide-routing)를 참고하세요.  

--- 

## 원격 조종
여러분이 브로커로 ```RabbitMQ(AMQP)```, ```Redis```나 ```Qpid```를 사용한다면 런타임에 ```worker```를 컨트롤하고 감시할 수 있습니다.  

예를 들어, 아래 명령으로 현재 어떤 ```worker```가 작업 중인지 알 수 있습니다.  
```
$ celery -A proj inspect active
```

이것은 브로드캐스트 메시징에 의해 구현된 것이기 때문에 클러스터에 속한 모든 ```worker```에 의해 원격 컨트롤 명령이 수신되게 됩니다.  

```-destination``` 옵션으로 요청에 대해 특정 ```worker```가 동작하게 할 수도 있습니다.   
```
$ celery -A proj inspect active --destination=celery@example.com
```

```destination```이 지정되지 않으면 모든 ```worker```가 요청에 응답하게 됩니다.  

```celery inspect``` 명령은 ```worker```에 어떤 영향도 주지 않고 단지 ```worker``` 안에서 무슨 일이 일어나고 있는지에 대한 정보와 통계를 제공합니다. 아래 명령으로 ```inspect```가 제공하는 명령 목록을 볼 수 있습니다.   
```
$ celery -A proj inspect --help
```

또 ```celery control```은 런타임에 변경 가능한 것에 대한 명령을 가지고 있습니다.  
```
$ celery -A proj control --help
```

예를 들어, 아래와 같이 ```task```와 ```worker``` 모니터링에 사용되는 이벤트 메시지를 활성화 할 수도 있습니다.  
```
$ celery -A proj control enable_events
```

이벤트가 활성화되면 이벤트 덤프를 통해 ```worker```가 작업 중인 사항을 볼 수 있습니다.  
```
$ celery -A proj events --dump
```

아래와 같이 curses 인터페이스를 시작할수도 있고  
```
$ celery -A proj events
```

모니터링이 끝나면 이렇게 이벤트를 다시 비활성화 할 수도 있습니다.  
```
$ celery -A proj control disable_events
```

```celery status``` 역시 원격 컨트롤 명령으로 클러스터 안의 온라인 ```worker``` 목록을 보여줍니다.   
```
$ celery -A proj status
```

셀러리 명령어와 모니터링에 대해 더 알고 싶다면 [모니터링 가이드](http://docs.celeryproject.org/en/latest/userguide/monitoring.html#guide-monitoring)를 참고하세요.  

--- 

## 타임존
내부적으로 그리고 메시지에 사용되는 모든 시간과 날짜는 UTC 타임존을 사용합니다.  

예를 들어, ```worker```가 countdown과 함께 메시지를 수신하는 경우 UTC 시간은 지역 시간으로 변환됩니다. 만약 시스템 타임존이 아닌 다른 타임존을 사용하고 싶다면 ```timezone```을 설정해줘야 합니다.  

```
app.conf.timezone = 'Europe/London'
```

--- 

## 최적화
기본적으로 설정은 처리량에 최적화되어 있지 않고, 짧은 작업부터 긴 몇몇 작업까지 어딘가에서 처리량과 공정한 스케줄링의 어느 적정선에 타협하여 작업을 수행하게 됩니다.  

만약 스케줄링을 공평하게 하는 것이나, 처리량에 따른 최적화를 원한다면 [최적화 가이드](http://docs.celeryproject.org/en/latest/userguide/optimizing.html#guide-optimizing)를 읽어보길 권장합니다.  

만약 여러분이 ```RabbitMQ```를 사용한다면 ```librabbitmq```를 설치할 수 있습니다. 이것은 C로 구현된 AMQP 클라이언트입니다.  
```
$ pip install librabbitmq
```

--- 

## 이제 무엇을 해야 할까요?
여기까지 읽었다면 이제 [사용자 가이드](http://docs.celeryproject.org/en/latest/userguide/index.html#guide)를 볼 차례입니다. 생각이 있으시면 [API 레퍼런스](http://docs.celeryproject.org/en/latest/reference/index.html#apiref)도 읽어보세요.  
