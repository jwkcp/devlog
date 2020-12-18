---
layout: post
title: vi나 view 등의 명령어가 도커(Docker)에서 컨테이너에서 동작하지 않을 때
tags: docker linux
comments: true
---

# 문제

도커 컨테이너를 올리고 쉘로 접속해보면 `vi`나 `view` 등의 명령어가 동작하지 않을 때가 있습니다. 어떻게 해야 할까요?  

---

# 원인 

`vi`나 `view`가 설치되어 있지 않아서 그렇습니다.  

---

# 해결방법

`vi`나 `view`를 설치해주면 됩니다.  

{% highlight shell %}
apt update
apt install vim
{% endhighlight %}
