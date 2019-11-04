---
layout: post
title: 이미지를 base64로, base64를 이미지로 변환하는 방법
tags: python
comments: true
---

## 이미지를 base64 문자열로 변환

{% highlight python %}
import base64

with open('./image.png', 'rb') as img:
base64_string = base64.b64encode(img.read())
{% endhighlight %}

---

## base64 문자열을 이미지로 변환

{% highlight python %}
import base64
from PIL import Image
from io image BytesIO
import matplotlib.pyplot as plt

img = Image.open(BytesIO(base64.b64decode(base64_string)))
plt.imshow(img)
{% endhighlight %}
