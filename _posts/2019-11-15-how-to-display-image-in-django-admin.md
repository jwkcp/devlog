---
layout: post
title: 장고(Django) 어드민에서 이미지 썸네일 표시하는 방법
tags: django
comments: true
---

## 목표

장고 어드민에서 이미지를 파일명이 아니라 썸네일로 표시하고 싶은데 어떻게 해야하나요?  

---

## 방법

예를 들어 아래와 같은 기본 어드민 코드가 있다고 가정하겠습니다.  

{% highlight python %}
from django.contrib import admin
from .models import Book


class BookAdmin(admin.ModelAdmin):
    list_display = ['title', ]

admin.site.register(Book, BookAdmin)
{% endhighlight %}

이 상태에서 어드민에 책표지의 파일명이 아니라 파일 썸네일을 보여주고 싶다면 아래와 같이 해줍니다. 함수를 만들고 그 함수명을 list_display에 추가하는 것이지요. 함수명은 아무것이나 원하는 것으로 하면 됩니다. 과거에는 `book_cover_thumbnail.allow_tags = True`와 같이 `allow_tags`를 썼으나 이제는 `mark_safe`를 사용하는 것으로 바뀌었습니다.  

`book_cover_thumbnail.short_description = "Thumbnail"` 구문 역시 선택사항인데요. 어드민에 보여질 리스트에 헤더에 뭐라고 표시할지 정해주는 것입니다.  

{% highlight python %}
from django.contrib import admin
from django.utils.safestring import mark_safe
from .models import Book


class BookAdmin(admin.ModelAdmin):
    def book_cover_thumbnail(self, obj):
        return mark_safe('<img src="{}"/>'.format(obj.book_cover.url))
    
    book_cover_thumbnail.short_description = "Thumbnail"

    list_display = ['title', 'book_cover_thumbnail', ]

admin.site.register(Book, BookAdmin)
{% endhighlight %}
