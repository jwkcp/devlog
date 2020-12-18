---
layout: post
title: docker-compose 사용 시 nginx.conf에서 'directive is not allowed' 에러가 발생하는 경우 원인과 해결방법
tags: docker nginx
comments: true
---

# 문제

docker-compose로 아래와 같이 설정한다음 nginx를 구동해보면 이런 에러가 발생하는 경우가 있습니다. 왜 그런걸까요? 정상적인 지시자 맞는데요.  

**docker-compose 예시 일부**  
{% highlight django+python %}
# ... 생략 ...

nginx:
    image: library/nginx:latest
    ports:
        - "80:80"
    volumes:
        - ./config/nginx:/etc/nginx/conf.d

# ... 생략 ...
{% endhighlight %}

**nginx 에러 메시지**  
`nginx: [emerg] "worker_processes" directive is not allowed here in /etc/nginx/conf.d/nginx.conf:1`

---

# 원인 

nginx의 default.conf를 수정하려 해서 그렇습니다. 위의 `docker-compose`의 `volumes` 설정 부분을 보면 호스트의 nginx 디렉토리와 컨테이너의 conf.d 디렉토리를 마운트 시켜주고 있는데요. 이렇게 되면 호스트 디렉토리의 설정이 컨터이너의 conf.d에 있는 nginx 설정을 덮어쓰려고 시도하게 되는데 이 과정에서 오류가 발생합니다.  

---

# 해결방법

nginx 설정을 볼륨 마운트 해줄 때 위와 같이 디렉토리 통채로 하지 마시고 정확한 파일을 지정해주는 것이 좋습니다. 예를 들면, 아래와 같이 할 수 있습니다.  

{% highlight django+python %}
# ... 생략 ...

nginx:
    image: library/nginx:latest
    ports:
        - "80:80"
    volumes:
        - ./config/nginx/nginx.conf:/etc/nginx/nginx.conf

# ... 생략 ...
{% endhighlight %}

이렇게 하면 nginx의 `worker_processes`나 `event`, `http` 등 각종 상위 지시자 관련 에러가 발생하지 않게 됩니다.  
