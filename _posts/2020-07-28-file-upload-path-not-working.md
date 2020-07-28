---
layout: post
title: 파일업로드 컨트롤에서 파일 경로가 표시되지 않을 때
tags: html js
comments: true
---

## 증상

HTML로 파일업로드 폼을 만들었는데 파일 경로가 표시되지 않는다.  
  
왜 그런걸까. 아래와 같이 HTML로 작성돈 폼이 있다고 가정해보자. (이 코드는 [부트스트랩](https://getbootstrap.com/docs/4.5/components/input-group/#custom-file-input) 페이지에서 가져왔다.)  

{% highlight html %}
<div class="input-group mb-3">
  <div class="custom-file">
    <input type="file" class="custom-file-input" id="imageupload">
    <label id="imageupload-label" class="custom-file-label" for="imageupload" aria-describedby="inputGroupFileAddon02">Choose file</label>
  </div>
</div>
{% endhighlight %}

이 코드를 그대로 가져다 붙여넣고 테스트해보면 파일 선택 후 경로 표시가 되지 않는다. 반면 부트스트랩 페이지에서 해보면 경로가 제대로 표시된다.  

## 원인

HTML 폼은 말 그대로 형태만 구현하는 것이다. 선택한 파일의 경로를 동적으로 표시해주려면 자바스크립트를 써서 선택된 파일의 경로를 표시되도록 구현해주어야 한다.  

## 해결방법

바닐라 자바스크립트로도 같은 기능을 구현할 수 있지만 제이쿼리를 이용해 좀 더 쉽게 아래와 같이 구현할 수 있다.  

{% highlight javascript %}
$(document).ready(function() {
    $('#imageupload').on('change', function() {
        $('#imageupload-label').text($(this).val());
    });
});
{% endhighlight %}

핵심은 input이 변할 때 input의 id를 읽어 해당 값을 label에 경로를 표시해주는 것이다.  
