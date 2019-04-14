---
layout: post
title: Let's Encrypt 무료 SSL인증서 워드프레스에 설치하고 https 적용하기
tags: webserver ssl
comments: true
---

# 개요

### Let's Encrypt란?

[Let's Encrypt](https://letsencrypt.org/)는 SSL 인증서를 무료로 발급해주는 CA(Certificate Authorities)입니다. [여러 글로벌 기업의 후원](https://letsencrypt.org/sponsors)을 받고 있으며 모질라(Mozilla) 재단에서 '신뢰할 수 있는 인증 기관(Trusted CA)' 으로 인증도 받았습니다. 따라서 베리사인(VeriSign)이나 코모도(Comodo)와 같은 유명 인증 업체와 같은 신뢰도를 가지며 SSL 암호화 기술 방식과 동작도 정확히 동일합니다. 단지 다른 것은 사이트의 인증에 문제가 있어 최종 웹사이트 방문자가 피해를 입었을 경우 배상 여부만 차이가 있을 뿐입니다. [여기](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)를 누르면 추가자거인 SSL과 CA에 대한 쉽고 간략하게 설명한 글을 볼 수 있습니다.  


### Certbot

Let's Encrypt의 SSL인증서는 [ACME(Automatic Certificate Management Environment)](https://en.wikipedia.org/wiki/Automated_Certificate_Management_Environment) 프로토콜을 준수하는 프로그램을 이용해 발급을 받을 수 있는데 Let's Encyrpt는 [Certbot](https://certbot.eff.org) 사용을 권장하고 있습니다. [여기](https://letsencrypt.org/docs/client-options)를 방문하면 ACME를 지원하는 기타 프로그램 목록을 볼 수 있습니다. 참고로 2016년 5월까지 Certbot은 `letsencrypt` 나 `letsencrypt-auto`로 불렸습니다.  
  
Certbot을 시스템 관리자가 서버에서 실행하는 프로그램입니다. 시스템 관리자는 Certbot을 통해 인증서를 직접 요청할 수 있습니다. 다양한 웹서버와 운영체제에 적합한 패키지 역시 배포하고 있습니다. [이 페이지](https://certbot.eff.org/)에서 자신의 환경에 맞는 패키지를 검색하고 설치하는 방법을 쉽게 찾을 수 있습니다. Certbot에 대한 문서는 [여기](https://certbot.eff.org/docs/)를 참고하세요.

---

# 발급 방법

인증서를 발급하는 방법은 크게 2가지로 나눌 수 있습니다. 쉘을 통해 직접하는 것, 그리고 호스팅 업체에서 제공하는 기능(cPanel 등을 통해)을 이용하는 것.  


### 쉘(SSH)로 발급받기

쉘 엑세스 권한이 있다면 [Certbot](https://certbot.eff.org/) ACME 클라이언트 사용을 추천합니다. Certbot은 인증서 발급과 설치를 갑작스런 중단없이 설치할 수 있도록 자동화 해줍니다. 자동 설정을 원치않는 고급 사용자를 위한 전문가 모드도 사용 가능하고 사용하기 쉬우며 많은 운영체제에서 동작합니다. 문서화도 훌륭하게 되어 있습니다. [여기](https://certbot.eff.org/)를 방문하시면 여러분의 운영체제와 웹서버에 맞는 사용법을 얻을 수 있습니다.  


### 쉘(SSH)없이 발급받기

Let's Encrypt를 쉘 접근없이 사용하는 최고의 방법은 호스팅 업체에서 제공하는 내장 기능을 사용하는 것입니다. 여러분이 쓰는 호스팅 업체가 Let's Encrypt를 지원한다면 여러분을 대신해 설치하고 최신 버전으로 유지하는 등을 과정을 자동으로 해줄겁니다. 몇몇 호스팅 업체의 경우에는 이 기능을 켜기 위해 설정이 필요하기도 하고 자동으로 해주기도 합니다.  
  
[여기](https://community.letsencrypt.org/t/web-hosting-who-support-lets-encrypt/6920)에 Let's Encrypt를 지원하는 호스팅 업체 목록이 있습니다.
  
여러분의 호스팅 업체가 Let's Encrypt를 지원하지 않는다면 해당 업체에 Let's Encrypt 기능 지원을 요청하세요. 저희도 최선을 다해 해당 업체에 적합한 방법을 지원할겁니다.  
  
여러분이 사용하는 호스팅 업체에서 Let's Encrypt를 지원할 의사는 없지만 개별 인증서 업로드를 허용하는 경우라면 로컬 머신에 Certbot을 설치한 다음 [수동 모드](https://certbot.eff.org/docs/using.html#manual)를 사용할 수 있습니다. 수동 모드에서는 소유권 확인을 위해 특정 파일을 업로드할 수 있고, Certbot은 호스팅 업체에 업로드할 수 있는 인증서를 탐색합니다. 그렇지만 이 방법은 웬만하면 추천하지 않습니다. 시간을 많이 잡아먹기도 하고 인증서가 만료될 때 마다 매년 수 차례 이 과정을 반복해야 합니다. 사용하시는 호스팅 업체에 Let's Encrypt 지원을 요청을 다시 한 번 생각해보시거나, 호스팅 업체 교체를 검토해보세요.  


---

# Certbot 이용하기

여기서는 쉘로 인증서를 발급 받는 방법에 대해 설명합니다.

### 시스템 요구사항

-   파이썬 2.7 또는 3.4 이상의 유닉스 기반 운영체제
-   root 권한  
     : `/etc/letsencrypt`, `/var/log/letsencrypt`, `/var/lib/letsencrypt`에 쓰기 권한이 필요하고, 웹서버 설정을 수정하기 위해 필요합니다. 이론적으로 root 권한 없이 설치도 가능하지만 여기서는 설명하지 않습니다. root 권한 없이 설치하기는 Certbot 매뉴얼을 참고하세요.
-   `certbot-auto`를 사용하는 경우 512MB 이상의 RAM  
     : 의존성 파일 설치가 필요하기 때문입니다. 사전 빌드된 운영체제를 사용하는 경우 스왑 파일을 이용하는 방법을 찾아보세요.

### 여러가지 설치 방법

##### Certbot-Auto

`certbot-auto`는 Certbot 설치 랩퍼(wrapper)로 의존성 파일을 웹서버에서 찾아 사용하고 파이썬 가상환경에 나머지를 집어넣는 등의 역할을 합니다. 아래와 같이 사용할 수 있습니다.
  
**설치**

```
user@webserver:~$ wget https://dl.eff.org/certbot-auto
user@webserver:~$ chmod a+x ./certbot-auto
user@webserver:~$ ./certbot-auto --help
```

**무결성 검사**

```
user@webserver:~$ wget -N https://dl.eff.org/certbot-auto.asc
user@webserver:~$ gpg2 --keyserver pool.sks-keyservers.net --recv-key A2CFB51FA275A7286234E7B24D17C995CD9775F2
user@webserver:~$ gpg2 --trusted-key 4D17C995CD9775F2 --verify certbot-auto.asc certbot-auto
```

### 기타

아래와 같은 설치도 제공합니다.

-   [도커를 통한 설치](https://certbot.eff.org/docs/install.html#running-with-docker)
-   [기타 운영체제 패키지를 통한 설치](https://certbot.eff.org/docs/install.html#running-with-docker)
-   [소스를 통한 설치](https://certbot.eff.org/docs/install.html#installing-from-source)

---

정말 특별한 요구사항이 없는 이상 패키지 매니저를 통한 Certbot설치가 간편하지만, 이런 패키지를 사용하지 못하는 환경에서는 Certbot 설치 프로세스를 자동화해주는 `certbot-auto`를 이용할 수 있습니다. 특히 웹서버 중단없이 SSL인증서를 발급, 적용하CHECK

# 전체 과정

1. .well-known 디렉토리와 .htaccess 파일 생성
2. certbot 다운 및 실행
3. 아파치 설정 변경 및 재시작
4. https 리다이렉트
5. wp-config.php 변경

# 세부 과정

## 1.well-known 디렉토리와 .htaccess 파일 생성

워드프레스가 설치된 루트 디렉토리에 .well-known 디렉토리를 만들고 .htaccess 파일을 생성한다.

-   **.well-known 디렉토리**:

## 2. certbot 다운 및 실행

```
# curl -O
```

## 3. 아파치 설정 변경 및 재시작

## 4. https 리다이렉트

## 5. wp-config.php 변경

---

---

**함께보면 좋은 글:**

-   [SSL 인증서는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)
-   [mod_rewrite는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-mod-rewrite/)
-   [.htaccess 파일은 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess/)
-   [.well-known 디렉토리는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/well-known-directory/)

**참고링크**

-   [Let's Encrypt - Getting Started](https://letsencrypt.org/getting-started/)
