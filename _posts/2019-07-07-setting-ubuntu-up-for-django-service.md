---
layout: post
title: 장고(Django) 서비스를 위한 AWS lightsail 우분투 환경 설정하기
tags: django ubuntu
comments: true
---

## 시스템 업데이트/업그레이드 하기

`sudo apt update` 후 `sudo apt upgrade`  
`sudo apt upgrade` 명령을 하면 선택 사항이 몇 나오는데 기본값(예를 들어 GRUB 업그레이드 같은 <- 이건 그냥 현 시스템 유지 선택)을 선택.

---

## 로케일 설정하기

기본 로케일을 `ko_KR.UTF-8`로 설정해준다. `sudo dpkg-reconfigure locales`로 설정하면 쉽다. 자세한 내용은 [우분투에서 locale(이하 '로케일') 설정하기](https://devlog.jwgo.kr/2018/06/01/how-to-set-locale-on-ubuntu/) 참고.

---

## 파이썬 버전 맞추기

`pyenv`로 원하는 파이썬 버전을 설치해는게 앞으로도 편하고 좋다. 설치는 [우분투(Ubuntu)에 pyenv 설치하기](https://devlog.jwgo.kr/2019/07/07/installing-pyenv-in-ubuntu/), 설치 도중 문제가 있는 경우 [pyenv로 파이썬 설치 시 발생하는 오류와 해결 방법 (이거 하나면 끝!)](https://devlog.jwgo.kr/2019/06/05/must-installed-lib-when-installing-python-using-pyenv/)를 참조하면 된다. pyenv 공식 문서는 [여기](https://github.com/pyenv/pyenv). 아래는 간단한 요약이다.

```bash
$sudo apt install build-essential libffi-dev libbz2-dev zlib1g-dev libreadline-dev libsqlite3-dev

$git clone https://github.com/pyenv/pyenv.git ~/.pyenv

$echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
$echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
$echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc

$exec "$SHELL"

$pyenv install 3.6.8

$pyenv shell 3.6.8

$echo 'export PYENV_VERSION=3.6.8` >> ~/.bashrc
```

---

## rabbitmq 설치하기 (선택사항)

1. 설치
   `sudo apt install rabbitmq-server`

2. 서비스 확인
   `sudo systemctl status rabbitmq-server`

3. 큐(queue)에 얼마나 작업(task)가 쌓였는지 보기
   `sudo rabbitmqctl list_queues`

4. 큐(queue)에 쌓인 작업 날리기
   `sudo rabbitmqctl purge_queue celery` (뒤의 celery 부분에 큐 이름을 넣습니다.)

---

## redis 설치하기 (선택사항)

1. 설치
   `sudo apt install redis-server`

2. 서비스 확인
   `sudo systemctl status redis-server`  
   `redis-cli ping`하면 `PONG`나오면 성공

3. 데이터 조회
   `redis-cli get 키이름` 하면 키에 해당하는 값이 조회됨.

---

## git에서 소스 복사

`ssh-keygen -t rsa`로 서버에서 키를 발급받고 깃허브 저장소 설정 > 배포키에 추가해주면 된다. 자세한 내용은 [git 프라이빗 저장소 원격지 서버에 클론하는 방법
](https://devlog.jwgo.kr/2019/07/07/how-to-clone-private-repo-into-remote-server/) 참고.

> 잠깐!

1. 배포 전 장고의 `DEBUG = True` 를 `DEBUG = False`로 바꿔주셨는지 확인하세요.
2. 장고의 ALLOWED_HOSTS에 올바른 값을 넣었는지 확인하세요. `ALLOWED_HOSTS = ['*']`는 모든 클라이언트를 접근을 허용합니다. 웹서버를 사용한다면 웹서버에서만 접근하도록 `ALLOWED_HOSTS = ['웹서버도메인주소']` 와 같이 해주고 웹서버 설정에서 `proxy_set_header Host $http_host`처럼 설정해주면 웹서버가 접근한 URL을 gunicorn이 서비스하는 WSGI서버에 그대로 전달하게 됩니다.

---

## 가상환경 설치 후 활성화

`python -m venv .venv` 후 `source .venv/bin/activate`

---

## 의존성 설치

`pip install -r requirements.txt`

---

## 셀러리 설치

`pip install celery`

더 자세한 셀러리 설치 방법은 [장고(Django)에서 셀러리(Celery) 사용하기]({% post_url 2019-07-02-using-celery-with-django-1 %})와 [장고(Django)에서 셀러리(Celery)로 크론탭처럼 주기적으로 작업 시키키 초간단 정리]({% post_url 2019-07-03-do-periodic-task-using-celery-with-django %})를 참고하세요.

--

## 셀러리 데몬으로 돌리기

1. C_FORCE_ROOT 환경변수와 tempfile 관련 설정을 해줍니다.
2. 사용할 설정을 /etc/conf.d/celery에 해주고
3. /etc/systemd/system/celery.service 를 만들어 주면 끝입니다.
4. 참, 주기적으로 실행할 작업이 있다면 /etc/systemd/system/celerybeat.service도 만들고 실행해주면 됩니다. (선택사항)

> 주의하세요!
> 위에 5번째 줄을 보면 `concurrency=8`이라고 되어 있는 부분이 있습니다. 동시성 옵션인데 동시에 몇 개의 워커를 생성해서 큐의 밀린 작업을 처리하게 할지 정하는 옵션입니다. 소량의 작업을 주기적으로 실행한다면 1로 해도 괜찮습니다. 아마존 라이트셰일(lightsail) 월 \$3.5 짜리 서버는 concurrency 옵션에 8을 주면 터미널 명령이 먹지 않을 정도로 느려지는 형상을 경험할 수 있습니다.

자세한 내용은 [데몬으로 셀러리(Celery) 돌리기]({% post_url 2019-07-05-celery-daemonization %}) 글을 참고하세요.

---

## 웹서버, WSGI서버 설정하기

지금까지 장고(Django) 프로젝트를 서비스하기 위해 몇 가지 작업을 했습니다.

1. 시스템 업그레이드
2. 로케일 설정
3. 파이썬 설치
4. rabbitmq (메시지 큐 브로커, 선택사항) 설치
5. redis (메모리 디비, 선택사항) 설치
6. 소스 복사
7. 가상환경 및 의존성 설치
8. 데몬으로 셀러리 돌리기

--

이 작업들은 내부적으로 프로그램이 실행되는 과정에 대한 것이었습니다. 이제 이렇게 작업한 결과물을 WSGI서버와 웹서버를 통해 외부에 공개할 차례입니다. 아시다시피 웹서버는 Nginx나 Apache와 같이 정적 데이터를 서비스하고 사용자의 초기 요청을 라우팅해주는 역할을 합니다. WSGI는 **W**eb **S**erver **G**ateway **I**nterface의 약자로 Gunicorn이나 uWSGI와 같은 프로그램이 있습니다. WSGI는 웹 애플리케이션 서버가 앞서 이야기한 웹서버와 어떻게 할지 정해놓은 명세로 우리가 파이썬을 이용해 장고 프로젝트에 짠 소스가 실행되며 동적 컨텐츠를 서비스 할 수 있게 해줍니다.

여기서는 웹서버로 Nginx, WSGI서버로 Gunicorn을 사용합니다. 아래 또는 [AWS Lightsail에 nginx, gunicorn, systemd를 이용해 장고(Django) 서비스 배포해보기]({% post_url 2019-06-05-how-to-set-server-to-serve-django %})를 참고하세요.

---

### Gunicorn 설치 및 설정

`pip install gunicorn`으로 gunicorn을 설치해줍니다. 그리고 `/etc/systemd/system` 경로에 `gunicorn.service`라는 파일을 만들고 아래와 같이 설정한 후 실행(`sudo systemctl start gunicorn`), 서비스 등록(`sudo systemctl enable gunicorn`) 해줍니다. 실행은 말그대로 서비스를 실행하는 것이고, 등록은 서버 재시작 시에도 자동으로 실행되도록 해줍니다. 잘 실행되고 있는지 확인(`sudo systemctl status gunicorn`)해보세요.

#### 프로젝트 레이아웃

> MYPROJECTDIRECTORY  
> ㄴ mydjango  
> ㄴ manage.py  
> ㄴ run  
> ㄴ staticfiles

#### systemd 스크립트

```config
[Unit]
Description=Gunicorn Service
After=network.target

[Service]
User=ubuntu
Group=ubuntu
WorkingDirectory=장고프로젝트경로(예를 들어, /home/ubuntu/MYPROJECTDIRECTORY)
ExecStart=/home/ubuntu/MYPROJECTDIRECTORY/.venv/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/MYPROJECTDIRECTORY/run/gunicorn.sock MYPROJECTDIRECTORYNAME.wsgi:application

[Install]
WantedBy=multi-user.target
```

---

### Nginx 설치 및 설정

`sudo apt install nginx`로 설치하고 `/etc/nginx/sites-enabled` 파일을 `gunicorn`과 연결해줍니다.

```nginx
upstream backend {
        server unix:장고프로젝트경로/run/gunicorn.sock;
}

server {
        listen 80 default_server;
        server_name 서비스할도메인;

        access_log /var/log/nginx/서비스할도메인-access.log;
        error_log /var/log/nginx/서비스할도메인-error.log;

        location = /favicon.ico {
                return          204;
                access_log      off;
                log_not_found   off;
        }

        location / {
                include proxy_params;

                # upstream을 쓰지 않을 경우, 이렇게 설정할수도 있습니다.
                # proxy_pass http://unix:장고프로젝트경로/run/gunicorn.sock;

                # upstream을 쓸 경우 위의 backend 이름을 이용해 아래와 같이 표현할수도 있습니다.
                proxy_pass http://backend;
        }
}

```

`sudo systemctl restart nginx`로 웹서버 재시작하여 설정을 반영해줍니다. `sudo nginx -t` 명령으로는 nginx 설정에 문제가 있는지 검사할 수 있습니다.
