---
layout: post
title: 파이썬에서 클래스 맴버 변수의 자료형이 string이 아니라 tuple로 나타나는 이유와 해결방법
tags: python
comments: true
---

## 문제

파이썬에서 아래와 같이 사람 클래스를 만들고 클래스 인스턴스에서 맴버 변수에 접근해보면 string이 아니라 tuple이 되어 있다.

{% highlight python %}
class Human:
    def __init__(self, name, gender):
        self.name = name,
        self.gender = gender,
{% endhighlight %}

{% highlight python %}
tom = Human("tom", "m")
tom.name # tuple
tom.gender # tuple
{% endhighlight %}

왜 name과 gender가 tuple일까?

## 원인

`self.name = name`의 문장 끝에 `,(콤마)`가 붙어있기 때문이다. 파이썬은 뒤에 콤마가 붙으면 튜플(tuple) 자료형으로 인식한다.

## 해결방법

아래와 같이 콤마를 제거해준다.

{% highlight python %}
class Human:
    def __init__(self, name, gender):
        self.name = name
        self.gender = gender
{% endhighlight %}
