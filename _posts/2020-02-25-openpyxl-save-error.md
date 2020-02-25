---
layout: post
title: 파이썬에서 openpyxl을 이용해 엑셀 save 시 에러나는 이유와 해결방법
tags: python openpyxl
comments: true
---

## 문제

`openpyxl`의 `save` 호출 시 `{0} is not a valid column name`과 같은 에러와 함께 저장이 안되는 경우가 발생  

## 원인 

`save`를 호출하면 파일이 닫히기 때문에, 변수로 `load_workbook`로 받은 파일의 레퍼런스를 가지고 있더라도 `save`를 다시 호출하면 에러가 발생한다.  

## 해결방법  

이미 `save`를 호출했는데 workbook 레퍼런스 변수를 통해 다시 `save`를 호출하지 않도록 한다. `save`를 마지막에 한번만 해주거나, 꼭 두 번 `save`를 호출해줘야 한다면 `load_workbook`으로 파일을 열고 레퍼런스를 다시 얻도록 한다.  
