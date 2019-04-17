---
layout: post
title: 우분투 리눅스에서 yarn 설치 후 let ... 에러가 발생하는 경우
tags: yarn
comments: true
---
    
yarn 설치 후 ```yarn install``` 같은 명령을 쓰려고 하면 아래와 같은 에러가 발생할 때가 있습니다. 이건 node 버전이 오래됐거나 설치된 노드에 문제가 있어서 발생했을 확률이 큰데요. 최선 버전의 노드를 설치해주고 다시 실행해보면 문제가 해결될 것입니다. 최선 버전 노드 설치는 [여기]({% post_url 2019-04-17-how-to-install-nodejs-on-ubuntu %})를 참고하세요.   
      
```
/usr/share/yarn/lib/cli.js:45726
let {
^

SyntaxError: Unexpected token {
at exports.runInThisContext (vm.js:53:16)
at Module._compile (module.js:374:25)
```