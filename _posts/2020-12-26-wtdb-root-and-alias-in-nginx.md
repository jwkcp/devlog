---
layout: post
title: Nginx설정에서 root와 alias 차이점
tags: nginx
comments: true
---

`nginx` 설정 시 `location` 경로를 지정할 때 `root`나 `alias`를 사용할 수 있습니다. 이 둘에 차이점을 간단히 보면 아래와 같습니다.   

사용자가 요청을 http://`A/B/C/d.txt`로 했다고 가정해시다. 이때 각각은 아래와 같이 실제 경로를 탐색합니다.  

---

## root   

{% highlight nginx %}
location /A/B/ {
    root /가/나/;
}
{% endhighlight %}

**실제 탐색 경로**: /가/나/A/B/C/d.txt  

## alias

{% highlight nginx %}
location /A/B/ {
    alias /가/나/;
}
{% endhighlight %}

**실제 탐색 경로**: /가/나/C/d.txt
