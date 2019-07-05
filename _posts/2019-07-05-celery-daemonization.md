---
layout: post
title: 데몬으로 셀러리(Celery) 돌리기
tags: celery systemd
comments: true
---

이 문서는 [Celery 공식 문서 중 데몬화하기](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html)에서 Systemd 일부를 발췌하여 실제 데몬으로 돌리기까지 과정에서 어려움을 겪을 만한 부분에 설명을 더한 것입니다.
     
---

**이 글과 관련된 시리즈 글:**  
- [장고(Django)에서 셀러리(Celery) 사용하기 1편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-1)   
- [장고(Django)에서 셀러리(Celery) 사용하기 2편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-2)   
- [장고(Django)에서 셀러리(Celery) 사용하기 3편](https://devlog.jwgo.kr/2019/07/02/using-celery-with-django-3)   
- [장고(Django)에서 셀러리(Celery)로 크론탭처럼 주기적으로 작업 시키키 초간단 정리](https://devlog.jwgo.kr/2019/07/03/do-periodic-task-using-celery-with-django/)
    
---

셀러리를 단순히 실행하려면 ```celery -A PROJECT_NAME worker -l info -B```와 같은 명령이면 됩니다. 하지만 실 서비스 환경에서는 이렇게 실행하면 프로세스가 갑자기 죽거나 했을 때 적절한 대응이 어렵습니다. 그래서 ```systemd```나 ```supervisord```와 같은 서비스 실행 관리자를 쓰거나 셀러리 공식 문서 중 [Daemonization](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#usage-systemd)의 ```Init-script```의 방법으로 셀러리를 서비스합니다.   

셀러리 공식 문서에는 크게 아래 4가지 방법으로 데몬으로 셀러리를 서비스하는 방법이 나와 있습니다. 이 글에서는 ```systemd```로 서비스하는 방법에 대해서만 설명합니다. 다른 방법으로 서비스하는 방법은 공식 문서를 참고하세요.  

- Init-script 사용하기
- systemd 사용하기
- supervisord 사용하기
- launchd (macOS) 사용하기  

---

먼저 여기서 셀러리(Celery)라고 하는 것은 아래 두 개 프로그램을 통칭한다고 보면 됩니다.   

- ```celery```: 요청을 큐에 담아 비동기적으로 처리
- ```celerybeat```: 크론탭(crontab)처럼 주기적으로 실행이 필요한 작업을 브로커 큐에 담아줌

데몬으로 셀러리를 실행할 때도 위 두 개에 대한 스크립트와 서비스를 각각 작성, 실행해주어야 합니다. 아래에서 그 세부 설정을 볼 수 있습니다. 더 읽어 보시죠.  

---

```Init-script```는 ```init.d```에 스크립트를 작성해 넣으면 ```systemd```가 제공하는 systemd-sysv 호환성 레이어를 이용해 서비스하는 방법입니다. 실행 시 root 권한이 필요합니다.   

---

```systemd```는 ```systemctl {start|stop|restart|status} celery.service```의 명령을 쓰는 서비스, 네 여러분이 알고 계시는 그것 맞습니다. 실행에 필요한 스크립트와 설정 경로는 아래와 같습니다.   

- 스크립트: ```/etc/systemd/systed/celery.service```, ```/etc/systemd/systed/celerybeat.service```
- 설정: ```/etc/conf.d/celery```

---

## 설치 과정

### 사전 작업 (1/4)

#### 1. C_FORCE_ROOT 환경 변수 설정
셀러리는 기본적으로 root 유저로 실행되지 않는다는 것을 전제로 합니다. 셀러리는 메시지로 직렬화(serialize)된 피클(pickle, 파이썬에서 사전형과 같은 자료구조를 저장하기 위한 한 방법)를 수신할 수 있는데 여기에 악의적인 코드가 포함되어 root로 실행되면 매우 위험한 일이 생길 수 있기 때문입니다. (셀러리를 데몬으로 구동 시 본인의 코드가 피클을 매개변수로 받는 등의 작업이 있다면 이 부분에 각별히 신경을 써야 합니다.)     

그런데 systemd는 root 유저로 실행되니, 셀러리를 systemd로 돌리려면 root 계정으로 실행되야 하는 일이 생깁니다. 그래서 명시적으로 root 권한으로 셀러리를 돌리겠다는 표시를 해야 하는데 이게 ```C_FORCE_ROOT```입니다. ``` export C_FORCE_ROOT='true` ```와 같이 해주면 되는데 앞으로 계속 이 환경 변수가 필요하므로 ```.bashrc```와 같이 본인 쉘의 설정에 넣어 항상 실행되도록 만들어 줍니다.   

이 환경 변수 설정 없이 그냥 셀러리를 실행하면 정상적으로 실행된 것 같은 화면이 나오지만 서비스는 실행 즉시 죽어버리는 현상이 발생합니다.  

---

#### 2. pid와 log를 위한 디렉토리 
셀러리 데몬 구동을 위한 서비스 스크립트와 설정을 아래에서 살펴보겠지만 ```var``` 디렉토리 안의 ```run```과 ```log``` 디렉토리를 사용합니다. 여기에 ```celery```라는 디렉토리에 ```pid```와 ```log```파일을 만들어 씁니다. 그런데 이 디렉토리는 root 권한만 쓸 수 있지요. 따라서 아래 작업 없이 그냥 서비스를 실행하면 ```No such file or directory```와 같은 에러 메시지를 만나게 됩니다.   

```/etc/tmpfiles.d/celery.conf``` 파일을 생성하고 아래 내용을 입력해주세요.   

```
d /var/run/celery 0755 celery celery -
d /var/log/celery 0755 celery celery -
```

그리고 이 설정을 적용하기 위해 ```systemd-tmpfiles --create``` 명령을 실행합니다. 이 명령 실행에 대한 부분이 셀러리 공식 문서에는 써 있지 않아 한참 애를 먹었네요. 리눅스에 좀 더 익숙하신 분들이라면 바로 보고 아셨겠지만요. : )   

---

### 설정 (2/4)
설정을 ```/etc/conf.d/celery```에 만들어줍니다. 공식 문서에는 주석으로 좀 더 많은 부연 설명이 있는데 여기서는 좀 간소화했습니다. 여러 설정들이 있지만 ```CELERY_BIN```와 ```CELERY_APP```을 제외하면 딱히 수정할 부분이 없을겁니다.   

```CELERY_BIN``` 에는 설치된 셀러리의 절대 경로를 써줍니다. 가상환경에 설치한 분은 가상환경 경로를 포함해 기재해야 합니다.   

```CELERY_APP``` 은 자기 코드의 프로젝트 명입니다.  

```
CELERYD_NODES="w1"
CELERY_BIN="/usr/local/bin/celery"
CELERY_APP="proj"
CELERYD_MULTI="multi"
CELERYD_OPTS="--time-limit=300 --concurrency=8"

# - %n will be replaced with the first part of the nodename.
# - %I will be replaced with the current child process index
#   and is important when using the prefork pool to avoid race conditions.
CELERYD_PID_FILE="/var/run/celery/%n.pid"
CELERYD_LOG_FILE="/var/log/celery/%n%I.log"
CELERYD_LOG_LEVEL="INFO"

# you may wish to add these options for Celery Beat
CELERYBEAT_PID_FILE="/var/run/celery/beat.pid"
CELERYBEAT_LOG_FILE="/var/log/celery/beat.log"
```

---

### celery.service (3/4)
```/etc/systemd/system/celery.service``` 경로에 아래 내용으로 파일을 만들어 줍니다. 이 파일 역시 손댈 부분이 많지 않습니다. 아래 나열한 항목만 바꿔줘도 충분합니다.  

- **User**: 서비스를 실행할 사용자입니다. 저의 경우 celery라는 사용자를 ```sudo adduser celery```로 만들어 주었습니다만 자신의 상황에 맞게 입력해주시면 됩니다.  
- **Group**: 위에 User와 같이 자신에 맞게 설정합니다. 저의 경우 celery라는 그룹도 ```sudo adduser celery```로 함께 만들면서 이걸 써줬습니다.  
- **WorkingDirectory**: 자신의 최상위 프로젝트 폴더명을 써주면 됩니다.  
- **Exec{Start|Stop|Reload}**: 명령 시작 부분에 ```/bin/sh```로 되어 있는데 자기가 쓰고 있는 쉘로 바꿔주세요.   

나머지는 앞서 설정한 ```/etc/conf.d/celery```의 값을 읽어와 알아서 설정됩니다.   

```
[Unit]
Description=Celery Service
After=network.target

[Service]
Type=forking
User=celery
Group=celery
EnvironmentFile=/etc/conf.d/celery
WorkingDirectory=/opt/celery
ExecStart=/bin/sh -c '${CELERY_BIN} multi start ${CELERYD_NODES} \
  -A ${CELERY_APP} --pidfile=${CELERYD_PID_FILE} \
  --logfile=${CELERYD_LOG_FILE} --loglevel=${CELERYD_LOG_LEVEL} ${CELERYD_OPTS}'
ExecStop=/bin/sh -c '${CELERY_BIN} multi stopwait ${CELERYD_NODES} \
  --pidfile=${CELERYD_PID_FILE}'
ExecReload=/bin/sh -c '${CELERY_BIN} multi restart ${CELERYD_NODES} \
  -A ${CELERY_APP} --pidfile=${CELERYD_PID_FILE} \
  --logfile=${CELERYD_LOG_FILE} --loglevel=${CELERYD_LOG_LEVEL} ${CELERYD_OPTS}'

[Install]
WantedBy=multi-user.target
```

---

### celerybeat.service (4/4)
```celery.service```와 대부분의 과정이 같습니다. ```/etc/systemd/system/celerybeat.service``` 경로에 파일을 만들고 아래 내용을 입력합니다. 수정해야 하는 부분도 ```celery.service```와 같습니다. 나머지는 그대로 두면 됩니다.     

```
[Unit]
Description=Celery Beat Service
After=network.target

[Service]
Type=simple
User=celery
Group=celery
EnvironmentFile=/etc/conf.d/celery
WorkingDirectory=/opt/celery
ExecStart=/bin/sh -c '${CELERY_BIN} beat  \
  -A ${CELERY_APP} --pidfile=${CELERYBEAT_PID_FILE} \
  --logfile=${CELERYBEAT_LOG_FILE} --loglevel=${CELERYD_LOG_LEVEL}'

[Install]
WantedBy=multi-user.target
```

---

### 끝
이제 ```sudo systemctl start celery.service```로 서비스를 구동하고 ```sudo systemctl status celery.service```로 잘 돌고 있는지 상태를 점검하세요. 참, 실행 스크립트를 생성하거나 수정한 다음에는 ```sudo systemctl daemon-reload```를 통해 파일이 변경되었음을 알려줘야 한다는 점도 잊지 마세요.     

자, 다시 정리해보겠습니다.   

1. ```C_FORCE_ROOT``` 환경변수와 ```tempfile``` 관련 설정을 해줍니다.
2. 사용할 설정을 ```/etc/conf.d/celery```에 해주고
3. ```/etc/systemd/system/celery.service``` 를 만들어 주면 끝입니다.
4. 참, 주기적으로 실행할 작업이 있다면 ```/etc/systemd/system/celerybeat.service```도 만들고 실행해주면 됩니다. (선택사항)

