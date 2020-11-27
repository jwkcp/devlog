---
layout: post
title: An invalid form control with name='content' is not focusable 에러가 발생하는 경우 해결 방법
tags: django html web
comments: true
---

# 문제

`An invalid form control with name='content' is not focusable` 이런 에러가 발생한다면 도대체 무엇이 문제이고 어떻게 해결해야 하나요?  

---

# 원인 
폼 필드 내에 `required`로 되어 있는 필드를 그냥 전송하려고 했고, 브라우저가(특히 크롬)이 에러를 표시하려고 했으나 `d-none`이나 `display:none` 등의 설정으로 해당 폼 필드가 가려져 있어 표시할 수 없다는 경고입니다.  

---

# 해결방법
일부 stackoverflow 등의 답변을 보면 `novalidate` 속성을 폼에 넣어서 이 에러를 피할 수 있다고 하는 답변이 있으나 이렇게 하면 안됩니다. 폼 유효성 검사는 최소한의 검증 절차이므로 이런 꼼수를 써서 적절히 발생하는 경고를 무시하면 나쁜 사용자 경험을 가지는 웹페이지가 됩니다. 뿐만 아니라 서버에서 폼 검증 시 문제가 생기는 등 상황을 더 악화시키게 됩니다.  

이런 경고가 발생하면 이렇게 **필수 필드인데도 가려져있는** 필드가 뭐가 있는지 살펴보셔야 합니다. 특히 `include` 구문을 통해 코드를 삽입해서 사용하고 있다면 이런 코드 조각에 `required` 속성을 포함하는 폼 필드가 들어있을 가능성이 상당히 높습니다. 이 부분을 유념해서 살펴보세요.  
