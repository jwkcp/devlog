---
layout: post
title: 파이썬에서 openpyxl을 이용해 엑셀 다루기 간단 요약
tags: python openpyxl
comments: true
---

# openpyxl란?

[openpyxl](https://openpyxl.readthedocs.io/en/stable/)은 엑셀을 다루기 위한 파이썬 라이브러리입니다. Office Open XML을 다루는 파이썬 기본 라이브러리의 부족한 점을 보완하기 위해 개발되었습니다.

## 설치

`pip install openpyxl`  

---

# 간단 사용법

## 불러오기
`import openpyxl`  

## 파일 열기
{% highlight python %}
#엑셀 파일 하나가 워크북(workbook)입니다.
workbook = openpyxl.load_workbook(파일경로)

#엑셀 파일에는 여러 시트가 있을 수 있습니다. 아래 코드는 활성 시트를 가져옵니다.
worksheet = workbook.active
{% endhighlight %}

## 파일 복사하기
{% highlight python %}
import shutil

shutil.copy(기존파일명, 신규파일명)
{% endhighlight %}

---

# 셀 다루기

## 헤더 가져오기
`headers = worksheet[1]`  

## 열 추가하기
`worksheet.insert_cols(삽입시작인덱스, 삽입종료인덱스)`

## 셀 접근하기
`cell = worksheet.cell(row=행인덱스, column=열인덱스)`

## 셀 내용 접근하거나 바꾸기
`cell.value` or `cell.value = 새로운값`

## 셀 내용 가운데 정렬
{% highlight python %}
from openpyxl.styles import Alignment

cell.alignment = Alignment(horizontal='center')
{% endhighlight %}

## 셀 색상 바꾸기
{% highlight python %}
from openpyxl.styles import PatternFill, Color

#예를 들어, 연한 오렌지색일 경우
fill_with_orange = PatternFill(patternType='solid', fgColor=Color("fff3bf"))

cell.fill = fill_with_green
{% endhighlight %}

---

더 자세한 내용은 [openpyxl 문서](https://openpyxl.readthedocs.io/en/stable/) 참고
