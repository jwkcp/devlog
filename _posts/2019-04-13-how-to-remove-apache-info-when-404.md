---
layout: post
title: 404 Not Found 시 아파치 웹서버 정보 나타나지 않게 하기
tag: webserver
comments: true
---

아파치 설치 후 기본 설정 그대로 쓰면 404 Not Found 시 아파치 웹서버 정보가 나타난다. ```/etc/apache2/apache2.conf``` 파일에 아래 설정을 추가하면 나타나지 않는다.   
    
```
ServerSignature Off
ServerTokens ProductOnly
```
    