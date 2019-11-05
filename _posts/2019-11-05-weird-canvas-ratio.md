---
layout: post
title: HTML5 캔버스(canvas)의 크기를 css로 지정하면 이상해지는 이유
tags: html
comments: true
---

## 문제

캔버스(canvas)를 css로 가로, 세로를 지정하면 비율이 2배가 되는 등 이상해집니다. 예를 들어 아래와 같이 코딩하면 세로가 가로보다 2배 커진 상태가 됩니다. 왜 그런가요?

---

## 원인

캔버스는 기본 가로, 세로가 300px, 150px로 정해져 있습니다. 이 상태에서 css로 `width: 300px; height: 300px`을 주면 가로는 그대로인 상태에서 세로가 2배 커지게(scale) 됩니다.

---

## 해결방법

캔버스의 속성을 이용해 아래와 같이 기본 크기를 지정해주세요.

{% highlight html %}
<canvas id="mycanvas" width="350" height="350"></canvas>
{% endhighlight %}

이후 css에서 가로, 세로를 각각 700px로 지정하면 2배 스케일된 캔버스가 만들어집니다. 지정하지 않으면 캔버스 속성에서 지정한 값이 됩니다.

---

_더 읽어볼만한 글_  
[HTML5 Canvas Essentials, informit.com](http://www.informit.com/articles/article.aspx?p=1903884)
