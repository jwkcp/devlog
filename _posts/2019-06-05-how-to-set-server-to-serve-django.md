---
layout: post
title: AWS Lightsail에 nginx, gunicorn, systemd를 이용해 장고(Django) 서비스 배포해보기
tags: python django gunicorn systemd nginx
comments: true
---

장고(이하 Django)로 서비스 개발을 시작해보면 처음엔 ```python manage.py runserver```로 쉽게 서버를 동작시킨다. 하지만 ```runserver```는 실 운영(production)에서는 사용하지 말라고 안하던가. 그래서 찾아보면 wsgi 규약을 준수하는 웹 애플리케이션 서버(이하 WAS)가 필요하고 uWSGI나 Gunicorn 같은 것을 찾아보게 된다.     
    
단순히 이것만 있는게 아니다. 동적 페이지를 WAS가 서비스하고 html이나 css, js 같은 정적 리소스들은 웹서버(이하 WEB)에게 맡겨야 하니 nginx나 apache같은 것이 필요하다. 또 이들이 죽지 않게, 아니 죽으면 다시 살려주면서 서비스 관리를 해줄 프로세스 모니터링 도구들, supervisord나 systemd, upstart, runit 등의 도구가 필요하다. 이뿐인가. 보통 Django 프로젝트는 가상환경 안에서 실행하도록 구성하는 경우가 많으니 가상환경에서 도는 것도 염두에 두어야 한다. 이렇게 실제 AWS lightsail이나 EC2 등 호스팅 서버에 올리려고 하면 부딪히는 난관이 한 두개가 아니고 시간도 많이 잡아먹게 된다.      
     
이 글이 이런 과정을 쉽게 단축시켜주거나 하는 것은 아니다. 단지 전체 과정이 어떤 식으로 되는지 쭉 살펴보면서 막연한 두려움을 없애고 전체 과정을 따라하며 배우는데 도움이 되었으면 좋겠다는 생각을 해본다. 모든 과정은 하나 하나 해보면서 궁금한 점들을 해소하고 기록하였다.     
      
---
   
# 환경
- OS: Ubuntu 16.04     
- 파이썬: 3.x

---
      
# 전체과정
1. 호스팅 서버 생성
2. 패키지 업데이트
3. 파이썬 준비
   - pyenv 설치 (여러 버전 파이썬을 사용하기 위함이니 그럴 필요 없다면 설치 안해도 됨)
   - pyenv 필수 라이브러리 설치 (역시 마찬가지)
4. 가상환경 생성
5. Django 설치
6. Django 프로젝트 생성 및 기타 작업
7. Gunicorn 설치 및 설정
8. Systemd 설정
9. Nginx 설치 및 설정
10. 테스트

---

# 상세
## 1. 호스팅 서버 생성
여러 서비스가 있으니 입맛에 맞는 서비스로 선택   

---
    
## 2. 패키지 업데이트
서버를 생성하고 ssh로 접속한 후 ```sudo apt update``` 실행    

---
     
## 3. 파이썬 준비
pyenv를 사용하지 않고 서버에 설치된 혹은 설치가 안되어 있다면 직접 설치해도 되지만 여러 버전의 파이썬을 쉽게 전환하고 쓰기 위해 여기서는 pyenv를 사용하겠습니다.    
     
**pyenv 설치**   
```git clone https://github.com/pyenv/pyenv.git ~/.pyenv```    
     
**경로 환경설정**
```vi .bashrc```로 연 다음 아래 내용 추가     
    
```
export PYENV_ROOT=”$HOME/.pyenv”
export PATH=”$PYENV_ROOT/bin:$PATH”
eval “$(pyenv init -)"
```   
저장하고 닫은 후 ```source .bashrc```로 쉘 재실행    
           
**pyenv로 파이썬 설치를 위한 필수 라이브러리 설치**    
```
sudo apt install zlib-dev bzip2 bzip2-dev readline-dev sqlite3 sqlite3-dev libssl-dev xz-utils
```
   
**간단한 pyenv 명령 소개**     
```
# 무슨 명령 있나 보기
pyenv

# 설치 가능한 파이썬 목록 보기
pyenv install --list

# 설치된 파이썬 목록 보기
pyenv versions

# 현재 선택된 파이썬 보기
pyenv version

# 특정 버전 파이썬 설치하기 (예를 들어, 3.6.8 버전이면)
pyenv install 3.6.8

# 특정 버전 파이썬 삭제하기 (예를 들어, 3.6.8 버전이면)
pyenv uninstall 3.6.8
```

---

