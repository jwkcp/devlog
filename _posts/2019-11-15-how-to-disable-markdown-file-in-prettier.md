---
layout: post
title: Prettier 사용 시 마크다운은 적용하지 않도록 예외처리 하는 방법
tags: vscode tools
comments: true
---

## 문제

`vscode`에서 `Prettier`를 적용해두면 마크다운 작성 시 공백 문자를 자동으로 제거해 소스코드 입력 시 들여쓰기가 이쁘게 되지 않습니다.  

---

## 해결방법

마크다운 파일(.md)이 있는 디렉토리에 `.prettierignore`파일을 생성하고 `*.md`를 입력해주면 됩니다. 이제 `prettier`가 마크다운 파일 편집 시 마음대로 공백을 제거하지 않습니다.  
