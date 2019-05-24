---
layout: post
title: 파이썬으로 만든 프로그램을 윈도우용 exe로 만들기
tags: python
comments: true
---

파이썬을 윈도우 실행파일로 만들기 위해서 pyinstaller가 사용된다.    

#### 설치    
```
pip install pyinstaller
```
    
#### 사용
```
pyinstaller mysourcefile.py

# 콘솔 창 안띄우고 하나의 파일로 생성
pyinstaller --noconsole --onefile mysourcefile.py

# 빌드 전에 모든 캐시와 임시파일 제거, 빌드 시 출력할 로그 레벨(기본은 INFO)
pyinstaller --clean --log-level ERROR
```
당연히 위 옵션을 섞어서 사용할 수도 있다. 더 많은 옵션은 [여기](https://pyinstaller.readthedocs.io/en/v3.3.1/usage.html)를 참고.    
     
이렇게 실행하고 에러없이 끝나면 build 디렉토리와 dist 디렉토리가 생긴다. dist 디렉토리에 생성된 exe 파일이 있다.     
혹시 에러가 발생한다면 [여기](https://devlog.jwgo.kr/2019/05/24/pyinstaller-error/) 참고.    
    
---

### 참고
pyinstaller와 관련된 더 자세한 정보는 [여기](https://pyinstaller.readthedocs.io)에서 찾을 수 있다.