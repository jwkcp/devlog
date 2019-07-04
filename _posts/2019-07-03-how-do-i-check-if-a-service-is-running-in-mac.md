---
layout: post
title: 맥(mac)에서 systemctl와 같이 서비스를 관리하는 명령이 없을까?
tags: mac terminal
comments: true
---

맥에서 ```brew```로 프로그램을 설치하고 나니 우분투의 systemd 명령인 systemctl 같이 서비스 실행 상태를 볼 수 있는 명령이이 없을까 궁금했다. 아니 궁금하기보단 불편했다.   

---

## **brew**로 할 수 있다.
예를 들어, 메시지 브로커인 RabbitMQ를 설치하고 동작 상태를 본다고 가정하고 관련 명령을 살펴보자.  

---

## 명령 모음
**설치**  
```brew install rabbitmq```   

**시작**  
```brew services start rabbitmq```  

**중지**  
```brew services stop rabbitmq```  

**재시작**  
```brew services restart rabbitmq```  
  
**상태보기**  
```brew services list``` or ```brew services list | grep rabbitmq```  

> 위의 명령에서 service**s**와 같이 끝에 s가 붙으니 잊지 말자.   

