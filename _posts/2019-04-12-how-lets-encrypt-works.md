---
layout: post
title: Let's Encyrpt는 어떻게 SSL인증서를 발급해줄까?
tag: security
comments: true
---
   
**이 글을 읽기 전에 [여기](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)를 방문하여 SSL과 CA가 무엇인지에 대해 쉽고 간략한 설명을 먼저 읽어 보시면 큰 도움이 됩니다.**
    
Let's Encrypt와 ACME 프로토콜은 사람의 개입없이 브라우저 신뢰 인증서를 자동으로 발급하고 HTTPS 서버 설정을 가능하도록 만드는데 목적이 있습니다. 이건 웹서버에서 도는 인증서 관리 에이전트(Certificate management agent)가 있기 때문에 가능한 것이지요.   
     
이 기술이 어떻게 가능한지 이해하기 위해, Let's Encrypt에서 제공하는 인증서 관리 에이전트를 이용해 https://example.com/ 의 설정 과정을 함께 살펴보기로 하죠.   
     
이 과정을 두 단계로 설명할 수 있습니다.    
    
* **1단계: 인증** - 에이전트가 [CA](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/) 에게 도메인이 온전히 웹서버의 통제 하에 있는 것을 증명하는 것   
* **2단계: 발급** - 에이전트가 도메인에 대한 인증서를 요청, 갱신, 폐기하는 것
      
### 도메인 검증
Let's Encrypt는 공개 키로 서버 관리자를 식별합니다. 최초 에이전트 소프트웨어는 Let's Encyrpt와 통신하여 새로운 키 쌍을 생성하고, Let's Encrypt CA에게 서버가 하나 또는 그 이상의 도메인의 통제권을 가지고 있는 것을 증명합니다. 이 과정은 지금까지 SSL인증서를 발급을 위해 계정을 생성하고 거기에 도메인을 추가하는 등 CA들이 했던 전통적인 과정과 흡사합니다.   
     
인증서 발급을 위해 에이전트는 Let's Encrypt CA에게 example.com 도메인이 서버의 통제 하에 있는지 증명하기 위해 필요한 것들을 요청합니다. 그러면 Let's Encrypt CA는 요청한 도메인을 검증하기 위해 하나 또는 그 이상의 첼린지 셋트를 발급합니다. "너 진짜야? 그럼 내가 퀴즈 낼테니 맞춰봐" 하는 것처럼 요청한 도메인을 테스트하여 검증하려는 것이죠. 이렇게 검증하는 방법을 여기서는 첼린지라고 부릅니다. 이 때 에이전트는 아래 2가지 방법 중 하나를 선택할 수 있습니다.   
    
* example.com의 DNS 레코드를 탐색하는 방법
* https://example.com 도메인의 [well-known](https://devlog.jwgo.kr/2019/04/12/well-known-directory/) URI예서 HTTP 리소스를 탐색하는 방법 
     
이런 첼린지와 함께, Let's Encrypt CA는 에이전트가 키 쌍을 제어할 수 있다는 것을 증명하기 위해 개인 키 쌍으로 서명되길 원하는 임시 파일을 제공합니다. 아래 그림을 한번 보세요.   
    
![https://letsencrypt.org/images/howitworks_challenge.png](https://letsencrypt.org/images/howitworks_challenge.png)
    
에이전트가 Let's Encrypt CA로 부터 요구받은 첼리지를 완료합니다. 두 번째 탐색 방법을 완료할 수 있다고 가정해보겠습니다. 우선 https://example.com 사이트의 특정 경로에 파일 하나를 만듭니다. 에이전트는 제공된 서명용 임시 파일을 개인 키를 이용해 서명합니다. 이 과정을 에이전트가 완료하면 CA에게 검증이 완료되었다고 알립니다.    
    
CA는 이제 첼린지가 성공했는지 체크하고 제공된 임시 파일의 서명을 확인합니다. 그리고 웹서버로 부터 파일을 다운로드하여 제대로 내용이 작성되었는지 점검합니다.    
    
![https://letsencrypt.org/images/howitworks_authorization.png](https://letsencrypt.org/images/howitworks_authorization.png)
    
서명이 유효하고 첼린지까지 성공했음을 확인하면, 공개 키로 식별된 에이전트는 example.com의 인증서 관리를 허가받게 됩니다. 이 때 example.com의 키 쌍을 "허가된 키 쌍(authorized key pair)"이라고 부릅니다.    
      
### 인증서 발급과 폐지
에이전트가 인증된 키 쌍을 가지게 되면 요청, 갱신, 폐지하는 것은 간단한 과정입니다. 인증된 키 쌍을 가지고 메시지에 서명에서 전송하면 끝이죠.   
     
도메인의 인증서를 얻기 위해 에이전트는 Let's Encrypt에게 지정된 공개키로 example.com 도메인의 인증서 발급 요청을 요구하는 '인증서 서명 요구(Certificate Signing Request)'를 만듭니다. 이걸 CSR이라고 부를께요. CSR은 보통 CSR의 공개 키에 해당하는 개인 키의 서명이 포함됩니다. 에이전트는 Let's Encrypt CA에게 허가 여부를 알리기 위해 example.com의 허가된 키로 전체 CSR에 서명합니다.    
    
Let's Encrypt CA가 요청을 받게 되면, 양쪽 서명을 모두 확인합니다. 문제가 없으면 CSR의 공개키로 exmaple.com의 인증서를 발급하고 에이전트에게 리턴합니다.   
    
!https://letsencrypt.org/images/howitworks_certificate.png](https://letsencrypt.org/images/howitworks_certificate.png)    
    
인증서 폐기도 비슷합니다. 에이전트는 example.com의 허가된 키로 인증서 폐기 요청을 하고, Let's Encrypt CA는 요청이 허가된 것이지 여부를 확인합니다. 맞다면, OCSP와 같은 일반 폐기 채널을 통해 폐기를 진행합니다. 이렇게 하면 브라우저와 같이 신뢰할 수 있는 당사자가 폐기된 인증서를 받아들이면 안된다는 것을 알 수 있게 됩니다.    
    
![https://letsencrypt.org/images/howitworks_revocation.png](https://letsencrypt.org/images/howitworks_revocation.png)
     
*이 글은 [Let's Encrypt의 How It Works](https://letsencrypt.org/how-it-works/)라는 글을 번역한 것입니다.*
    
---

**함께보면 좋은 글:**   
* [SSL 인증서는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)
* [mod_rewrite는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-mod-rewrite/)
* [.htaccess 파일은 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess/)
* [.well-known 디렉토리는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/well-known-directory/)

**참고링크**
* [Let's Encrypt - How It Works](https://letsencrypt.org/how-it-works/)