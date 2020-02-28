---
layout: post
title: 파이썬에서 xmltodict와 같은 서드파티 라이브러리 디버그 로그 끄는 법
tags: python xmltodict
comments: true
---

## 문제

설치한 라이브러리에서 디버그 로그를 너무 많이 쏟아 내고, 내가 작성한 로그와 섞여서 불편하다. 예를 들어, `xmltodict`라는 라이브러리를 사용하면 아래와 같이 로그가 계속 쏟아진다.  

```
[DEBUG] 2018-01-25 03:15:36,974 EUC-JP Japanese prober hit error at byte 1765
[DEBUG] 2018-01-25 03:15:36,990 EUC-KR Korean prober hit error at byte 1765
[DEBUG] 2018-01-25 03:15:36,994 CP949 Korean prober hit error at byte 1765
[DEBUG] 2018-01-25 03:15:37,009 EUC-TW Taiwan prober hit error at byte 1765
[DEBUG] 2018-01-25 03:15:37,036 utf-8 not active
[DEBUG] 2018-01-25 03:15:37,036 SHIFT_JIS Japanese confidence = 0.01
[DEBUG] 2018-01-25 03:15:37,036 EUC-JP not active
[DEBUG] 2018-01-25 03:15:37,036 GB2312 Chinese confidence = 0.01
[DEBUG] 2018-01-25 03:15:37,036 EUC-KR not active
[DEBUG] 2018-01-25 03:15:37,036 CP949 not active
```

## 해결방법  

어떤 로거가 저 로그를 찍는지 살펴본다. 위 `xmltodict`의 경우 아래와 같이 로그가 찍혔다.  

`chardet.charsetprober get_confidence DEBUG`  

로그 이름을 알았으니 아래와 같이 로그 레벨을 변경해준다.  

{% highlight python %}
xmltodict_logger = logging.getLogger('chardet.charsetprober')
xmltodict_logger.setLevel(logging.INFO)
{% endhighlight %}

이렇게 하면 내 로그는 디버그 수준으로 찍으면서, 쓸데없는 디버그 로그를 보지 않아도 된다.  

