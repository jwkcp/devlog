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

---

## 리스트 안의 for문

{% highlight python %}
[i*2 for i in range(10)]
{% endhighlight %}

위와 같이 구현하면 `[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]` 리스트가 됩니다. `for i in range(10)`이 먼저 해석되고 `i*2`가 실행되는 겁니다.

---

## 리스트 안에 중첩된 for문

{% highlight python %}
item_cnt = 3
list = [['a', 'b', 'c'], ['aa', 'bb', 'cc'], ['aaa', 'bbb', 'ccc']]

[[nested_list[i] for nested_list in list] for i in range(item_cnt)]
{% endhighlight %}

뭔가 굉장히 복잡해 보입니다. 차근차근 살펴보겠습니다. 기본적인 것은 **리스트 안의 for문**과 동일합니다. for문이 2개이므로 실행 순서만 주의하면 됩니다.

`for i in range(item_cnt)`이 실행되면서 숫자 1이 나옵니다. 그럼 이 숫자가 앞쪽의 `[nested_list[i] for nested_list in list]`에 전달되면서 앞쪽 for문이 끝날 때까지 실행됩니다. 이 과정에서 `['a', 'aa', 'aaa']`가 만들어집니다.

그 다음 다시 `for i in range(item_cnt)`이 실행되면서 숫자 2가 나오고 앞쪽의 `[nested_list[i] for nested_list in list]`에 전달되면서 `['b', 'bb', 'bbb']`가 만들어집니다.

이런 방법으로 반복하면 `[['a', 'aa', 'aaa'], ['b', 'bb', 'bbb'], ['c', 'cc', 'ccc']]`와 같은 결과가 나옵니다.

for문이 중첩되었다고 겁먹지 마세요 : ) 뒤쪽 for문의 결과를 받아 앞쪽 for문을 반복하는 식입니다.

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
