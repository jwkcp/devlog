---
layout: post
title: 자주 쓰는 워드프레스 커스텀(Custom) CSS
tags: wordpress
comments: true
---

아래 내용을 워드프레스 테마 > CSS 편집 > 추가 CSS 에 넣어주면 됩니다.  
폰트는 [구글 폰트](https://fonts.google.com/)에 방문해 원하는 폰트 고르고 아래 @import 부분과 font-family를 바꿔주면 됩니다.

가급적이면 자식 테마를 만들어 수정하는 것이 좋습니다. 그러면 부모 테마가 업데이트 되어도 내 설정이 그대로 남기 때문입니다.  
[워드프레스에서 자식 테마 1분만에 만들기](http://devlog.jwgo.kr/2019/04/22/making-child-theme-for-wordpress) 참고하세요.

{% highlight css %}
@import url('https://fonts.googleapis.com/css?family=Gothic+A1');

body,
button,
input,
aside,
select,
textarea {
font-family: 'Gothic A1', sans-serif;
font-size: 1.0rem;
line-height: 1.8rem;
}

blockquote {
font-family: 'Gothic A1', sans-serif;
font-size: 0.8rem;
line-height: 1.4rem;
}

code,
kbd,
tt,
var,
samp,
pre {
font-family: 'Gothic A1', sans-serif;
-webkit-hyphens: none;
-moz-hyphens: none;
-ms-hyphens: none;
hyphens: none;
}

{% endhighlight %}
