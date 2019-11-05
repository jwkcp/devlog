---
layout: post
title: 외부파일로 구성된 javascript 또는 jquery가 동작하지 않을 때
tags: javascript
comments: true
---

## 문제

외부파일(example.js)로 된 자바스크립트가 동작하지 않는 황당한 경험을 할 때가 있다.

---

## 해결방법

1. jquery를 쓴다면 가장 먼저 확인할 것이 jquery가 제대로 링크 걸려있는지 보는 것이다. 웹페이지에서 소스보기를 한 후 jquery 링크를 눌러 본다.

2. 내가 만든 외부파일(example.js)가 제대로 링크 걸려있는지 확인한다. 역시 마찬가지로 소스보기 후 링크를 눌러본다.

3. jquery를 쓴다면 내가 만든 외부파일(example.js)보다 jquery 링크가 더 상단에 있는지 확인한다.

4. 외부파일(example.js)를 불러올 때 아래와 같이 text를 빠트리지 않았는지 확인한다. 예를 들어,

**잘못된 예**
{% highlight html %}

<script type="javascript" src="/static/js/example.js"></script>

{% endhighlight %}

**올바른 예**
{% highlight html %}

<script type="text/javascript" src="/static/js/home.js"></script>

{% endhighlight %}

---
