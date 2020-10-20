---
layout: post
title: 장고(Django)에서 커스텀 템플릿 태그같은 공통 모듈은 어디에 두어야 할까?
tags: django
comments: true
---

장고(Django)에서 아래와 같은 커스텀 템플릿 태그를 구현했다고 하자. 이 태그를 여러 앱에서 써야 한다면 어떻게 해야 할까.  

{% highlight django %}
from django import template

register = template.Library()

@register.simple_tag
def is_zero(val):
    return True if val == 0 else False
{% endhighlight %}

---

**1. 복사해서 각 앱마다 붙여넣는다.(완전 비추)**   
가장 간편하지만 여러군데 코드 중복이 생깁니다.  

**2. 별도의 common같은 더미 앱을 만들어 여기에 공통모듈을 넣는다.(비추)**  
코드 중복없이 공통 모듈을 장고(Django)에서 사용하는 보편적인 방법이었습니다.  

**3. settings.py에 `OPTIONS`을 사용한다.(추천)**   
장고 1.9이후로 `settings.py`의 설정을 이용하면 더미 앱을 만들지 않고도 커스텀 템플릿 태그같은 공통모듈을 사용할 수 있습니다. 어느 위치에 설정을 해야 하는지 헷갈려합니다. 아래 예를 보시죠.   

## 프로젝트 구조  
{% raw %}
mysite
    - myapp
    - mysite
        - manage.py
        - templatetags
            - custom_tags.py
{% endraw %}

## SETTINGS.PY
{% highlight django %}
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates'), os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
            'libraries': {
                'custom_tags': 'mysite.templatetags.custom_tags',
            }
        },
    },
]
{% endhighlight %}

`OPTIONS`설정 아래 `libraries` 항목을 추가하면 끝입니다. `custom_tags`의 이름은 템플릿에서 참조할 이름으로 해주면 되고, 뒤의 값은 경로를 써주면 되는데 위의 예에서는 `mysite`아래 `manage.py` 파일이 있는 동일 레벨의 디렉토리에 `templatetags`라는 디렉토리를 만들고 그 안에 `custom_tags.py`이 있는 경우입니다.   

이렇게 하면 쓸데없는 더미 앱 없이 공통 모듈을 모든 앱에서 참조하여 사용할 수 있습니다.  