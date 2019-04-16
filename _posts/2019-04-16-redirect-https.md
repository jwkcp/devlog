---
layout: post
title: 아파치 웹서버에서 http를 https로 리다이렉트하는 방법
tags: webserver ssl
comments: true
---
    
서버에서 ssl을 설정한 이후 리다이렉트를 설정하지 않은면 http://example.com 처럼 http로 접속한 사용자는 https 서비스를 이용하지 않게 됩니다. 아래와 같이 설정해주면 http로 접속하는 사용자들을 https로 리다이렉트 할 수 있습니다.   
     
### rewrite 활성화
```
$ sudo a2enmod rewrite
```
       
---
   
### http 가상 호스트(Virtual Host) 설정
http와 https 둘 다 서비스하고 있다면 ```/etc/apache2/sites-enabled```에 설정 2개가 들어 있을 거에요. 이 중에서 http 설정을 열어 아래와 같이 추가해 줍니다.   
    
```
# Rewrite 엔진을 켭니다.
RewriteEngine on

# HTTP로 요청이 들어오면
RewriteCond %{HTTPS} off

# HTTPS로 돌려줍니다. 앞의 .*은 정규식입니다. 맨뒤의 [R=301,L]은 301, 즉 영구 이동한 것으로 리다이렉트를 하고 조건에 따른 룰 적용은 여기서 끝낸다(L)는 뜻입니다. 콤마(,) 사이에 공백을 넣지 마세요.
RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [R=301,L]   
```
     
---

### 아파치 재시작
```
$ sudo systemctl restart apache2
```
이제 http://example.com 으로 접속하면 https://example.com으로 자동 이동합니다. 301 리다이렉트도 설정했으니 검색엔진도 색인 시 https로 된 주소를 검색 결과에 노출할 것이구요. 도움이 되셨나요? ^^

---

실제 발급 과정을 따라하려면 아래 글을 참고하세요.     
* [Let's Encrypt 무료 SSL인증서 발급 받기 (어렵지 않아요)](https://devlog.jwgo.kr/2019/04/16/get-ssl-cert-from-letsencrypt)
* [Let's Encrypt SSL인증서 자동 갱신하기](https://devlog.jwgo.kr/2019/04/16/how-to-lets-encrypt-ssl-renew)
      
**함께보면 좋은 글:**

* [SSL 인증서는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)
* [mod_rewrite는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-mod-rewrite/)
* [.htaccess 파일은 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess/)
* [.well-known 디렉토리는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/well-known-directory/)
    
**참고링크**
* [RewriteRule Flags](https://httpd.apache.org/docs/2.4/rewrite/flags.html)