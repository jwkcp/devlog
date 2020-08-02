---
layout: post
title: Django admin에서 함수로 만들 컬럼명 설정 방법
tags: django
comments: true
---

Django(장고)에서 모델을 어드민에 표시할 때 `list_display`를 사용합니다. 그런데 모델에 포함된 컬럼이 아닌데 어드민 리스트 컬럼에 표시하고 싶을 때가 있습니다. 예를 들어, `many-to-many` 필드의 경우 그대로 어드민 리스트에 표시할 수 없습니다. 총 수량을 세서 숫자를 표시하고 싶을 때도 그렇고요. 이럴 땐 별도의 함수를 만든 후 그 함수의 이름을 `list_display`에 넣어주면 됩니다. 아래 간단한 예제를 보겠습니다.  

{% highlight python %}
from django.contrib import admin
from myapp.models import Person

@admin.register(Person)
class PersonAdmin(admin.ModelAdmin):
    list_display = ('name', 'age', 'get_like_food', )

    def get_like_food(self, person):
        # 여기서 favorite_food가 many-to-many 필드라고 가정합니다.
        return person.favorite_food.all().count()

    get_like_food.short_description = '좋아하는 음식'
{% endhighlight %}

위와 같이 `many-to-many` 필드를 어드민 리스트에 표시할 수 있습니다. count()를 빼고 for문을 돌리고 join으로 문자열을 합쳐 리턴하면 문자열을 어드민 컬럼에 표시할 수 있고요. 이렇게 표시하면 어드민 리스트의 컬럼명이 `get_like_food`로 표시됩니다. 이걸 바꾸려면 어떻게 해야 할까요? 위와 같이 `short_description` 값을 지정해주면 됩니다.   
