---
layout: post
title: 여러 도메인을 한 서버에서 운영할 때 http -> https 리다이렉트가 이상하게 되는 경우
tags: apache ssl
comments: true
---

### 증상

a.com 과 b.com 두 개 도메인을 한 서버에서 운영하려고 합니다. 아파치 가상 호스트를 이용해 http로 각 페이지를 따로 띄우는 것까지는 문제가 없었습니다. 그리고 SSL인증서를 발급받은 후 https 가상 호스트를 설정한 다음 http가 https로 연결되도록 Rewrite를 통해 설정해 주었습니다. 마지막으로 a2ensite로 a.com 그리고 b.com을 각각 SSL 아파치 .conf 설정 파일을 sites-enable로 연결해주었구요.

그런데 이게 웬일, a.com은 정상적으로 http가 https로 리다이렉트되며 연결이 되는데 b.com은 http가 https로 리다이렉트 되면서 페이지가 정사적으로 뜨지 않고 크롬 주소창에 https에 붉은 색 x표시가 나타나며 사이트에 문제가 있다는 메시지가 나왔습니다. 무엇이 문제일까요.  
  
왜 a.com은 http, https 둘 다 잘 연결이 되는데 b.com은 엉뚱한 곳으로 https 연결이 되는 걸까요? 이런 질문이 외국 커뮤니티에도 많이 올라왔지만 명확한 답을 찾지 못했었습니다.  


### 원인

아마 이런 문제를 겪는 분들은 십중팔구 아파치에 기본적으로 있던 default-ssl.conf를 그대로 썼을 겁니다. 앞 부분만 가져와 봤습니다. 여기서 필요한 부분에 알맞은 값을 넣고 사용했을 것이구요.

```
<IfModule mod_ssl.c>
  <VirtualHost _default_:443>
    ServerAdmin webmaster@localhost

    DocumentRoot /var/www/html

    # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
    # error, crit, alert, emerg.
    # It is also possible to configure the loglevel for particular
    # modules, e.g.
    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    ...
```

아래는 많이 사용하는 http 아파치 설정 파일의 리다이렉트 구문입니다. 조건을 보면 **요청된 URL이 https가 아니면** `https://%{SERVER_NAME}%{REQUEST_URI}`로 이동하도록 하고 있습니다. 위에서 a.com을 먼저 a2ensite를 통해 활성화시켰고 b.com은 그 다음에 처리했습니다. 아파치로 요청이 들어오면서 http://b.com 도 a.com의 리다이렉트 조건에 걸려 엉뚱하게 리다이렉트 되면서 인증서가 달라졌고 문제가 생긴 것입니다.  


```
RewriteEngine on
RewriteCond %{HTTPS} off
RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [R=301,L]
```

다시 제일 위에 아피치 기본 `default-ssl.conf` 설정 부분을 봅시다. 여기에는 `ServerName` 부분이 없습니다. 기본적으로 구성되어 있는 SSL 설정 파일의 항목에 값만 바꾸면서 ServerName을 입력하지 않았고 여러 도메인의 https 리다이렉트에 문제가 생긴 것입니다. 단일 도메인을 사용하여 http를 https로 리다이렉트하거나, 여러 도메인을 이용하여 가상 호스트를 통해 http 여럿을 서비스하는 것은 아무 문제가 없습니다. 아파치 기본 설정의 항목에 맞춰 값만 바꿔써도 문제가 없지요.  
  
그런데 여러 도메인을 http에서 https로 리다이렉트하면서 서비스를 제공하고자 할 때는 ServerName이 빠지자 문제가 생겼습니다.

# 해결방법

이와 동일한 문제가 있는 분들은 SSL 아파치 설정 파일에도 적절한 `ServerName`항목을 넣고 아파치를 재시작하시면 원하는데로 도메인 별 https 리다이렉트가 되는 것을 알 수 있습니다.
