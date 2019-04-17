---
layout: post
title: Let's Encrypt SSL 인증서 자동 갱신 설정 방법
tags: webserver ssl
comments: true
---
    
Let's Encrypt에서 발급하는 인증서는 90일짜리 단기 인증서입니다. 3개월에 적어도 한 번은 갱신을 해야 한다는 뜻이기도 합니다. 아래는 이 인증서를 갱신하는 방법에 대해 알아보겠습니다.    
     
참, 그전에 [Let's Encrypt 무료 SSL인증서 발급 받기 (어렵지 않아요)](https://devlog.jwgo.kr/2019/04/16/get-ssl-cert-from-letsencrypt) 글을 보신 분들은 아시겠지만 패키지 매니저를 통해 설치하셨으면 ```certbot```을, wget으로 직접 다운로드하셨으면 ```certbot-auto```를 가지고 계실겁니다. 둘 다 명령어는 똑같습니다. ```certbot-auto```은 ```certbot```을 랩핑한 것이거든요.      
      
아래에서는 ```certbot```을 예를 들어 설명하겠지만 ```certbot-auto```를 설치하신 분들은 ```certbot```자리에 대신 ```certbot-auto```를 입력하시면 됩니다. ```certbot-auto```를 실행할 때는 파일이 있는 곳에서 실행하시거나, 전체 경로와 함께 넣으셔야 한다는 점도 잊지마세요~^^       
    
## 갱신 테스트
아래 명령어로 실제 갱신이 아니라 잘 갱신되는지, 명령에 오류가 나진 않는지 등을 테스트 해 볼 수 있습니다.
```
# 
$ certbot renew --dry-run
```

---

## 실제로 갱신하기
뭐 별 것 없습니다. ```--dry-run```만 빼고 실행하시면 됩니다.
```
$ certbot renew
```

---

     
## 인증서 만료일 확인하기
Certbot으로 부터 발급받은 인증서들에 대한 정보를 표시합니다.
```
$ certbot certificates
```

---

## 자동 갱신하기
그런데 이걸 3개월 알람을 맞춰두고 매 번 사람이 하는 것 까먹을 수도 있고 여간 불편한게 아닙니다. 걱정하지 마세요. 우리에겐 크론탭(Crontab)이 있습니다. Crontab은 정해진 일시에 반복적으로 특정 작업을 할 수 있는 프로그램이고 우분투에 기본적으로 설치되어 있습니다.    
     
간단한 Crontab 명령과 규칙을 살펴보겠습니다.    
    
**자주쓰는 명령 2개**
```
# Crontab 보기
$ sudo crontab -l

# Crontab 편집
$ sudo crontab -e

# Crontab 실행 로그
$ view /var/log/syslog
``` 
crontab 명령어 앞에 sudo를 써준 것은 root 권한의 크론탭을 수정하고 보겠다는 의미입니다. 각자 웹서버와 인증서를 관리하는 사용자 계정의 크론탭을 이용하면 됩니다. certbot을 그냥 설치했을 때 /etc/letsencrypt에 인증서를 쓸 권한이 필요하므로 여기서는 sudo 를 붙여주었습니다.    
        
**규칙**
![http://www.ostechnix.com/wp-content/uploads/2018/05/cron-job-format-1.png](https://www.ostechnix.com/wp-content/uploads/2018/05/cron-job-format-1.png)
    
위 그림처럼 분, 시, 일, 월, 요일, 명령 순서로 기재하면 됩니다.    
예를 들어, /home/user/run.sh를 실행하고 싶다면
        
```
# 매 시 10분에 
$ 10 * * * * /home/user/run.sh

# 10분 마다 주기적으로 
$ */10 * * * * /home/user/run.sh

# 토요일 새벽 3시에
$ 0 3 * * 6 /home/user/run.sh
```   
이렇게 하면 됩니다. 

---

## 인증서 갱신하기
만약 매월 1일 03시에 인증서를 갱신하고 싶다면 아래와 같이 설정하면 됩니다.   
```
$ 0 18 1 * * /usr/bin/certbot renew --renew-hook="sudo systemctl restart apache2"
```
     
위에 새벽 3시인데 크론탭에는 18시라고 쓴 것은 서버 시간에 맞췄기 때문입니다. ```date``` 명령으로 자신의 서버 시간을 확인하고 시간을 설정하세요. [이 페이지](https://www.worldtimebuddy.com/kst-to-utc-converter)에서 시간을 쉽게 변환해볼 수 있습니다.      
      
뒤에 나오는 [--renew-hook](https://certbot.eff.org/docs/using.html#pre-and-post-validation-hooks)은 인증서 갱신을 성공적으로 마치면 아파치를 재시작하기 위해 넣어주었습니다. 인증서 갱신 전에 실행되는 ```--pre-hook```도 있습니다.   
      
---

**실제 발급 과정을 따라하려면 아래 글을 참고하세요.**     
[Let's Encrypt 무료 SSL인증서 발급 받기 (어렵지 않아요)](https://devlog.jwgo.kr/2019/04/16/get-ssl-cert-from-letsencrypt)
      
**함께보면 좋은 글:**

* [SSL 인증서는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)
* [mod_rewrite는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-mod-rewrite/)
* [.htaccess 파일은 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess/)
* [.well-known 디렉토리는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/well-known-directory/)
    