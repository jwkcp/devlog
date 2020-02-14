---
layout: post
title: 파이썬에서 갑자기 로거가 동작하지 않을 때 해결방법
tags: python logger logging
comments: true
---

## 문제

파이썬에서 설정을 파일로 분리하여 fileConfig로 설정을 불러쓰는데 갑자기 일부 혹은 전체 로거가 동작하지 않아 로깅이 안될 때가 있다. 핸들러를 스트림, 파일 어떤 것을 지정해도 로그가 하나도 찍히지 않거나 최초 실행한 파일(예를 들어, main.py 같이)의 로그만 찍히고 나머지 불러온 모듈의 로그가 하나도 찍히지 않는다.

## 원인

파이썬에서는 fileConfig나 dictConfig로 설정을 불러써 로거를 사용할 경우 `disable_existing_loggers` 옵션이 기본적으로 활성화되어 있다. 그러니까 기본적으로 다른 로거들은 무시하도록 하는 옵션이 `True`이기 때문에 불러온 다른 모듈의 로그가 하나도 찍히지 않는 것이다. [공식문서](https://docs.python.org/3/howto/logging.html#configuring-logging)에서 아래와 같이 설명하고 있다.

> Warning The fileConfig() function takes a default parameter, disable_existing_loggers, which defaults to True for reasons of backward compatibility. This may or may not be what you want, since it will cause any non-root loggers existing before the fileConfig() call to be disabled unless they (or an ancestor) are explicitly named in the configuration. Please refer to the reference documentation for more information, and specify False for this parameter if you wish.The dictionary passed to dictConfig() can also specify a Boolean value with key disable_existing_loggers, which if not specified explicitly in the dictionary also defaults to being interpreted as True. This leads to the logger-disabling behaviour described above, which may not be what you want - in which case, provide the key explicitly with a value of False.

요약하면 이렇다. _"fileConfig의 `disable_existing_loggers`가 하위 호환성 때문에 활성화되어 있으니 주의하세요. `fileConfig()`가 호출되기 전 루트가 아닌 로거들은 `disable_existing_loggers` 옵션을 명시적으로 `False`로 바꾸기 전까지는 비활성화됩니다. `dictConfig()`를 사용하는 경우도 마찬가지입니다."_

# 해결방법

위 설명대로 `disable_existing_loggers` 옵션을 `False`로 한.번.만 지정해주면 동작하지 않던 다른 모듈을 로그가 드디어 찍히는 것을 볼 수 있다. 이 옵션을 False하는 예는 아래와 같다.

{% highlight python %}
logging.config.fileConfig('logger.conf', disable_existing_loggers=False)
logger = logging.getLogger(**name**)
{% endhighlight %}
