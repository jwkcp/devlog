---
layout: post
title: Postgresql이 갑자기 안될 때
tags: postgresql
comments: true
---

## 문제

아래와 같은 에러를 뿜으며 postgresql이 죽는다. postgresql의 로그는 우분투 리눅스의 경우 `/var/log/postgresql/postgresql-버전번호-main.log` 에서 볼 수 있다.

> 2019-00-00 00:00:00 KST [1469-2] LOG: received fast shutdown request  
> 2019-00-00 00:00:00 KST [1469-3] LOG: aborting any active transactions  
> 2019-00-00 00:00:00 KST [1481-2] LOG: autovacuum launcher shutting down  
> 2019-00-00 00:00:00 KST [1478-1] LOG: shutting down  
> 2019-00-00 00:00:00 KST [1478-2] LOG: database system is shut down  
> 2019-00-00 00:00:00 KST [27487-1] FATAL: could not map anonymous shared memory: 메모리를 할당할 수 없습니다  
> 2019-00-00 00:00:00 KST [27487-2] HINT: This error usually means that PostgreSQL's request for a shared memory segment exceeded available memory, swap space, or huge pages. To reduce the request size (currently 148488192 bytes), reduce PostgreSQL's shared memory usage, perhaps by reducing shared_buffers or max_connections.  
> 2019-00-00 00:00:00 KST [1782-1] LOG: database system was shut down at 2019-00-00 00:00:00 KST

---

## 원인

아래 에러 메시지에 원인과 해결책이 있다.

> 2019-00-00 00:00:00 KST [27487-2] HINT: This error usually means that PostgreSQL's request for a shared memory segment exceeded available memory, swap space, or huge pages. To reduce the request size (currently 148488192 bytes), reduce PostgreSQL's shared memory usage, perhaps by reducing shared_buffers or max_connections.

메모리가 부족해서 서비스가 죽었다는 말이다.

---

## 해결방법

1. free -m 명령으로 현재 메모리 상태를 본다. (뒤에 -m 옵션은 메가바이트 단위로 보겠다는 뜻이다.)

2. `/etc/postgresql/버전번호/main/postgresql.conf` 설정 파일에서 약 113번 째 줄부터 메모리 관련 설정이 있다. 여기에서 `shared_buffters = 128MB`이런 식으로 되어 있는 값을 바꿔주면 된다. 만약 `free -m`으로 봤는데 `buff/cache`메모리가 110 이런 식으로 써져있으면 이 값보다 좀 넉넉하게 작게 설정해준다. (물론 자신이 운영하는 서비스 특성에 따라 다를 수 있다.) 나의 경우 `buff/cache`가 103이었고, postgresql 설정에서 `shared_buffers` 값을 60MB로 바꿔주고 postgresql 서비스를 재시작해주었다.

---

AWS 라이트셰일 \$3.5/m 서비스의 메모리가 512MB인데 이렇게 메모리 오류가 나는 경우는 저사양의 서버에 기본값으로 서비스를 올려서 그런 경우가 종종 있으니 참고.