## 4. 가상환경 생성
가상환경은 위치는 프로젝트 폴더에 해도 되고 별도의 장소에 해도 된다. 자신의 가상환경을 프로젝트 마다 공유할지 특정 프로젝트 별로 사용할지 등에 따라 결정하면 된다. 여기서는 ```~/.venv```에 ```myvenv```라는 이름으로 설치해보자.    
       
```
python -m venv ~/.venv/myvenv
```
      
---

## 5. Django 설치
```
pip install django
```
   
## 6. Django 프로젝트 생성 및 settings.py 수정
**Django 프로젝트 생성**    
```django-admin.py startproject mydjango```    
    
**settings.py 수정**
이 과정을 하면 나중에 nginx에서 정적 파일을 서비스하는 것까지 확인할 수 있습니다. ```settings.py```를 열고 맨 아래 STATE_URL 밑에 추가.         
```
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```   
     
**정적파일 모으기**
```
python manage.py makemigrations
python manage.py migrate
```
     
**수퍼유저 생성하기**
꼭 하지 않아도 되지만 어드민 접속해보기 위해     
```
python manage.py createsuperuser
```
      
---

## 7. Gunicorn 설치 및 설정
아래와 같이 ```gunicorn.service``` 파일 생성. 위치는 상관 없지만 여기서는 mydjango 디렉토리 아래 run이라는 디렉토리를 만든 후 여기에 넣어주겠습니다. 꼭 run이 아니고 fuck으로 이름 지어도 상관없습니다. 폴더 구조를 보면 이런 상태입니다.    
```
mydjango
  - mydjango
  - manage.py
  - run
  - staticfiles
```

```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=ubuntu
Group=ubuntu
WorkingDirectory=/home/ubuntu/apps/mydjango
ExecStart=/home/ubuntu/.venv/myvenv/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/apps/mydjango/run/mydjango.sock mydjango.wsgi:application

[Install]
WantedBy=multi-user.target
```
```WorkingDirectory```에는 Django 프로젝트 루트 디렉토리 경로를, ```ExecStart```에는 gunicorn 실행부터 옵션을 설정합니다. 가상환경에서 gunicorn을 설치했을 경우 꼭 가상환경의 절대 경로를 넣어줘야 합니다.    

---

## 8. Systemd 설정
이 상태에서 systemd를 이용해 gunicorn을 실행하겠습니다.   

`/etc/systemd/system`에 직접 만들어줘도 되고, 원하는 위치에 만든 후 `/etc/systemd/system`에 심볼릭 링크를 만들어줘도 됩니다. 아래는 심볼릭 링크로 만드는 방법입니다.  

systemd는 ```/etc/systemd/system```에 있는 파일을 실행합니다. 따라서 ```sudo systemctl start gunicorn.service```를 하기 위해 위헤서 만든 ```gunicorn.service```의 심볼링 링크를 만들어 줍니다.    
   
```
sudo ln -s /home/ubuntu/apps/mydjango/run/gunicorn.service /etc/systemd/system/
```
    
그리고 ```sudo systemctl start gunicorn.service```로 실행해줍니다. ```sudo systemctl status gunicorn.service```를 하면 상태를 볼 수 있습니다. 중지는 ```sudo systemctl stop gunicorn.service```. 서버 재시작 시 자동 실행은 ```sudo systemctl enable gunicorn.service``` 입니다.    
     
여기까지 하면 위에 ```mydjango/run``` 디렉토리 아래에 ```gunicorn.sock``` 파일이 생깁니다. 이 파일을 이용해 아래에서 설정할 nginx의 proxy_pass가 들어오는 동적 파일에 대한 요청을 gunicorn으로 쏴주고 gunicorn에 의해 서비스 되는 Django가 응답하게 됩니다.    

---

## 9. Nginx 설치 및 설정
```sudo apt install nginx```로 설치하고 호스팅 서버 IP로 접속해보면 nginx 사이트가 뜹니다. 이제 nginx 설정으로 앞서 생성된 gunicorn.sock으로 연결하겠습니다.     
     
```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        server_name 도메인이나호스팅서버IP입력;

        location = /favicon.ico { access_log off; log_not_found off; }

        location /static/ {
                root /home/ubuntu/apps/mydjango/staticfiles;
        }

        location / {
                include proxy_params;
                proxy_pass http://unix:/home/ubuntu/apps/mydjango/run/mydjango.sock;
        }
}

```

이렇게 하고 nginx를 ```sudo systemctl restart nginx``` 재시작 해줍니다.

---

## 10. 테스트
짠! 사이트가 떴습니다. 흐름을 다시 한 번 살펴보면 ```사용자 브라우저 -> nginx -> gunicorn(with systemd) -> Django``` 이런 식입니다.    
