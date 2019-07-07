---
layout: post
title: 우분투(Ubuntu)에 pyenv 설치하기
tags: pyenv ubuntu
comments: true
---

`pyenv`는 한 머신에 여러 버전의 파이썬을 설치, 필요에 따라 수시로 바꿔가며 사용할 수 있게 해준다. 파이썬 패키지 환경을 가상화하는 `virtualenv`와 햇갈리지 말자. 간단히 말해 `pyenv`는 시스템에 종속되지 않는 파이썬을, `virtualenv`는 파이썬 패키지 설치를 가능하게 해준다.

## pyenv 설치

1. pyenv를 설치하고 싶은 위치에 깃허브 저장소를 카피한다. 일반적으로 `~/.pyenv`에 복사한다.  
   `$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv`

2. pyenv 환경설정을 해준다. 앞으로도 계속 사용하기 위해 아래와 같이 해준다.

```
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
```

3. 쉘 재시작
   `exec "$SHELL"`

---

## pyenv 사용

-   원하는 버전 설치: `pyenv install 3.6.8`
-   현재 설치된 버전 보기: `pyenv versions`
-   설치된 버전 중 원하는 버전 선택하기: `pyenv shell 3.6.8`

---

## 계속 특정 버전 사용하기

쉘을 재시작해보면 pyenv의 파이썬 버전이 기본값으로 돌아가있을 것이다. 설치한 버전 중 원하는 버전으로 계속 실행되게 하고 싶으면 `.bashrc`에 `PYENV_VERSION=3.6.8`과 같이 `pyenv`로 설치한 버전 중 원하는 버전을 넣어주면 된다.
