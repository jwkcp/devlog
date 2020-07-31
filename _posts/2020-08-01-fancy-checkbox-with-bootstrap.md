---
layout: post
title: 부트스트랩을 이용한 예쁜 체크박스 만들기(iOS 스타일)
tags: html bootstrap
comments: true
---

html의 기본 체크박스를 iOS처럼 동글 동글 좀 보기좋게 표시하고 싶다면 아래와 같이 하면 됩니다.  

{% highlight html %}
<div class="custom-control custom-switch">
    <input type="checkbox" class="custom-control-input" id="myid">
    <label class="custom-control-label" for="myid">활성화 여부</label>
</div>
{% endhighlight %}

체크박스와 동일하게 `checked`나 `disabled` 속성을 이용해 컨트롤의 상태를 바꿀 수 있습니다.  
