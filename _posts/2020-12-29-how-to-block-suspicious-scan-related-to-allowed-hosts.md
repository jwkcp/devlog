---
layout: post
title: Django 배포했는데 Invalid HTTP_HOST header 에러가 Sentry에 계속 쌓일 때 조치 방법
tags: django sentry nginx
comments: true
---

# 문제

Django를 배포하고 에러 로그를 찍거나 Sentry를 달아보면 어마어마한 봇들의 접속이 시작됩니다. 특히 가장 많이 발생하는 것이 `Invalid HTTP_HOST header: '15.164.18.00'. You may need to add '15.164.18.00' to ALLOWED_HOSTS.` 이런 식의 에러인데 어차피 Django `ALLOWED_HOSTS` 설정으로 다 차단되긴 했지만 두 가지 잠재적 문제점이 있습니다.  

### 문제1 - Sentry 무료 용량 초과

이런 식으로 접근해서 `.env`같은 설정 파일이나 `/owa/auth/logon.aspx`같이 잘 알려져 있거나 기본 값으로 흔히 쓰는 경로를 무작위로 그리고 지속적으로 요청합니다. 경험 상 일주일도 되지 않았는데 약 2,400회 정도의 접근 시도가 있었습니다. 대표적인 접근 경로로 대략 아래와 같은 것이 있습니다. 접근하는 OS나 브라우저, 툴도 다양합니다.   

- /Autodiscover/Autodiscover.xml
- /index.php
- /mifs/.;/services/LogService
- /api/jsonws/invoke
- /bag2
- /api/.env
- /actuator/health
- /_layouts/15/Picker.aspx
- /Orion/WebResource.axd
- /console/
- /Autodiscover/Autodiscover.xml
- /vendor/phpunit/phpunit/src/Util/PHP/eval-stdin.php

이외에도 정말 다양한 경로를 몇 개 IP에서 지속적으로 요청합니다. 이런 봇에 의한 무작위 공격(?)을 받게 되면 짜증도 짜증이지만 Sentry 무료 용량 제한을 순식간에 넘어버리게 됩니다. 그래도 Sentry가 처음 한 번은 너그럽게 넘어가줍니다. 다음 번에는 얄짤없다는 경고와 유료 플랜 전환 권고도 합니다.  

### 문제2 - ALLOWED_HOSTS를 건드리지 마세요.

위에 찍힌 에러 로그를 보면 "15.164.18.00 아이피가 유효하지 않은 HTTP_HOST이니 `ALLOWED_HOSTS`에 등록이 필요할수도 있다" 이런 식입니다. 그래서 이런 에러가 귀찮기도 하고 자꾸 wsgi로 띄운게 연결도 안되고 하면 `ALLOWED_HOSTS`에 저 IP를 등록해버리는 경우가 생깁니다. 이러면 절대 안됩니다. 저 에러는 장고가 보안 장치로 쓸데없는 접근을 잘 막아냈다는 뜻이기도 합니다. 더 심하게는 `*` 처럼 전체 접근을 열어버리는 경우가 생기는데 최악으로 가는 지름길일 수 있습니다. Django가 애써 만들어둔 보안 장치를 스스로 해제하는 꼴입니다. 일부에서 `ALLOWED_HOSTS` 관련 에러만 뜨면 `*`을 입력하면 된다고 하는 글이 자주 보이는데 절대 안됩니다. 내 도메인이나 접근이 유효한 주소만 등록해야 합니다.   

---

# 해결방법

방화벽에서 해당 IP를 거를 수도 있고, nginx에서 차단 IP로 등록할수도 있고, Django에서 해당 IP를 서비스 거부토록 만들수도 있습니다만 여기서는 nginx에서 내가 지정한 도메인 빼고 다 HTTP 코드 444(nginx용)을 던져주는 방법을 소개합니다.  

아래 구문은 `nginx` 설정에서 `server` 블럭 안에 넣어보세요.  

{% highlight nginx %}
if ($host !~* ^(example.com)$) {
    return 444;
}
{% endhighlight %}

또는 이런 식으로 서브 도메인도 지정할 수 있습니다.  

{% highlight nginx %}
if ($host !~* ^(example.com|www.example.com)$) {
    return 444;
}
{% endhighlight %}

뭔가 좀 복잡한 것 같은데 느낌표는 부정(Not)의 의미이고, 물결(~)은 대소문자 구분, 물결별(~*)은 구분하지 않는다는 의미입니다. 캐럿(^)과 달러($)표시는 정규식의 시작과 끝을 나타냅니다.  

이렇게 하면 지긋지긋한 접근시도가 Sentry에 기록될 기회조차 얻지 못하고 차단됩니다. 제대로 동작하는지 확인하려면 `curl`을 이용해 확인해보세요. 위 설정을 `nginx`에 적용하기 전과 후에 각각 테스트해보면 확실히 비교가 됩니다.  

`curl -k --verbose --header 'Host: 123.123.123.123' 'https://example.com`  

`-k`는 안전하지 않은 SSL 접근을 허용하겠다는 뜻이고, `--verbose`는 과정에서 발생하는 일을 상세히 알려달라는 것이고, `--header`는 그뒤에 따라오는 설정으로 요청을 보내는 것입니다. 그 뒤의 도메인을 대상이고요.  

위 설정을 적용하기 전에 이 명령으로 테스트해보면 Sentry에 그대로 찍힙니다. 그러나 적용 후 다시 테스트해보면 아무런 예외 로그도 찍히지 않습니다.    

---

- `ALLOWED_HOSTS`에 아무 IP나 등록하거나 `*`을 입력하지 마세요. 
- 맥에서 `curl`을 설치하고 싶다면 `brew install curl`하시면 됩니다.
- 나쁜 IP `54.180.71.125`, `15.164.18.37`. AWS 쓰는 것 같은데 왜 아마존은 제제하지 않는걸까..?  

