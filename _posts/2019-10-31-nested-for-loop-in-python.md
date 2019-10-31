---
layout: post
title: 리스트 안에 중첩된 파이썬의 for loop이 동작하는 방법과 간단한 예제
tags: python
comments: true
---

파이썬에는 리스트 안에 for문을 넣을 수 있습니다. 유용한 구문이지만 처음보는 사람에겐 당황스러운 문법이 아닐 수 없습니다. 예를 들어 아래와 같은 모양입니다.

## 일반적인 for문

{% highlight python %}
for i in range(10):
...
{% endhighlight %}

## 리스트 안의 for문

{% highlight python %}
[i*2 for i in range(10)]
{% endhighlight %}

위와 같이 구현하면 [0, 2, 4, 6, 8, 10, 12, 14, 16, 18] 리스트가 됩니다. `for i in range(10)`이 먼저 해석되고 `i*2`가 실행되는 겁니다.

## 리스트 안에 중첩된 for문

{% highlight python %}
item_cnt = 3
list = [['a', 'b', 'c'], ['aa', 'bb', 'cc'], ['aaa', 'bbb', 'ccc']]

[[nested_list[i] for nested_list in list] for i in range(item_cnt)]
{% endhighlight %}

뭔가 굉장히 복잡해 보입니다. 차근차근 살펴보겠습니다. 기본적인 것은 **리스트 안의 for문**과 동일합니다. for문이 2개이므로 실행 순서만 주의하면 됩니다.

`[nested_list[i] for nested_list in list]`이 먼저 실행됩니다. 그럼 `list`에서 `['a', 'b', 'c']`가 뽑혀 나올 것입니다. 그 다음 `for i in range(item_cnt)`가 실행되면서 i를 앞서 실행한 for문에 전달해줍니다. 그러면 결과가 `['a', 'aa', 'aaa']`와 같이 됩니다. 그다음 다시 앞쪽의 for문이 실행되고 다시 뒤쪽의 for문이 실행되어 `['b', 'bb', 'bbb']`가 됩니다. 이렇게 `['c', 'cc', 'ccc']`까지 되면 최종적으로 `[['a', 'aa', 'aaa'], ['b', 'bb', 'bbb'], ['c', 'cc', 'ccc']]`와 같은 결과가 됩니다.

for문이 중첩되었다고 겁먹거나 헷갈리지 마시고 순서대로 생각하시면 됩니다. : )

---

### 예제 소스

{% highlight python %}
item_cnt = 3
list = [['a', 'b', 'c'], ['aa', 'bb', 'cc'], ['aaa', 'bbb', 'ccc']]

[[nested_list[i] for nested_list in list] for i in range(item_cnt)]
{% endhighlight %}

### 결과

{% highlight python %}
[['a', 'aa', 'aaa'], ['b', 'bb', 'bbb'], ['c', 'cc', 'ccc']]
{% endhighlight %}
