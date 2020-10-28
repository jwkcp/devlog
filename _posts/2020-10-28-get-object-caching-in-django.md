---
layout: post
title: 장고(Django)에서 CBV사용 시, 여러 번 호출되는 get_object 함수 오버라이딩하여 효율 높이기
tags: django
comments: true
---

장고(Django)에서 제네릭 뷰를 쓰다보면 `success_url`을 호출하는 `get_success_url()`이나 권한 검사 등을 위해 `dispatch()` 함수를 호출하는 일이 생깁니다. 이때 해당 객체를 얻기 위해 `get_object()`가 호출되게 되는데요. 이 함수는 DB를 엑세스하게 됩니다. 같은 객체를 가지고 오기 위해서 두 번, 세 번씩 DB를 조회하게 하고 싶지는 않습니다. 어떻게 하면 될까요. 객체를 가져오는 역할을 하는 `get_object()` 함수를 오버라이딩 해주면 됩니다.

{% highlight python+django %}
class MyClass(UpdateView):
    # 이 변수를 직접 사용하지 않습니다. get_object에서 객체를 임시 저장하는 용도로만 사용합니다.
    _object = None

    # get_object의 호출을 최소화하기 위해 오버라이딩
    def get_object(self, queryset=None):
        if self._object is None:
            self._object = super().get_object(queryset)
        return self._object
{% endhighlight %}

이렇게 하면 한 번 DB에서 가져온 객체를 재조해해서 오지 않습니다.  

*만약 객체의 접속 권한을 실시간으로 바꾸는 등 객체의 현재 상태가 중요한 작업을 한다면 이 방법을 쓰면 해당 상태 정보를 제대로 반영하지 못할 수 있으니 다른 방법을 고민해서 사용하세요. :)*
