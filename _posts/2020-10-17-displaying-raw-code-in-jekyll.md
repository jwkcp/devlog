---
layout: post
title: 깃허브 지킬(Jekyll)에서 장고 템플릿 태그 그대로 표시하는 방법
tags: jekyll github django templatetag
comments: true
---

## 문제
깃허브에 지킬(Jekyll)을 깔고 코드를 표시할 때 `highlight` 구문을 씁니다. [이렇게](https://jekyllrb.com/docs/liquid/tags/) 말이죠. 그런데 장고의 템플릿 태그등을 표시하려면 지킬에서 사용하는 문법과 겹쳐서 제대로 표시되지 않습니다. 어떻게 해야 할까요?  

## 해결방법
`highlight`구문 안에 `raw` - `endraw` 구문을 넣는 겁니다. 관련 내용은 [여기](https://jekyllrb.com/docs/liquid/tags/)에서 찾을 수 있습니다. 어떻게 표시되는지 궁금하다면 [이 페이지](https://devlog.jwgo.kr/2020/10/17/fancy-messaging-system-in-django/)의 코드 표시를 확인해보세요.  

아래는 본문에서 발췌한 것입니다.  

```
Jekyll processes all Liquid filters in code blocks
If you are using a language that contains curly braces, you will likely need to place {% raw %} and {% endraw %} tags around your code. Since Jekyll 4.0 , you can add render_with_liquid: false in your front matter to disable Liquid entirely for a particular document.
```