---
layout: post
title: pyinstaller 사용 시 WARNING lib not found api-ms-win 과 같은 에러가 발생하는 경우
tags: python
comments: true
---

## 문제

윈도우에서 pyinstaller를 실행하면 `WARNING: lib not found: api-ms-win-crt-runtime...` 과 같은 에러가 주르륵 나온다.  

---  

## 원인

관련 윈도우 10 SDK dll이 없어서 그렇다.  

---  

## 해결방법  

아래 주소에서 Windows 10 SDK를 설치 한 후 해당 경로를 인수에 포함시켜 pyinstaller를 실행한다.  

[https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk)  

설치 후 아래와 같은 경로에 관련 dll이 들어간다. 버전 번호 등은 좀 다를 수 있다.  
`C:\Program Files (x86)\Windows Kits\10\Redist\10.0.18362.0\ucrt\DLLs\x86`  

x64 또는 x86 폴더를 사용하기 쉽게 `C:\windowkit`로 복사했다고 가정하면 아래와 같이 pyinstaller 명령을 사용하면 된다.  

`pyinstaller --onefile -p c:\windowkit myfile.py`  

