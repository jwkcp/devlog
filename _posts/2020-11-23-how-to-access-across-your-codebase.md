---
layout: post
title: 장고(Django)에서 네비게이션 메뉴와 같이 코드 베이스 전체에서 접근해야 하는 값이 있다면 어떻게 해야 할까요?
tags: django
comments: true
---

# 문제

장고에서 네비게이션과 같이 동적 메뉴 등 항상 웹페이지에 떠있어야 하거나 여러 앱에서 접근해야 하는 값이 있어야 하는 경우 어떻게 해야 할까요.  
장고에 익숙치 않은 분들은 이런 값을 각 뷰 마다 복사해서 붙여넣어 사용합니다. 그런데 이렇게 하다보면 뭔가 다른 방법이 있지 않을까 하는 생각이 듭니다. 그때 필요한 것이 `context_processor` 입니다. 뭔가 복잡하고 어려울 것 같지만 그렇지 않습니다. 간단히 함수 하나 정의해주고 `settings.py`에서 써주면 프로젝트 전체 템플릿에서 이 값에 접근할 수 있습니다.  

---

# 방법

## 1. context_processors.py 만들기
아래와 같이 앱이나 프로젝트 디렉토리에 `context_processors.py`라는 파일을 만들어줍니다. 이름은 아무래도 상관없지만 이렇게 이름을 지어주면 알아보기도 편하고 여러모로 좋습니다. 매개변수로 `request`를 주어야 한다는 점만 기억해주세요.  

{% highlight django+python %}
def menu_list(request):
    menu_list = Menu.objects.all(is_enable=True)
    return {
        "menu_list": menu_list
    }
{% endhighlight %}

---

## 2. settings.py에 적어주기  
`context_processors.py`를 위치시킨 프르젝트 폴더 이름이 `mysite`라면 이래와 같이 `settings.py`에 추가해줍니다.  

{% highlight django+python %}
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                ...
                'mysite.context_processors.menu_list',
                ...
            ],
        },
    },
]
{% endhighlight %}

---

## 3. 씁니다.
이제 그냥 템플릿에서 하던대로 쓰기만 하면 됩니다.  

{% highlight django+python %}{% raw %}
{{ menu_list }}
{% endraw %}{% endhighlight %}
  