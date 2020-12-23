---
layout: post
title: http를 https로 리다이렉트하는 방법
tags: django nginx
comments: true
---

# 문제

AWS Lightsail의 로드밸런서는 https 리다이렉트를 지원할까? 안된다면 웹서버에서 어떻게 할 수 있을까?  

---

# 해결방법

제가 알기로는 lightsail의 로드밸런서는 별도로 지원하는 리다이렉트 기능이 없습니다.(2020.12)  

웹서버에서 해줘야 하는데 `nginx` 기준으로 아래와 같이 설정에서 입력하면 됩니다. 요청의 프로토콜(`$http_x_forwarded_proto`)이 `http`이면 영구적으로(301) `https://$server_name$request_uri`로 이동했다고 설정합니다.  

{% highlight nginx %}
server {
    listen   80;
    server_name    www.example.com;   

    if ($http_x_forwarded_proto = 'http') {
         return 301 https://$server_name$request_uri;   
    }
}
{% endhighlight %}
