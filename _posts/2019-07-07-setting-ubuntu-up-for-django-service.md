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

```
$ sudo apt install build-essential libffi-dev libbz2-dev zlib1g-dev libreadline-dev libsqlite3-dev

$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv

$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc

$ exec "$SHELL"

$ pyenv install 3.6.8

$ pyenv shell 3.6.8

$ echo 'export PYENV_VERSION=3.6.8` >> ~/.bashrc
```

---

## rabbitmq 설치하기 (선택사항)

1. 설치
   `sudo apt install rabbitmq-server`

2. 서비스 확인
   `sudo systemctl status rabbitmq-server`

---

## redis 설치하기 (선택사항)

1. 설치
   `sudo apt install redis-server`

2. 서비스 확인
   `sudo systemctl status redis-server`  
   `redis-cli ping`하면 `PONG`나오면 성공

---

## git에서 소스 복사

`ssh-keygen -t rsa`로 서버에서 키를 발급받고 깃허브 저장소 설정 > 배포키에 추가해주면 된다. 자세한 내용은 [git 프라이빗 저장소 원격지 서버에 클론하는 방법
](https://devlog.jwgo.kr/2019/07/07/how-to-clone-private-repo-into-remote-server/) 참고.

---

## 가상환경 설치 후 활성화

`python -m venv .venv` 후 `source .venv/bin/activate`

---

## 의존성 설치

`python install requirements.txt`

---

... 작업중 ...
