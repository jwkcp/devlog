---
layout: post
title: Django(장고)에서 allauth로 카카오 로그인 시 KOE006 에러 발생하는 경우 해결 방법
tags: django
comments: true
---

# 문제

로컬 개발 환경에서 멀쩡히 잘 돌어가던 `django-allauth`의 카카오 로그인 기능이 갑자기 `KOE006`에러를 뱉으며 동작하지 않는다.  

`KOE006`은 [카카오 디벨로퍼 페이지](https://developers.kakao.com/docs/latest/ko/kakaologin/trouble-shooting)에서 아래와 같이 설명하고 있다.  

```
등록되지 않은 Redirect URI를 인증코드 요청에 사용해서 발생하는 에러입니다.  

[내 애플리케이션] > [카카오 로그인]에서 Redirect URI를 새로 등록하거나,
이미 등록된 Redirect URI를 인증코드 요청 파라미터 redirect_uri 값으로 사용합니다.
```

---

# 원인

`redirect url`을 잘 등록했는데 이 에러가 난다면 내가 입력한 주소와 다른 값이 사용되고 있다는 얘기입니다. 이 에러가 로컬에서는 안나다가 배포 후 발생하는 것은 배포 시 보통 장고의 내장 웹서버를 쓰지 않고 `nginx`나 `apache`등의 웹서버와 `gunicorn` `uWSGI` 등으로 리버스 프록시를 구성하면서 발생합니다. 실제 도메인을 입력해 요청은 웹서버가 받았는데 처리하는 앱서버(장고)가 내부적으로 사용하는 주소는 localhost나 127.0.0.1같은 주소로 통신하고 이 값을 다시 리턴하면 카카오와 같이 소셜 로그인을 제공하는 서버에서는 등록되지 않은 redirect url로 인식하는 것입니다.  

---

# 해결방법
`nginx`를 예로 들면 대략 아래와 같은 식의 설정이 있을 것입니다. 여기서 중요한 부분은 `proxy_set_header Host $host;`입니다. 이 설정은 업스트림 그러니까 뒤에서 요청을 받아주는 장고 서버가 응답을 돌려주면서 localhost와 같은 주소가 아니라 원래 요청을 받았던 주소로 설정토록 하는 것입니다. 이렇게 하면 대부분 문제가 해결 됩니다.  

*nginx 설정 예시*   
{% highlight nginx %}
location / {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass http://app:8000;
}
{% endhighlight %}

**그러나!!**  

이렇게 설정하면 `Bad request(400)` 에러가 발생하는 분이 계실 수 있습니다. 이 에러는 십중팔구 Django의 settings.py에 `ALLOWED_HOSTS` 설정 때문입니다. 만약 `example.com`이라는 도메인을 쓰고 있다면 이 도메인을 `ALLOWED_HOSTS` 목록에 포함시켜 주세요.  

