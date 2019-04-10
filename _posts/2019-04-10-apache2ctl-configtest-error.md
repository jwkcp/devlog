---
layout: post
title: Error while running apache2ctl configtest 에러가 발생할 때 해결 방법
tags: apache
comments: true
---
    
```Error while running apache2ctl configtest``` 에러가 발생하는 경우 아파치 설정 파일(/etc/apache2/sites-enabled의 설정 등)에 문제가 있는 경우가 대부분이다. 아래의 명령으로 정확히 어떤 문제가 있는지 확인할 수 있다.
     
```
apache2ctl configtest
```
     
