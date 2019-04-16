---
layout: post
title: SSL인증서가 필요해요. 그런데 Let's Encrypt가 무엇인가요?
tags: webserver ssl
comments: true
---
    
## 개요
### Let's Encrypt란?

[Let's Encrypt](https://letsencrypt.org/)는 SSL 인증서를 무료로 발급해주는 CA(Certificate Authorities)입니다. [여러 글로벌 기업의 후원](https://letsencrypt.org/sponsors)을 받고 있으며 모질라(Mozilla) 재단에서 '신뢰할 수 있는 인증 기관(Trusted CA)' 으로 인증도 받았습니다. 따라서 베리사인(VeriSign)이나 코모도(Comodo)와 같은 유명 인증 업체와 같은 신뢰도를 가지며 SSL 암호화 기술 방식과 동작도 정확히 동일합니다. 단지 다른 것은 사이트의 인증에 문제가 있어 최종 웹사이트 방문자가 피해를 입었을 경우 배상 여부만 차이가 있을 뿐입니다. [여기](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)를 누르면 추가자거인 SSL과 CA에 대한 쉽고 간략하게 설명한 글을 볼 수 있습니다.

### Certbot

Let's Encrypt의 SSL인증서는 [ACME(Automatic Certificate Management Environment)](https://en.wikipedia.org/wiki/Automated_Certificate_Management_Environment) 프로토콜을 준수하는 프로그램을 이용해 발급을 받을 수 있는데 Let's Encyrpt는 [Certbot](https://certbot.eff.org) 사용을 권장하고 있습니다. [여기](https://letsencrypt.org/docs/client-options)를 방문하면 ACME를 지원하는 기타 프로그램 목록을 볼 수 있습니다. 참고로 2016년 5월까지 Certbot은 `letsencrypt` 나 `letsencrypt-auto`로 불렸습니다.

Certbot을 시스템 관리자가 서버에서 실행하는 프로그램입니다. 시스템 관리자는 Certbot을 통해 인증서를 직접 요청할 수 있습니다. 다양한 웹서버와 운영체제에 적합한 패키지 역시 배포하고 있습니다. [이 페이지](https://certbot.eff.org/)에서 자신의 환경에 맞는 패키지를 검색하고 설치하는 방법을 쉽게 찾을 수 있습니다. Certbot에 대한 문서는 [여기](https://certbot.eff.org/docs/)를 참고하세요.

---

## 발급 방법
인증서를 발급하는 방법은 크게 2가지로 나눌 수 있습니다. 쉘을 통해 직접하는 것, 그리고 호스팅 업체에서 제공하는 기능(cPanel 등을 통해)을 이용하는 것.

### 쉘(SSH)로 발급받기

쉘 엑세스 권한이 있다면 [Certbot](https://certbot.eff.org/) ACME 클라이언트 사용을 추천합니다. Certbot은 인증서 발급과 설치를 갑작스런 중단없이 설치할 수 있도록 자동화 해줍니다. 자동 설정을 원치않는 고급 사용자를 위한 전문가 모드도 사용 가능하고 사용하기 쉬우며 많은 운영체제에서 동작합니다. 문서화도 훌륭하게 되어 있습니다. [여기](https://certbot.eff.org/)를 방문하시면 여러분의 운영체제와 웹서버에 맞는 사용법을 얻을 수 있습니다.

### 쉘(SSH)없이 발급받기

Let's Encrypt를 쉘 접근없이 사용하는 최고의 방법은 호스팅 업체에서 제공하는 내장 기능을 사용하는 것입니다. 여러분이 쓰는 호스팅 업체가 Let's Encrypt를 지원한다면 여러분을 대신해 설치하고 최신 버전으로 유지하는 등을 과정을 자동으로 해줄겁니다. 몇몇 호스팅 업체의 경우에는 이 기능을 켜기 위해 설정이 필요하기도 하고 자동으로 해주기도 합니다.

[여기](https://community.letsencrypt.org/t/web-hosting-who-support-lets-encrypt/6920)에 Let's Encrypt를 지원하는 호스팅 업체 목록이 있습니다.

여러분의 호스팅 업체가 Let's Encrypt를 지원하지 않는다면 해당 업체에 Let's Encrypt 기능 지원을 요청하세요. 저희도 최선을 다해 해당 업체에 적합한 방법을 지원할겁니다.

여러분이 사용하는 호스팅 업체에서 Let's Encrypt를 지원할 의사는 없지만 개별 인증서 업로드를 허용하는 경우라면 로컬 머신에 Certbot을 설치한 다음 [수동 모드](https://certbot.eff.org/docs/using.html#manual)를 사용할 수 있습니다. 수동 모드에서는 소유권 확인을 위해 특정 파일을 업로드할 수 있고, Certbot은 호스팅 업체에 업로드할 수 있는 인증서를 탐색합니다. 그렇지만 이 방법은 웬만하면 추천하지 않습니다. 시간을 많이 잡아먹기도 하고 인증서가 만료될 때 마다 매년 수 차례 이 과정을 반복해야 합니다. 사용하시는 호스팅 업체에 Let's Encrypt 지원을 요청을 다시 한 번 생각해보시거나, 호스팅 업체 교체를 검토해보세요.
    
---

실제 발급 과정을 따라하려면 아래 글을 참고하세요.     
[Let's Encrypt 무료 SSL인증서 발급 받기 (어렵지 않아요)](https://devlog.jwgo.kr/2019/04/16/get-ssl-cert-from-letsencrypt)