---
layout: post
title: 내가 만든 패키지 테스트하기 (No module name 에러가 발생할 때)
tags: python
comments: true
---

## 문제

패키지/모듈을 하나 만들었고 아래와 같은 디렉토리 구조를 가지고 있다. 간단히 유닛테스트를 작성하려고 하는데 _No module named..._ 하는 에러가 발생한다. 아니, 꼭 유닛테스트가 아니라 단순히 내가 만든 패키지/모듈을 불러쓰려고 하는데도 _No module named..._ 에러가 발생한다. 도대체 뭐가 잘못된 걸까?

```
PROJECT_DIR
  L PACKAGE_DIR
    L __init__.py
    L module.py
  L TEST_DIR
    L __init__.py
    L test.py
```

---

## 원인

위 디렉토리 구조를 보자. 우리가 만든, 그러니까 불러다 쓸 패키지/모듈의 최종 파일은 **module.py**이다. 그리고 **test.py**파일에 _from PACKAGE_DIR import module_ 처럼 임포트했다. 이 상태에서 _No module named ..._ 에러가 발생하는 것이다.

기본적으로 파이썬은 모듈을 불러올 때 패키지 설치 경로와 자신의 현재 위치를 참조한다. 이런 이유로 바로 위 상위 디렉토리라도 불러오지 못하는 일이 발생하게 된다. ..(점 두 개)를 이용해 상대 경로로 참조하려는 시도를 했다면 아마 아래와 같은 에러를 만나게 될 것이다.

_ValueError: attempted relative import beyond top-level package_

그러니 이것도 올바른 방법이 아니다.

---

## 해결방법

여기서는 크게 3가지 방법을 언급한다. 결국은 파이썬 파일이 우리가 만든 패키지/모듈을 찾을 수 있게 해주는게 핵심이다.

### 1. sys path에 내가 만든 패지키 경로 추가하기(흐음...)

파일 코드 제일 처음에 아래와 같이 써준다.

{% highlight python %}
import sys
sys.path.insert(0, '/home/user/PROJECT_DIR/PACKAGE_DIR')
{% endhighlight %}

그런데 이렇게 하면 절대 경로를 사용해야 하기 때문에 다른 사람하고 공유한다든지, 다른 컴퓨터에서 작업해야 할 경우 좀 불편한 점이 없지 않다.

### 2. 환경변수 PATHONPATH에 추가하기(글쎄...)

말그대로 작업 중인 컴퓨터의 환경변수 PYTHONPATH에 우리가 만든 패키지의 경로를 넣어주는 것이다. 별로 추천하고 싶진 않다.

### 3. setup.py 이용하기(추천)

파이썬에서 배포용 패키지를 만들 때 사용하는 **setup.py**를 이용한다. 이 파일을 이용해 아래 명령을 쓰면 현재 디렉토리 **PROJECT_DIR**에 우리가 만든 패키지를 설치해주고 위 _from PACKAGE_DIR...._ 구문도 정상적으로 동작하게 된다. (PROJECT*DIR 내에 *패키지명.egg-info\_ 이름을 가진 디렉토리가 생긴다. 참고로 당연한 얘기지만 이 디렉토리를 git으로 관리하지 않는다.)

{% highlight python %}
python setup.py develop
{% endhighlight %}

혹은 이렇게 해도 된다.

{% highlight python %}
pip install .
{% endhighlight %}

잠깐, **setup.py** 없이 위 명령을 실행하면 **setup.py** 파일이 없다는 메시지를 보게 된다. **setup.py**는 [파이썬 공식문서](https://packaging.python.org/tutorials/packaging-projects/#setup-py)를 보고 복사해서 자기에게 맞게 내용을 바꿔서 사용하면 된다.

---

## 더 읽어볼 만한 글

-   [파이썬 모듈 pypi에 배포하는 방법]({% post_url 2018-03-11-how-to-deploy-to-pypi %})
-   [파이썬 유닛테스트를 실행했을 때 아무 결과가 나오지 않는다면]({% post_url 2018-03-08-why-python-unittest-not-working %})
