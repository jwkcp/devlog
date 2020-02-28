---
layout: post
title: pip 사용 시 pip is configured with locations that require TLS/SSL 와 같은 에러가 발생하는 경우 해결 방법
tags: python
comments: true
---

## 문제

`pip install --upgrade pip`나 `pip install requests`와 같이 pip를 이용해 라이브러리를 설치하려고 하면 아래와 같은 에러가 발생한다.  

```
WARNING: pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.",)': /simple/pip/
```

## 원인

아마 직전에 brew나 pip 등을 이용해 뭔가를 삭제하고 재설치한 적이 있을 것이다. 이 과정에서 패키지 의존성이 깨진 것으로 보인다.  

## 해결방법  

`pyenv`를 사용하고 있다면 설치된 버전을 다시 설치 해준다. 예를 들어, `pyenv versions`했는데 별표된 파이썬 버전이 `3.6.2`로 되어 있다면 `pyenv install 3.6.2`로 다시 설치해준다. 그러면 대부분 문제가 해결된다. 그래도 동일하다면 openssl 설치도 잘 되어 있는지 체크해준다.  

`pyenv`를 사용하고 있지 않다면 그냥 파이썬을 재설치해보자.  

