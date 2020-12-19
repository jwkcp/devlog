---
layout: post
title: docker-compose가 Can't find a suitable configuration... 에러가 나는 경우 원인과 해결방법
tags: docker linux
comments: true
---

# 문제

터미널에서 `docker-compose ps`를 실행했는데 아래와 같은 오류가 나는 경우  

```
Can't find a suitable configuration file in this directory or any parent. Are you in the right directory?

Supported filenames: docker-compose.yml, docker-compose.yaml
```

---

# 원인

`docker-compose`는 프로젝트를 기반으로 동작하기 때문에 `.yml`이나 `.yaml`의 확장자를 가지는 파일이 없는 곳에서는 위와 같은 에러가 발생합니다.  

---

# 해결방법

`.yml`이나 `.yaml`의 확장자를 가지는 파일이 있는 프로젝트 디렉토리 내에서 실행해보세요.  
