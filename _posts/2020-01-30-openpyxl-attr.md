---
layout: post
title: openpyxl 사용 시 셀 속성
tags: python openpyxl
comments: true
---
  
파이썬에서 엑셀을 다룰 때 openpyxl를 많이 사용하시는데요. 워크시트에서 불러온 셀의 속성에 뭐가 있는지 알 수 있도록 간단히 캡춰한 이미지 하나 공유합니다.  

이렇게 시트를 열고  

```
wb = openpyxl.load_workbook(filepath)
sw = wb.active

headers = ws[1]
```

첫 번째 행을 가져오면 아래와 같은 속성에 접근할 수 있습니다. 아래 이미지는 엑셀의 제일 첫 번째 셀, 그러니까 A1이 되겠네요. 셀의 값은 `value`로 접근하시면 됩니다.  

<iframe src="https://drive.google.com/file/d/12ptr84YvEKScYHeEAYgvrYF1qrt4PwDq/preview" width="640" height="480"></iframe>