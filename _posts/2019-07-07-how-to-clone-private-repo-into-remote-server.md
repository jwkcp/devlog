---
layout: post
title: git 프라이빗 저장소 원격지 서버에 클론하는 방법
tags: git
comments: true
---

## 문제

프라이빗 저장소를 서버에 클론하려고 하면 당연히 권한 때문에 클론이 안된다. 어떻게 해야 할까?

## 방법

클론하려는 서버에 ssh 키를 발급받고 해당 저장소 배포키(Deploy keys)에 공개키(Public key)를 등록하면 된다.

## 상세

1. 서버에서 키 발급
   `ssh-keygen -t rsa`

2. 서버에서 발급한 키 클립보드에 복사
   ~/.ssh 위치에 `id_rsa.pub` (기본 키 명칭일 경우) 의 텍스트를 복사.

3. 깃허브에 공개키 추가
   깃허브의 `Settings > Deploy keys` 에 발급한 공개키 텍스트를 복사.
