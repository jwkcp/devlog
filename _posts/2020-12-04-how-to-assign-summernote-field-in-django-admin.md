---
layout: post
title: 장고(Django)에서 어드민 페이지에 썸머노트(summernote) 필드 적용하는 방법
tags: django summernote
comments: true
---

# 문제

[썸머노트 공식 페이지에 보면 어드민 페이지에 적용하는 법](https://github.com/summernote/django-summernote#apply-summernote-only-to-specific-textfield-in-model)이 나와있습니다. 하지만 이 방법은 `SummernoteModelAdmin`를 상속받는 방법인데, 만약 모델 어드민을 이미 상속받고 있다면 어떻게 해야 할까요?  

---

# 방법

`SummernoteModelAdminMixin`를 믹스인을 이용하면 됩니다.  

{% highlight django+python %}
from django_summernote.admin import SummernoteModelAdminMixin

from .models import Student


@admin.register(Student)
class StudentAdmin(SummernoteModelAdminMixin, admin.ModelAdmin):
    list_display = ['name', 'age', 'bio', ]

    summernote_fields = ('bio', )
{% endhighlight %}
