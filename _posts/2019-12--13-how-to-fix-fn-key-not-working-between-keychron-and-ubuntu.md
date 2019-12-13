---
layout: post
title: 우분투에서 키크론(Keychron) 기계식 키보드 fn키 동작 문제 해결 방법
tags: ubuntu
comments: true
---

## 문제

키크론 키보드에서 F1~F12 키가 정상적으로 동작하지 않는다. fn키를 누르고 해도 마찬가지로 동작하지 않고 멀티미디어 키로만 동작한다.  
  
---

## 원인

일단 키크론에서 공식적으로 윈도우, 맥에 대해서만 공식적으로 지원 표기를 하고 있는데 리눅스 호환성 때문이 아닌가 추측함.  

---

## 해결방법

1. 터미널을 열고  
2. `/sys/module/hid_apple/parameters` 경로에 `fnmode`파일의 내용을 2로 수정  
3. 저장 후 다시 fn키를 누르고 F1~F12를 눌러보면 정상적으로 동작함  
  
---

## 참고링크

키크론에서 이런 문제를 얘기하고 해결할 수 있도록 페이스북 그룹을 만들어두었는데 문제 발생 시 참고하면 도움이 됩니다.  

[https://www.facebook.com/groups/Keychronlinux/](https://www.facebook.com/groups/Keychronlinux/)  
