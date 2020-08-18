---
layout: post
title: Django(장고) 폼 템플릿에 readonly 같은 속성 추가하는 법
tags: python django
comments: true
---

장고에서 폼을 이용해 부트스트랩 등 CSS 작업을 하려면 만든 forms.py에서 아래와 같이 작업을 해줘야 합니다. (외부 패키지를 이용하는 등 다른 방법도 있지만 이 방법을 가장 많이 쓰게 되네요. 의존성을 하나라도 줄여야죠 ^^)  

{% highlight python %}
self.fields['name'].widget.attrs.update({
    'class': 'form-control',
    'placeholder': "이름",
})
{% endhighlight %}

`form-control`같은 것은 `class`에 속하면 되고, `placeholder`는 적절한 값을 키-값 쌍으로 넣어주면 됩니다.   

---

그럼 `readonly`같이 단독으로 입력해줘야 하는 속성은 어떻게 해야 할까요? 아래와 같이 하면 됩니다.  

{% highlight python %}
self.fields['name'].widget.attrs.update({
    'class': 'form-control',
    'placeholder': "이름",
    'readonly': 'True',
})
{% endhighlight %}