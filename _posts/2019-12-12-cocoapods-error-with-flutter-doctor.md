---
layout: post
title: flutter doctor 실행 시 cocoapods 관련 오류가 발생하는 경우 해결 방법
tags: flutter cocoapods
comments: true
---

## 문제

`flutter doctor`를 실행하면 아래와 같이 에러가 발생하고 `sudo gem install cocoapods`를 해도 문제가 해결되지 않는다.  
  
---

## 원인

`brew`로 설치한 것이 맥os가 업데이트 되면서 버전 문제가 생긴 것입니다. 문제 해결을 위해 `flutter doctor`는 gem으로 설치한 방법으로 안내합니다. 하지만 안내대로 `sudo gem install cocoapods`를 하면 `gem`으로 설치되긴했지만 경로가 등록되지 않았고 `brew`로 설치한 버전을 계속 참조하고 있기 때문에 에러가 없어지지 않습니다.  

---

## 해결방법

1. `brew`로 설치한 `cocoapods`를 제거합니다. `brew uninstall cocoapods`  
2. `gem`으로 `cocoapods`를 설치합니다. 설치 시 편의성을 위해 `bin`에 설치합니다. `sudo gem install -n /usr/local/bin cocoapods`  

이제 다시 `flutter doctor`를 실행해보면 에러가 사라진 것을 볼 수 있습니다.  


