---
layout: post
title: 폼셋(formset) 업데이트 시 '필드를 확인하세요 id' 에러가 발생하는 이유와 해결방법
tags: django
comments: true
---

## 문제
폼셋(formset)의 값을 저장하려고 하면 아래와 같은 에러가 발생하는데 왜 그런 것이며 어떻게 해결해야 할까요?  

---

## 원인
폼셋을 템플릿에 랜더링할 때 아래와 같이 `management_form` 구문을 사용하고 각 개별 필드를 렌더링해 값을 업데이트하려고 하면 발생합니다.  
   
{% highlight django+python %}{% raw %}
{{ myformset.management_form }}
{% endraw %}{% endhighlight %}

**이런 식의 에러가 발생합니다.**  
- 필드를 확인하세요 id  
- [{'id': ['This field is required.']}, {'id': ['This field is required.']}]  

---

## 해결방법
폼셋을 통채로 렌더링하거나, `management_form`을 사용하고 개별 필드를 렌더링하려면 아래와 같이 폼 ID를 전송할 수 있도록 해당 필드를 함께 렌더링해주면 됩니다.   
  
{% highlight django+python %}{% raw %}
{% for form in myformset %}
{{ form.id }}
{% endfor %}
{% endraw %}{% endhighlight %}
