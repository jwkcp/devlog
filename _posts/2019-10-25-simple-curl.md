---
layout: post
title: curl 헷갈리면 이것만 기억하자.
tags: linux
comments: true
---

리눅스 명령에 익숙하지 않은 분들은 curl 명령이 좀 헷갈리실 겁니다. 일단 아래 사용법만 기억해두세요. 더 많은 옵션과 활용법은 필요에 따라 자동으로 익숙해질테니 너무 걱정 마시고요.

`curl -OL# 다운로드주소`

curl에 아무 옵션 없이 그냥 다운로드주소를 넣고 엔터를 치면 기본적으로 다운로드한 파일을 콘솔에 쿨력하게 됩니다. 간단히 생각해봐도 화면이 알아볼 수 없는 문자로 가득차면서 엉망친창이 되겠죠. 그래서 curl은 옵션없이 다운로드주소만 넣고 실행하면 사용자에게 "콘솔이 알 수 없는 문자로 가득 찰텐데 괜찮겠어?" 하고 물어봅니다. 그리고 그렇게 하고 싶으면 명시적으로 --output 옵션을 넣으라고 합니다. 우리가 원하는 것은 그냥 단순히 다운로드주소의 파일을 다운로드 받고 싶은 것이므로 이렇게 하면 안되겠죠?

위의 옵션을 이용하면 다운로드주소의 파일을 서버에 저장된 **원래 파일이름**으로 **현재 위치**에 HTTP응답코드만 포함해 **진행상태**를 보여주며 다운로드합니다.

**-O 옵션**: 서버에 저장된 파일명 그대로 다운로드  
**-L 옵션**: HTTP 헤더나 콘텐츠 빼고 깔끔하게 응답코드만 출력  
**-# 옵션**: 다운로드 진행상태 표시

쉽지요? ^^