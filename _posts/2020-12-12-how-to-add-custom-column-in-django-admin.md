---
layout: post
title: 장고(Django) 어드민에 커스텀 컬럼 추가하고 체크박스로 표시하기
tags: django
comments: true
---

# 문제

장고에서 모델에 정해진 컬럼 말고, 컬럼의 값을 계산한 결과를 별도의 컬럼으로 나타내려면 어떻게 해야 할까요? 또 True, False가 아닌 체크마크를 표시하려면 어떻게 해야 할까요.  

---

# 방법

학생에 대한 모델 `Student`가 있다고 가정하고 `name`, `age` 필드가 있다고 해보겠습니다. 이 모델의 기본 어드민 코드는 다음과 같습니다.  

**기본 코드**   
{% highlight django+python %}
from django.contrib import admin
from .models import Student

@admin.register(Student)
class StudentAdmin(admin.ModelAdmin):
    list_display = ['name', 'age', ]
{% endhighlight %}

이 상태에서 나이가 19세를 초과하는지 알기 쉽게 어드민 리스트에서 볼 수 있게 하려면 어떻게 할까요?  
별도의 모델 컬럼을 만들거나 모델에 메소드를 구현할수도 있지만, 이 기능을 관리자만 필요하다고 하면 어드민에 구현하는 것이 간단하고 적합할것입니다. 이 때, 아래와 같이 어드민에 함수를 추가하고 그 함수 이름을 `list_display`에 넣어주면 됩니다.

---

**커스텀 컬럼이 추가된 코드**  
{% highlight django+python %}
from django.contrib import admin
from .models import Student

@admin.register(Student)
class StudentAdmin(admin.ModelAdmin):
    list_display = ['name', 'age', 'is_adult', ]

    def is_adult(self, obj):
        return obj.age > 19
{% endhighlight %}

이 경우 `self`는 어드민 객체가 전달되고, `obj`는 `Student`객체가 전달됩니다.  

이제 어드민에 보면 `is_adult`라는 컬럼이 추가되어 있고 값이 True, False로 표시됩니다. 잘 된 것 같습니다. 여기서 조금 더 나가볼까요? `is_adult`라는 이름이 보기 싫으니 `성인 여부`로 바꾸고 True, False 표시 방법도 문자가 아닌 장고의 체크 표시로 바꿔보도록 하겠습니다.  

---

**더 개선된 코드**  
{% highlight django+python %}
from django.contrib import admin
from .models import Student

@admin.register(Student)
class StudentAdmin(admin.ModelAdmin):
    list_display = ['name', 'age', 'is_adult', ]

    def is_adult(self, obj):
        return obj.age > 19

    is_adult.short_description = '성인 여부'
    is_adult.boolean = True
{% endhighlight %}

이제 다 됐습니다. 훨씬 낫네요 ^^  
