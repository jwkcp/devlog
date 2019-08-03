---
layout: post
title: 파이참(Pycharm)에서 'Please specify a different sdk name' 에러가 발생하는 경우
tags: pyenv pycharm
comments: true
---

## 증상

파이참(Pycharm)에서 가상환경을 생성하거나 기존의 가상환경을 불러서 쓰려고 하면 아래와 같은 에러 메시지가 나오면서 인터프리터 지정이 안되는 경우가 있다.

`Cannot Save Settings. Please specify a different sdk name`

## 과정

다양한 파이썬 버전을 유연하게 사용하려면 pyenv는 매우 훌륭한 선택이다. 머신에 파이썬 2.7, 2.8, 3.5.2, 3.7 등 여러 버전을 설치하다보면 경로도 그렇고 여러 환경 변수들이 꼬이면서 예상치 못한 문제를 만들어 내기 때문이다. 그래서 기본으로는 3.6버전이 머신에 설치되어 있지만 3.5.2를 반드시 써야 하는 경우 `pyenv install 3.5.2`와 같이 해준 후 `pyenv shell 3.5.2` 명령을 하면 바로 3.5.2 버전의 파이썬이 사용 가능하게 된다. 그 후 `python -m venv .venv`와 같이 명령을 실행하면 파이썬3에 탑재된 가상환경 모듈 venv가 3.5.2 환경으로 .venv 디렉토리를 생성해주게 된다. 여기까지는 환상적이다.

_pyenv에 대한 글 더 보기_

-   [pyenv로 파이썬 설치 시 발생하는 오류와 해결 방법 (이거 하나면 끝!)](https://devlog.jwgo.kr/2019/06/05/must-installed-lib-when-installing-python-using-pyenv/)
-   [pyenv로 파이썬 설치 시 no acceptable C compiler found in \$PATH 에러가 발생하는 경우](https://devlog.jwgo.kr/2019/06/05/no-acceptable-c-compiler-found-in-path-error-with-pyenv/)
-   [장고(Django) 서비스를 위한 AWS lightsail 우분투 환경 설정하기](https://devlog.jwgo.kr/2019/07/07/setting-ubuntu-up-for-django-service/)

---

## 문제

이렇게 생성한 가상환경을 파이참의 `Preferences > Project > Project Interpreter`에서 `add`로 지정해주려고 하면 위에서 언급한 에러 메시지가 나타나며 인터프리터 지정이 해제되고 더 이상 진행되지 않는다.

## 해결방법

일단 이 문제는 파이참의 버그로 보인다. 아래의 경로에서 `jdk.table.xml` 파일을 삭제 후 파이참을 재시작하면 정상적으로 가상환경을 지정할 수 있다.

`jdk.table.xml`이 있는 경로: `~/Library/Preferences/PyCharm2019.2/options`

경로 중 `PyCharm2019.2`부분은 자신이 쓰고 있는 파이참 버전 명을 넣어준다. 이를 명령어로 표현하면 아래와 같다.

`rm -rf ~/Library/Preferences/PyCharm2019.2/options/jdk.table.xml`

끝 :)
