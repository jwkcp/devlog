---
layout: post
title: mod_rewrite는 도대체 뭔가요?
tag: webserver
comments: true
---
    
mod_rewrite의 모듈 식별자는  rewrite_module이고 소스 파일명은 mod_rewrite.c입니다. 즉석에서 요청된 URL을 규칙 기반으로 재작성하여 제공하는 기능을 합니다.    
    
예를 들어 워드프레스에서 '고유주소'를 사용자 설정으로 변경하고 싶을 때(example.com/?p=1 이렇게 생긴 주소를 example.com/1 이런 식으로) [.htaccess 파일](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess)을 생성하고 규칙 내용을 작성하게 됩니다. 이 때 조건으로 mod_rewrite가 사용됩니다. 아래처럼 말이죠.   
    
```
<IfMoudle mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f 
RewriteCond %{REQUEST_FILENAME} !-d
</IfMoudle>
```
    
위의 설정에서 다른 부분은 보지 마시고 제일 첫 번째 조건만 보시기 바랍니다. 지금 우리는 mod_rewrite에 대해서 이야기하고 있으니까요. ```IfModule```는 '이 모듈이 있으면'이란 조건문입니다. 여기에 mod_rewrite.c가 보이시죠? 이렇게 mod_rewrite를 이용하면 워드프레스가 example.com/?p=1 이렇게 온 요청(request)를 example.com/1 이런 식으로 즉석에서 바꿀 수 있게 해줍니다. 규칙에 기반해서 권한을 주는 거죠.    

---

**함께보면 좋은 글:**   
* [SSL 인증서는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)
* [.htaccess 파일은 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess/)
* [.well-known 디렉토리는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/well-known-directory/)

---

참고링크:
[http://httpd.apache.org/docs/current/mod/mod_rewrite.html](http://httpd.apache.org/docs/current/mod/mod_rewrite.html)