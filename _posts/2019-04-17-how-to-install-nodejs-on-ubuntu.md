---
layout: post
title: 우분투 리눅스에 최신 node.js 설치하는 방법
tags: node
comments: true
---

아래는 노드 버전 관리자인 nvm을 이용합니다. nvm은 노드 개발자들이 널리 사용하고 있는 툴입니다. nvm 설치는 매우 쉽고 간단합니다. [여기]({% post_url 2019-04-17-how-to-install-and-use-nvm %})를 눌러 nvm에 대해 살펴보세요.    
    
#### 최신 노드 설치하기
```
# 여기서 마지막 인자 node는 최신 버전의 별칭입니다.
$ nvm install node
```
    
#### 설치된 노드 & 설치할 수 있는 노드 버전 보기
```
# 설치된 노드 목록
$ nvm ls

# 설치할 수 있는 노드 목록
$ nvm ls-remote
```
    
#### 설치된 노드 선택하기
마지막 node 부분에 설치된 노드 중 원하는 버전 이름을 쓰면 됨.
```
$ nvm use node
```
    
#### 다른 터미널을 열어도 항상 같은 버전의 노드 사용하기
마지막 node 부분에 설치된 노드 중 원하는 버전 이름을 쓰면 됨.
```
$ nvm alias default node
```
    
#### nvm 비활성화하기
```
$ nvm deactivate
```