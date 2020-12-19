---
layout: post
title: usermod 이후에도 user도커(docker) 명령이 sudo 없이 동작하지 않을 때
tags: docker linux
comments: true
---

# 문제

아래와 같이 docker그룹에 사용자를 추가했는데도 `sudo`없이 `docker ps`와 같은 명령을 수행하면 권한이 없다는 오류가 뜬다.  

`sudo usermod -aG docker 사용자명`   

---

# 해결방법

`exit` 등으로 로그아웃 후 다시 로그인하면 `sudo`없이 `docker` 명령을 사용할 수 있습니다.  
