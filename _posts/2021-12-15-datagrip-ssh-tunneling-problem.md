---
layout: post
title: Jetbrain Datagrip에서 SSH 터널링 시 접속 안되는 문제
tags: tools database
comments: true
---

# 상황

SSH 터널링 사용 시 Mysql workbench로는 잘 접속되지만 Datagrip에서 연결되지 않을 때

---

# 원인

TLS 프로토콜 설정이 안되어 있어서 그렇습니다.

---

# 해결 방법

1. Drivers의 설정 중 Advanced탭을 엽니다.
2. *enabledTLSProtocols*의 값에 *TLSv1.2*를 입력합니다.
3. 다시 Test Connection을 해보면 정상적으로 접속됨을 확인할 수 있습니다.
