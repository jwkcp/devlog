---
layout: post
title: 리액트(React)에서 모먼트(moment.js) 한국어 표시 방법
tags: react momentjs
comments: true
---

## 문제

리액트에서 moment.js 설치(`yarn add moment`) 후 날짜 표시를 한글로 표시하려고 `moment.locale('ko')`로 해줬는데도 한글로 나오지 않습니다. 어떻게 해야 하나요?  
  
---

## 해결 방법

아래 코드를 임포트 해주면 됩니다.  

{% highlight javascript %}
import 'moment/locale/ko'
{% endhighlight %}

참, 당연히 `moment`를 사용하기 전에 `moment.locale('ko')` 설정이 먼저 나와야 합니다.  
