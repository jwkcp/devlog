---
layout: post
title: 맥(Mac)에서 pyenv로 파이썬 설치 시 zlib 관련 에러가 발생할 때 해결방법
tags: pyenv how-to
comments: true
---

## 증상

맥에서 pyenv를 이용해 파이썬 설치 중 `zipimport.ZipImportError: can't decompress data; zlib not available`와 같은 에러가 발생할 때   

---

## 해결방법

터미널에서 `sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target /` 실행  
