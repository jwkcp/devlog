---
layout: post
title: 장고(Django)에서 ModelAdmin 상속 받아 코드 재활용성 높이기
tags: django
comments: true
---

# 문제

장고에서 상속받은 받은 모델이 있고, 이를 어드민에서 구현하려면 `list_display`의 항목의 동일 컬럼을 복사해서 붙여넣기어야 합니다. 복사해서 붙여넣는 것 자체는 어려운 일이 아니지만 코드 중복이 발생하고 특정 컬럼을 넣고 뺄 때 각 어드민을 모두 만져줘야 합니다. 실수할 가능성도 생기고 효율도 떨어집니다. 어떻게 해야 할까요.  

---

# 예시

## 모델
대충 Phone 모델이 있고, 이를 상속하는 AndroidPhone이 있다고 하면  
{% highlight django+python %}
class Phone:
    model_name
    rel_date
    color
    ...

class AndroidPhone(Phone):
    google_engine_version
    ...

{% endhighlight %}

---

## 어드민
어드민은 기본적으로 이렇게 할 수 있습니다. 그런데 이렇게 하면 `list_display`에 똑같은 컬럼명을 복사해서 넣어주는 중복이 발생하게 됩니다.  
{% highlight django+python %}
@admin.register(Phone)
class PhoneAdmin(admin.ModelAdmin):
    list_display = ['model_name', 'rel_date', 'color', ]

@admin.register(AndroidPhone)
class AndroidPhoneAdmin(admin.ModelAdmin):
    list_display = ['model_name', 'rel_date', 'color', 'google_engine_version', ]
{% endhighlight %}

이걸 이렇게 고칠 수 있습니다.  
{% highlight django+python %}
@admin.register(Phone)
class PhoneAdmin(admin.ModelAdmin):
    list_display = ['model_name', 'rel_date', 'color', ]

@admin.register(AndroidPhone)
class AndroidPhoneAdmin(PhoneAdmin):
    def get_list_display(self, request):
        return self.list_display + ['google_engine_version', ]
{% endhighlight %}

이렇게 하면 공통으로 사용할 컬럼을 넣고 뺄 때도 매우 편리하고 코드 중복도 사라지게 됩니다.  
