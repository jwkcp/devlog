---
layout: post
title: nvm이란 무엇인가요? 어떻게 설치하고 사용하나요?
tags: node nvm
comments: true
---
    
## 왜 nvm을 사용하나 
#### 1. 여러 버전의 node.js를 쉽게 설치하고 사용할 수 있으니까
nvm은 Node Version Manager의 약자입니다. 여러 노드(Node) 버전을 사용해야 할 때 매우 유용합니다. 파이썬 가상환경을 접해 본 분이라면 그 개념을 바로 이해할 수 있으실 거에요. [여기](https://github.com/creationix/nvm)를 방문하면 nvm의 깃허브 저장소를 살펴볼 수 있습니다. nvm 깃허브 저장소에서는 nvm을 여러 버전의 활성 node.js 버전을 관리할 수 있는 심플한 배시 스크립트라고 되어 있네요.     
#### 2. 최신 node.js를 사용할 수 있으니까
리눅스 운영체제의 패키지 매니저를 통해 nodejs를 설치하면 최신 버전이 아니라 아주 오래된 버전이 설치되는 경우가 종종 있습니다. nodejs도 그렇습니다. nvm을 이용하면 매우 쉽고 간편하고 빠르고 깔끔하고 짱짱 편리하게 설치할 수 있습니다. 여러 node.js 버전 간 전환이 쉽다는 건 이미 알고 계시죠?   
    
---

## 어떻게 설치하나?
curl이나 wget으로 내려받아 설치합니다.
```
# curl을 이용할 경우
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash

# wget을 이용할 경우
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
```
      
잘 설치되었나 ```command -v nvm```을 터미널에 입력하여 체크! nvm이란 글자만 나오면 설치가 성공적으로 되었다는 말입니다.

