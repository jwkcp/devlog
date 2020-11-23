---
layout: post
title: 장고(Django) 어드민(admin)에 내가 원하는 동작 추가하기
tags: django
comments: true
---

# 문제

장고 어드민은 기본적으로 리스트에서 체크박스를 통해 데이터를 선택하면 '삭제'기능만 들어 있습니다. 여기에 원하는 기능을 넣고 싶다면 어떻게 해야 할까요?   

외래키 등 관계를 사용하지 않는 모델에도 당연히 사용 가능하지만 여기서는 외래키가 있는 상태를 가정하고 진행해보겠습니다. `Student`와 `Test` 모델이 있고 `Student`에 `Test`가 외래키로 매달려 있는 모델을 떠올려보세요. 평균 점수를 가져다 쓰는 페이지가 많은데 매번 이 평균 점수를 계산해서 보여주려니 디비 히트와 속도가 신경쓰인다면 이런 평균 점수를 가지는 컬럼 `grade_avg`를 `Student` 모델에 추가해줄 수 있습니다. 이렇게 추가된 컬럼을 어드민에서 수동으로 갱신, 업데이트하는 기능을 추가하고 싶다면 어떻게 해야 할까요?  

---

# 방법
간단합니다. admin 액션 함수를 만들어 `list_display`에 필드 추가하듯이 추가해주면 됩니다.  

## 1. 액션 함수 만들기
원하는 앱의 `admin`에 함수를 하나 만들어 줍니다. 평균을 계산하는 걸 만들것이므로 `Avg`를 임포트 해주겠습니다. 함수의 시그니처는 `modeladmin`, `request`, `questset`을 넣어줍니다. 여기서 `queryset`이 중요한데 이 액션 함수를 추가해줄 모델의 단일 인스턴스를 리턴하는 쿼리셋입니다.  

우리는 `Student`의 `Test`의 평균 점수를 업데이트하는 것을 해보기로 했으므로 아래와 같이 합니다.  

{% highlight django+python %}
from django.db.models import Avg


def my_action(modeladmin, request, queryset):
    # 평균 계산
    grade_avg = queryset.aggregate(Avg('test__grade')).get('test__grade__avg)
    # 해당 모델 인스턴스(여기서는 사용자가 어드민에서 체크한 인스턴스들)에 업데이트
    queryset.update(grade_avg=grade_avg)
{% endhighlight %}

더블 언더스코어가 좀 낯설 수 있는데 걱정하지 마세요. 이렇게 언더스코어 두 개를 쓰면 참조하는 외래키의 컬럼에 접근할 수 있는 편리한 기능이랍니다.  

잠깐, 어드민에서 복수 개의 항목을 선택하면 위 쿼리셋으로 계산된 값이 모든 컬럼에 업데이트 됩니다. 경우에 따라 이런 동작이 의도한 동작이 아닐 수 있습니다. 각 항목을 개별적으로 계산해서 업데이트 하고 싶다면 list() 등으로 쿼리셋을 평가(DB접근)하여 값을 업데이트 할 수 있습니다.  

{% highlight django+python %}
from django.db.models import Avg


def my_action(modeladmin, request, queryset):
    for student in list(queryset):
        # 평균 계산
        grade_avg = student.test.aggregate(Avg('grade')).get('grade__avg')

        # aggregate는 계산 결과를 dict로 리턴하고, get으로 접근하면 해당 키가 없으면 None을 리턴합니다.
        student.grade_avg = grade_avg if grade_avg else 0
        student.save()
{% endhighlight %}

---

## 2. StudentAdmin에 추가하기
게속 `Student` 모델을 다루고 있으므로 어드민 클래스 이름을 `StudentAdmin`이라고 하겠습니다.  

{% highlight django+python %}
class StudentAdmin(admin.ModelAdmin):
    # 여기에 어드민 리스트에서 보일 필드를 쓰는 건 알고 계시죠?
    list_display = ['...'] 

    # 비슷하게 이렇게 액션을 추가해줍니다. 이게 끝이에요. 간단하죠?
    actions = ['my_action', ]
{% endhighlight %}

---

## 3. 이름 만들어 주기
이제 어드민에 가서 아이템을 선택 후 삭제 등 기능이 있는 드롭다운을 눌러보면 내가 만든 기능이 보여집니다. 그런데 이름이 함수 이름 그대로네요. 이걸 좀 알기 쉽고 보기 쉽게 만들려면 아래와 같이 해주면 됩니다. (함수 밖에 써줘야 합니다.)  

`my_action.short_description="내가 쓰고 싶은 이름"`  

---

이 어드민 액션 관련 내용의 장고 공식 문서는 [여기](https://docs.djangoproject.com/ko/3.1/ref/contrib/admin/actions/)를 참고하세요.  

