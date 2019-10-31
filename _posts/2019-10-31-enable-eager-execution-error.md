---
layout: post
title: 'tensorflow' has no attribute 'enable_eager_execution' 에러가 발생하는 경우
tags: ai tensorflow
comments: true
---

텐서플로우(Tensorflow) 튜토리얼을 따라하다보면 아래와 같은 문구가 종종 보입니다.

`tf.enable_eager_execution()`

## 문제

이 문구는 텐서플로우를 즉시 실행모드로 동작시키겠다는 명령인데요. 이 명령에서 아래와 같은 에러가 발생하는 경우가 있습니다.

{% highlight python %}

> > > tf.enable_eager_execution()
> > > Traceback (most recent call last):
> > > File "<stdin>", line 1, in <module>
> > > AttributeError: module 'tensorflow' has no attribute 'enable_eager_execution'
> > > {% endhighlight %}

## 원인

현재 설치한 버전이 텐서플로우 2.0 이상일 가능성이 높습니다. 텐서플로우 1.x까지는 그래프를 만들고 그것을 세션을 통해 실행하는 방법을 사용했늗데요. 이게 불필요한 구문을 많이 작성해야 하고 디버깅도 불편한 문제가 있었습니다. 그래서 2.0 부터는 이 모드가 기본으로 동작하게 되어 있습니다.

## 해결방법

기본적으로 텐서플로우 2.0은 `tf.enable_eager_execution()` 명령이 기본값입니다. 그러니 특별히 이 구문을 빼고 실행하시면 됩니다. 텐서플로우 1.0을 사용하시는 분은 이 구문을 실행해도 에러가 발생하지 않을겁니다.

## 팁

텐서플로우 2.0을 설치했고 기존 코드에서 문제가 발생한다면 텐서플로우 1.x 의 방식으로 동작할 수 있도록 구글에서 대안을 마련해 두었습니다. 바로 `tf.compat.v1` 구문입니다. 위의 `enable_eager_execution` 의 경우 `tf.compat.v1.enable_eager_execution()`와 같이 앞부분에 `compat.v1`을 붙여주면 됩니다. `compat`은 호환성을 뜻하는 compatible에서 유래한 것 같고 v1은 텐서플로우의 버전을 표시한 것이 아닌가 생각합니다.
