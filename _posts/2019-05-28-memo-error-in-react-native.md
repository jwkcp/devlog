---
layout: post
title: 리액트 네이티브에서 react["default"].memo ... 에러가 발생할 때
tags: react-native
comments: true
---

## 증상
리액트 네이티브에 리덕스를 붙여 쓰려다보면 아래와 같은 에러가 발생할 때가 있다.    
     
```
react["default"].memo is not a function react-native redux ...
```
    
---
    
## 원인
react-redux의 최신 버전이 문제를 일으킨다.    
  
---
       
## 해결방법
react-redux가 이 문제를 수정하여 재배포할 때까지 6.0.1로 사용한다.   
  
이렇게 수정하세요.         
1. package.json에서 react-redux 항목을 찾아 6.0.1로 바꿔준 후     
2. 변경된 버전 설치를 위해 ```yarn install``` 혹은 ```npm install```을 통해 재설치한다.     
3. expo 사용자는 ```expo r -c``` 명령으로 캐시를 제거해주고 재실행하면 좋다.   
     
처음부터 6.0.1 버전으로 설치하려면 아래 명령을 이용하세요.    
```
yarn add react-redux@6.0.1
```
```     
