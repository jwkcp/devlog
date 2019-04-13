---
layout: post
title: 아파치 설정 시 RewriteEngine에서 Syntax error가 발생하는 경우 해결 방법
tag: webserver
comments: true
---

## 문제

아파치 https 리다이렉트 등을 위해 아래와 같이 설정 후 apachectl configtest를 하면 에러가 발생하는 경우가 있습니다.

#### 설정 예시

(아래 설졍 예시에 대한 설명을 보려면 [여기](https://devlog.jwgo.kr/2019/04/14/apache-https-redirect)를 클릭하세요.)

```
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://${SERVER_NAME} [R=301,L]
```

#### 발생 에러

```
AH00526: Syntax error on line 40 of /etc/apache2/sites-enabled/000-default.conf:
Invalid command 'RewriteEngine', perhaps misspelled or defined by a module not included in the server configuration
Action 'configtest' failed.
The Apache error log may have more information.
```

---

## 원인

`rewrite` 가 활성화되지 않아서 그렇습니다.

## 해결방법

```
sudo a2enmod rewrite
```
