---
layout: post
title: 파이썬에서 SQLITE 사용 시 fetchall 튜플말고 딕셔너리처럼 받기
tags: python sqlite
comments: true
---

## 문제

파이썬에서 Sqlite 쓸 때 `fetchall`을 하면 결과로 튜플(tuple)이 리턴된다. 딕셔너리처럼 key-value로 받아서 데이터에 접근하려면 어떻게 해야 할까.

## 방법

커넥션(connection) 생성할 때 아래와 같이 `row_factory`를 이용한다. 이건 별도의 꼼수가 아니라 sqlite에 기본으로 포함된 것이니 안심(?)하고 쓰면 된다.

{% highlight python %}

conn = sqlite3.connect(db_path)

#이부분 추가
conn.row_factory = sqlite3.Row
{% endhighlight %}

이후 아래와 같이 데이터에 접근할 수 있다.

{% highlight python %}
result = cur.fetchall()
result['id']
{% endhighlight %}
