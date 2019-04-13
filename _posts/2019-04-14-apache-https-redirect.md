---
layout: post
title: 아파치에서 https 리다이렉트 설정하기
tag: webserver
comments: true
---

아파치에서 https 리다이렉트를 시킬 경우 아래와 같이 설정하면 됩니다. 그 전에 [a2enmod rewrite 로 활성화하지 않으면 에러가 발생](https://devlog.jwgo.kr/2019/04/14/rewrite-engine-syntax-error-on-apache)하니 주의하세요.

```
# Rewrite 엔진을 활성화합니다.
RewriteEngine On

# 요청이 HTTPS가 아니면
RewriteCond %{HTTPS} off

# 모든 요청 문자열을 https로 다이렉트합니다. 이때 301로 영구 리다이렉트 시켜 검색 봇 색인에 새로운 https 주소를 알립니다. L은 조건에 따른 영향이 하위로 전달되지 않고 이 라인에서 끝낸다는 플레그(flag)입니다.
RewriteRule ^(.*)$ https://${SERVER_NAME} [R=301,L]
```
