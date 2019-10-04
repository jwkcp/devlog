---
layout: post
title: 깃(Git) 비밀번호 안물어보게 하기
tags: git
comments: true
---

깃(Git)에서 비밀번호를 안물어보게, 그러니까 기억하도록 하는 방법은 캐시(cache)를 사용하면 됩니다.

1. 캐시 활성화 `git config --global credential.helper cache`
2. 캐시 시간 설정 `git config --global credential.helper 'cache --timeout=2592000'` (30일 동안 묻지 않습니다. 하루가 86400초이니 자신에게 맞게 설정하면 됩니다.)

\_\_

근데 뭔가 복잡하지요. 외우지 말고 순서를 기억하면 됩니다. `git 명령어 옵션` 이런 순서로 보면 아래와 같이 크게 세 덩어리로 나눌 수 있습니다.

`git / config / --global credential.helper cache`

앞의 `git / config`는 늘상 쓰던 것이고, 뒤에 옵션 부분만 보면 되는데 `credential.helper`가 핵심입니다. 뭔가 철자도 복잡하고 길어서 거부감이 들지요? 그런데 `credential.helper` 만 기억해두면 나머지는 저절로 기억이 됩니다. 앞의 `--global` 옵션은 모든 계정에 대해 캐싱한다는 뜻이고 `config`에 대한 **옵션**입니다. 뭘 시킬지 정해주는 것이죠. `credential.helper` 역시 마찬가지 입니다. 뒤에 `cache`가 나오는 것은 `credential.helper`의 명령어 역할을 합니다. 시간을 설정할 때 `cache`에다 `--timeout` 옵션을 주고 있네요. 마치 `--global`처럼요.

맞습니다. **프로그램** **명령** **옵션** 의 형태가 반복되고 있습니다. 이건 터미널 명령의 기본 폼이죠.

git config --global  
credential.helper cache --timeout

\_\_

명령줄을 섞어서 이야기를 만들어보면,

git(깃에) config(설정을 할껀데) --global(전역으로 할래요) credential.helper(인증에 관한 설정인데) cache(그건바로 캐시에요) --timeout(시간은) 2592000(30일로 해주세요.)

어떤가요? 좀 기억하는데 도움이 되었나요?
