---
layout: post
title: HTML 코드에 이모티콘을 넣는 방법(초간단)
tags: html emoji
comments: true
---

HTML 페이지에 이모티콘을 넣으려면 어떻게 하면 될까요? 각종 폰트나 부트스트랩의 아이콘 svg 이미지를 써도 되지만 아래와 같이 한 줄의 HTML 코드로도 가능합니다. 우리가 흔히 자주 보는 이모티콘이지요? 아래 예제 코드는 w3schools에서 가져왔습니다.     

{% highlight html %}
<p style="font-size:100px">&#129409;</p>
{% endhighlight %}

필요에 따라 `font-size`부분을 넣어도 되고 안넣어도 됩니다. 이 속성은 이모티콘의 크기를 조절하는 역할을 합니다. [이 사이트](https://www.w3schools.com/charsets/ref_emoji.asp)를 방문해 원하는 이모티콘의 코드를 찾아보세요.    
