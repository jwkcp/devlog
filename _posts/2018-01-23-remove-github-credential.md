---
layout: post
title: 윈도우 또는 맥에서 깃허브(github) 인증 정보 삭제하기
tags: github, credential, how-to 
comments: true    
---

윈도우나 맥에서 깃허브 저장소를 복제하여 가지고 오거나 새로 생성 한 후 다시 깃허브 저장소로 푸시(Push) 하려고 하면 깃허브 인증 정보(사용자 이름, 비밀번호)를 넣어야 한다. 그리고 이런 식으로 한번 인증되고 나면 다음번 푸시할때는 다시 묻지 않게 된다. 하지만 어떤 이유로 윈도우 또는 맥에서 인증 정보를 삭제해야 한다면 어떻게 해야 할까?

답은 각 운영체제의 키 관리 메뉴에 있다.

### 윈도우
[**Windows 자격증명관리**] 
  
### 맥
[**키체인 관리**]
 
---
       
위 메뉴에 들어가 보면 github.com으로 관리되고 있는 키 정보를 볼 수 있다. 이 항목을 삭제하면 다음에 깃허브 저장소로 푸시하려 할 때 다시 한 번 인증 정보를 묻게 된다.
    

  
