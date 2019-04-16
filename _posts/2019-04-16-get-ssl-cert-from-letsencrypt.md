---
layout: post
title: Let's Encrypt 무료 SSL인증서 발급 받기 (어렵지 않아요)
tags: webserver ssl
comments: true
---
    
얼마 전 구글 크롬이 HTTPS를 지원하지 않는 사이트에 접속하면 주소 옆에 '주의요함'이라는 라벨을 붙이기 시작했습니다. 더불어 많은 사이트들이 https 지원을 위한 SSL 인증서를 적용하려고 하고 있는데요. 처음 SSL 인증서를 적용하려고 알아보면 Verisign, Codomo 이런 사이트들이 나오면서 몇 십에서 많게는 몇 백만원까지 가격이 붙어 있는 것을 보고 깜짝 놀라게 됩니다. [Let's Encyrpt는 글로벌 기업의 후원](https://letsencrypt.org/sponsors/)을 받고 있는 무료 SSL 발급 CA입니다. 아래 포스팅을 읽어보시면 SSL과 Let's Encrypt가 어떤 식으로 인증서를 발급하는지 알 수 있습니다.    
      
* [SSL 인증서는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)
* [SSL인증서가 필요해요. 그런데 Let's Encrypt가 무엇인가요?](https://devlog.jwgo.kr/2019/04/16/what-is-letsencrypt)
* [Let's Encyrpt는 어떻게 SSL인증서를 발급해줄까?](https://devlog.jwgo.kr/2019/04/12/how-lets-encrypt-works/)

---
## 순서
1. 아파치 설치 및 설정
2. Certbot 설치 
3. Certbot으로 인증서 발급
4. https를 위한 아파치 가상 호스트 설정
5. 방화벽 설정
6. 아파치 재시작
      
---
## 전제조건
이 글은 ubuntu 16.04, Apache 2.4.18을 기준으로 설명하지만 다른 환경에서도 대소동이 할 것입니다.   

---
     
## [1/6] 먼저 아파치를 설치하고 간단한 설정을 해봅시다.
1. 패키지 업데이트
```
sudo apt update
```
2. 아파치 설치
```
sudo apt install apache2
```
3. 아파치 설정
```/etc/apache2/sites-available/자기설정파일명(예를 들어, 000-default.conf 같은)``` 을 열고 ServerName, ServerAdmin, DocumentRoot 를 수정합니다. 
     
* **ServerName**: 도메인 주소 (예를 들어, example.com)    
* **ServerAdmin**: 관리자 이메일 
* **DocumentRoot**: 웹서버가 서비스할 소스가 있는 디렉토리 경로 (예를 들어, 워드프레스를 설치하고 있다면 htdocs의 경로)
    
그리고 사용할 파일이 ```/etc/apache2/sites-enabled``` 디렉토리에 심볼링 링크로 연결되어 있는지 확인합니다. ```/etc/apache2/sites-enabled``` 디렉토리 안에 위에서 설정한 .conf 의 파일명과 같은 파일이 보이면 됩니다. 안 보인다면 ```sudo a2ensite 자기설정파일명```을 하면 ```/etc/apache2/sites-enabled``` 디렉토리에 자동으로 심볼릭 링크됩니다.    

---

## [2/6] Certbot을 설치합시다.
Let's Encrypt에서는 SSL인증서를 받을 때 Certbot이라는 프로그램을 이용하길 권장합니다. Certbot은 SSL인증서를 발급받는 여러 에이전트 중 하나인데 [Certbot 이외에도 IETF 표준인 ACME 프로토콜을 준수하는 다른 발급 프로그램](https://letsencrypt.org/2019/03/11/acme-protocol-ietf-standard.html)을 쓸 수도 있습니다. 하지만 Certbot을 쓰시길 권장합니다.   
     
Certbot을 설치하려면 패키지 매니저를 이용할 수도 있고, 그냥 최신 버전을 다운로드 받아서 쓸 수도 있습니다. Let's Encrypt에서는 특별한 이유가 없으면 '패키지 매니저에서 제공하는 Certbot 패키지'를 사용하고, 그게 안될 경우 certbot-auto를 다운 받아 사용하라고 되어 있습니다. 일부 사용자들은 패키지 매니저에 있는 것은 버전 업데이트가 빨리 빨리 안될 수 있으니 ```cerbot-auto```를 받아쓰는 것이 좋다고 말하는 사람도 있다는 것 알아두세요^^ 아래는 2가지 설치 방법입니다. 둘 중에 원하는 방식을 선택하여 설치하면 됩니다.   
      
> Unless you have very specific requirements, we kindly suggest that you use the Certbot packages provided by your package manager (see certbot.eff.org). If such packages are not available, we recommend using certbot-auto, which automates the process of installing Certbot on your system. ([원문링크](https://certbot.eff.org/docs/install.html))    
          
### 1순위 - 패키지 매니저에서 제공하는 Certbot 패키지 사용
```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository universe
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install certbot python-certbot-apache 
```
     
### 2순위 - certbot-auto를 다운로드하여 사용
```
user@webserver:~$ wget https://dl.eff.org/certbot-auto
user@webserver:~$ chmod a+x ./certbot-auto
```
      
---
   
## [3/6] Certbot으로 인증서 발급하기
### 1순위 - 패키지 매니저에서 제공하는 Certbot 패키지를 사용하기로 했다면 -
```
$ sudo certbot --apache certonly
```
    
### 2순위 - certbot-auto를 사용하기로 했다면 -
certbot-autof르 다운받은 경로로 이동한 다음
    
```
$ sudo ./certbot-auto --apache certonly
```

#### [주의!] certonly를 꼭 붙여주세요.
**certonly 옵션을 주는 이유**는 certbot보고 "인증서 발급만 하고 다른 아파치 설정은 건드리지 마!" 이렇게 말하는 겁니다. certonly를 빼고 명령을 실행하면 예상치 못한 일이 생길 수 있으니 주의해야 합니다. 특히 비트나미(bitnami)와 같이 미리 빌드된 워드프레스를 사용하는 경우 기본적으로 아파치 사용부터 설정까지 많은 부분이 다른데 certonly를 빼고 명령을 실행하면 bitnami의 아파치가 아니라 다른 아파치가 실행되어 자신의 워드프레스 접속이 안될 수 있습니다.
     
#### [참고] webroot?!
**webroot**는 certbot이 인증서를 설치하는 방법 중 하나입니다. webroot를 이용하려면 [.well-known](https://devlog.jwgo.kr/2019/04/12/what-is-well-known-directory/) 폴더를 만들고 [.htaccess](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess/)파일을 만들어 해당 디렉토리의 옵션을 설정해서 Certbot이 도메인을 인증하는 첼린지(Challenge)를 수행합니다. 위의 방법을 이용하면 Certbot이 스스로 이런 첼린지를 수행할 권한을 설정 후 첼린지가 완료되면 다시 원상복구합니다. 그러니 이것도 특별한 이유가 없다면 위에 1순위 방법을 이용하면 편리합니다. 인증서 발급 과정이 궁금하다면 [여기](https://devlog.jwgo.kr/2019/04/12/how-lets-encrypt-works/)를 눌러 글을 더 읽어보세요.
     
--

이렇게 하면 위에 아파치 가상 호스트(Virtual Host)설정에 입력되어 있는 도메인이 보기로 나옵니다. 번호를 눌러 선택하세요. 만약 도메인이 안나온다면 아파치 가상 호스트 설정에 ServerName을 확인하세요. 뭔가 잘못되었을 수 있습니다. (그냥 도메인을 수동으로 입력해도 인증서는 발급 받을 수 있지만 어차피 해당 도메인을 https로 서비스 할 것이기 때문에 아파치 설정을 먼저 한 것입니다.)
    
그리고 아래 3가지 과정이 나옵니다. 
    
* 이메일 입력
* 이용 약관 동의 (A:동의)
* Let's Encrypt의 뉴스레터 수신 여부 (Y:동의 N:거부) - (선택사항) 거부해도 상관없으니 원하는 대로
     
여기까지 마치면 이(```/etc/letsencrypt/live/자기가설정한도메인명```) 경로에 인증서 파일 4개가 생성됩니다.    
    
* cert.pem
* privkey.pem
* chain.pem
* fullchain.pem

---

## [4/6] 이제 https 서비스를 위한 가상 호스트를 설정할 차례입니다.
지금까지 아파치 웹서버 설정했고, Certbot 다운받아 인증서 발급까지 마쳤습니다. 이제 남은 건 https 가상 호스트와 방화벽 설정 뿐입니다. 거의 다 왔어요. 힘내세요.   
    
```/etc/apache2/sites-available``` 디렉토리에 가면 기본적으로 생성된 default-ssl.conf가 있을겁니다. 복사를 해서 써도 좋고 자신이 원하는 설정을 사용해도 좋습니다. 이 파일을 열고 아래 설정을 넣어줍니다.   

* **ServerAdmin**: 관리자 이메일
* **DocumentRoot**: 웹서버가 서비스할 소스가 있는 디렉토리 경로 (예를 들어, 워드프레스를 설치하고 있다면 htdocs의 경로), 위에서 설정한 것과 같습니다.
    
그리고 이제 대망의 SSL 설정을 넣어줍니다. 여기에 발급받은 키 경로를 입력하면 끝입니다. 이미 입력되어 있는 상태이기 때문에 주석(# 표시)을 풀어주면 될거에요. 경로는 아래와 같습니다.
    
```
SSLEngine on
SSLCertificateFile      /etc/letsencrypt/live/자기도메인주소/cert.pem
SSLCertificateKeyFile   /etc/letsencrypt/live/자기도메인주소/privkey.pem
SSLCertificateChainFile /etc/letsencrypt/live/자기도메인주소/chain.pem
```    

이제 ssl을 활성화하고 설정도 사용할거라고 표시해줍니다.
```
sudo a2enmod ssl
sudo a2ensite 여러분의SSL설정파일명(예를 들어, default-ssl.conf)
```

## [5/6] 방화벽 설정
### 호스팅 사이트 방화벽 확인
호스팅 사이트에서 방화벽이 열려있는지 확인합니다. 예를 들어, AWS lightsail의 경우 '네트워킹' 탭에 가면 방화벽 설정이 있습니다. AWS lightsail은 기본적으로 http(80), ssh(22)만 열려있기 때문에 https(443)도 추가해줘야 합니다.    
     
### 우분투 방화벽 확인
운영체제에도 방화벽이 있습니다. 기본적으로 꺼져있으나 우리는 방화벽을 켠 다음 원하는 포트만 열겠습니다.    

```
# 방화벽 활성화
$ sudo ufw enable

# 방화벽 상태보기
$ sudo ufw status verbose

# 방화벽 열기
$ sudo ufw allow http
$ sudo ufw allow https
$ sudo ufw allow ssh
```

## [6/6] 아파치 재시작
    
```
$ sudo systemctl restart apache2
```
     
짠! 이제 https로 접속해보세요. 

---

짝짝짝! 고생하셨어요.     
http를 https로 강제 리다이렉트하는 방법이나 SSL 인증서를 자동으로 갱신 방법을 쉽게 설명한 아래 글도 읽어보세요.

* [http를 https로 리다이렉트하기](https://devlog.jwgo.kr/2019/04/16/redirect-https)
* [Let's Encrypt SSL인증서 자동 갱신하기](https://devlog.jwgo.kr/2019/04/16/how-to-lets-encrypt-ssl-renew)
      
---

**함께보면 좋은 글:**

* [SSL 인증서는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)
* [mod_rewrite는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-mod-rewrite/)
* [.htaccess 파일은 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess/)
* [.well-known 디렉토리는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/well-known-directory/)