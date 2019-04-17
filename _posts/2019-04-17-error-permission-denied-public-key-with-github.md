---
layout: post
title: Git 사용 시 'Error Permission denied (publickey)' 에러가 발생하는 경우
tags: git
comments: true
---
    
## 발생에러
```
Cloning into 'git-repo-uri'...
Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

---

## 예상 원인
```Permission denied```에러가 발생하는 것은 서버가 연결을 거부했다는 뜻입니다. 여러 이유가 있을 수 있습니다. 저의 경우에는 git을 sudo 명령과 함께 사용하려고 해서 위와 같은 에러가 발생했습니다. git clone을 할 때 디렉토리 생성 시 root 권한이 있어야 할 것 같아서 그랬는데 git은 sudo와 함께 쓰면 안된다고 합니다.   
     
### sudo 명령을 Git과 함께 썼을 경우
```sudo```명령을 Git과 함께 쓰지 않도록 하는 것이 좋습니다. 정말 꼭꼭꼭 써야 하는 합당한 이유가 있다면 Git관련 모든 명령을 실행할 때 마다 ```sudo```를 붙여줘야 합니다. (그럴바엔 차라리 ```su```를 이용해 루트쉘을 얻어 쓰는게 낫습니다.) SSH 키를 만들 때 ```sudo``` 명령 없이 만들어놓고 ```sudo git push```처럼 쓸때는 ```sudo```를 붙여쓰면 생성된 SSH 키를 다른 거라고 인식하기 때문에 이런 에러가 발생할 수 있습니다.     
     
### 서버랑 연결이 진짜 안되고 있는 경우
github.com 도메인을 칠 때 githib.com이나 guthub.com처럼 오타가 있을 수 있습니다. 혹시 회사에서 하고 있다면 내부 DNS로 인해 github.com을 제대로 찾지 못하고 있을 수도 있습니다. 이럴 때 아래의 명령으로 연결 테스트를 해볼 수 있습니다. (git@github.com처럼 git으로 해야지, 이 자리에 사용자 이름을 넣는다던지 하면 안됩니다. 예를 들어, superman@github.com은 테스트가 실패합니다.)      
      
```
$ ssh -vT git@github.com
```
    
기본 설정하에서는 22번 포트로 연결 테스트가 실행되며 결과가 나옵니다. 제대로 연결된다면 맨 아래쪽에 Github 사용자 이름과 함께 'You've successfully authenticated....'와 같은 문장이 나타납니다.    
    
### 기타 다른 오류 사항
기타 다른 오류 사항은 [Github.com에서 제공하는 오류 해결 페이지](https://help.github.com/en/articles/error-permission-denied-publickey)를 참고하세요.    
      
---

[함께 보면 좋은 글]
* [SSH Keygen을 이용한 키 생성 방법과 ssh-agent에 대한 간단 설명](https://devlog.jwgo.kr/2019/04/17/ssh-keygen-and-ssh-agent/)

[참고링크]
* [Error: Permission denied (publickey)](https://help.github.com/en/articles/error-permission-denied-publickey)