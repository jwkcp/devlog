---
layout: post
title: 비주얼 스튜디오 코드(VSCode)에서 설정 동기화 사용 방법
tags: vscode tools
comments: true
---

비주얼 스튜디오 코드(이하 VSCode)를 여러 대의 기기에서 쓰다보면 확장 프로그램이나 설정 동기화가 필요해집니다. 이 때 필요한 플러그인이 `Shan Khan`이 제작한 `Settings Sync`라는 확장 프로그램입니다. 프로그램은 일반 확장 프로그램 설치와 동일한데 VSCode가 로그인 뭐 이런 것이 없다보니 동기화 시킬 때 깃허브의 Gist를 활용합니다. Gist는 쉽게 말해 공개, 비공개로 코드 조각을 저장해 놓을 수 있는 공간입니다. (한 10년 전만 해도 메모장에 자주 쓰는 보일러 플레이트 코드를 저장해서 쓰기도 했었지요.)

따라서, VSCode 동기화를 하려면 아래 사전 조건이 갖춰져 있어야 합니다.

- Github 계정
- Settings Sync 설치

---

## 확장 프로그램 설치(1/3)

VSCode에서 `Settings Sync`라는 프로그램을 검색해서 설치합니다.  

---

## 깃허브에서 토큰 발급(2/3)

깃허브 로그인 후 오른쪽 상단의 프로필 사진을 누르면 `Settings` 메뉴가 보입니다. 이걸 누르고 아래 메뉴대로 찾아갑니다.  

`Settings > Developer settings > Personal access tokens`  

그리고 `Generate new token`을 눌러 토큰을 발급 받습니다.  

`Note` 항목에는 `code-settings-sync`를 넣어주고 아래 체크 박스 선태에서는 `gist`를 선택해줍니다.  

이 때 나오는 토큰 문자열을 따로 저장해둡니다. 나중에 VSCode 동기화 설정 시 필요합니다. 나중에 토큰 발급 페이지로 와서 찾으려고 해도 보안 상 보이지 않도록 되어 있기 때문에 복사해서 따로 보관해야 합니다.  

이쯤에서 토큰이 뭔지, 왜 토큰을 발급 받아야 하는지 의문이 드는 분들이 계실텐데 간략히 설명을 드리면 아이디, 비밀번호 대신 인증을 해주는 역할을 합니다. 예를 들어, 이런 동기화 확장 프로그램의 기능을 쓰자고 깃허브의 아이디, 패스워드를 넣으라고 나오면 아마 무지 찝찝할겁니다. 그리고 대부분 쓰지 않겠지요. 이런 상황에서 토큰을 사용하면 아이디, 패스워드 입력없이 내가 스스로 발급한 토큰 문자열만 넣어주면 인증이 되고 제한된 권한이 할당됩니다. 권한은 위에서 체크박스로 이미 선택했었지요? 혹여 문제가 생기면 발급한 토큰을 삭제해버리면 끝입니다.  

---

## `Settings Sync` 설정(3/3)

이제 VSCode의 설정을 업로드할 차례입니다. 단축키 `Shift + Alt + u`를 누르면 `Enter Github Personal Access Token`이라는 입력창이 나옵니다. 여기에 아까 발급해 복사한 토큰 문자열을 붙여넣어줍니다. 그리고 바로 아레 메시지로 나오는 `GIST ID` 문자열을 복사해둡니다. 나중에 다른 기기에 설치된 VSCode에서 설정을 다운로드 할 때 이 문자열이 사용됩니다.  

다운로드는 `Shift + Alt + d`를 누릅니다. 그러면 `Enter Github Personal Access Token` 입력 창이 나오고 복사해둔 토큰 문자열을 입력합니다. 그러면 `GIST ID`를 입력하라고 나옵니다. 이건 위에 업로드 설정 후 나왔던 그 문자열입니다. 이 값도 넣어줍니다. 그러면 내 깃허브의 Gist에서 설정을 가져와 VSocde에 설정해줍니다.  

이렇게 하면 기본적인 설정이 완료된 것입니다.  

전체적으로 정리해보면,  

VSCode에 설치된 `Settings Sync` 확장 프로그램이 내 깃허브의 Gist에 설정을 JSON으로 저장해두고 필요할 때 불러오고 업데이트하면서 기기 간 VSCode의 설정을 동기화하는 방식입니다. 실제로 [https://gist.github.com](https://gist.github.com)에 들어가서 `cloudSettings` 항목을 눌러보면 어떻게 설정이 저장되어 있는지 볼 수 있습니다.  

---

## 추가 팁

### 업로드 설정 후 나오는 GIST ID를 복사하지 못했어요

그때 GIST ID를 복사해두지 못했더라도 걱정하지 마세요. [https://gist.github.com](https://gist.github.com)에 들어가 `cloudSettings`를 누른 후 나오는 URL의 마지막 값이 `GIST ID`입니다. 이 값을 이용하세요.  

> `https://gist.github.com/{your_userName}/{gist_id}`

### 자동으로 설정을 동기화하고 싶어요

`Settings Sync`는 자동 동기화 기능도 제공하고 있습니다. VSCode command palatte를 이용해도 되지만 저는 설정을 이용하는 편입니다. VSCode의 설정에 들어가서 보면 왼쪽 트리 메뉴에 `확장 > 코드 설정 동기화 설정` 메뉴가 있습니다. 여기에 `Auto Upload`와 `Auto Download`에 체크해주세요. 그러면 새로운 확장 프로그램을 설치할 때 마다 `Settings Sync`가 내 Gist에 설정을 자동으로 업로드함으로서 동기화를 해줍니다.  

---

- 참고링크: [http://shanalikhan.github.io/2015/12/15/Visual-Studio-Code-Sync-Settings.html](http://shanalikhan.github.io/2015/12/15/Visual-Studio-Code-Sync-Settings.html)
