---
layout: post
title: Django에서 카테고리와 같은 동적 메뉴를 모든 페이지에 표시하는 방법
tags: django
comments: true
---

Django(장고)에서 보통 웹페이지를 구성할 때 base.html에 네비게이션 바나 풋터 등 공통으로 늘 보여줘야 하는 부분과 화면 구성을 합니다. 그리고 다른 페이지에서 이 base.html을 확장해 사용하지요.   

그런데 이 때 의문이 생깁니다. 예를 들어, 동적으로 변하는 카테고리를 네비게이션바에 표시하고 싶을 때 어떻게 해야 할까요? base.html을 상속하는 모든 html 페이지의 view에서 카테고리 모델을 조회한 다음 리턴되는 context에 담아 넘겨야 할까요? 불가능한 일은 아니지만 이건 아무리 생각해도 너무 노가다가 심합니다. 페이지가 100개라면 똑같은 카테고리 모델 조회 코드를 복붙해야 하니까요. Django는 이에 대한 해결책이 있습니다. 바로 `context_processors`를 사용하는 겁니다.  

---

## 방법

1. (크게 상관은 없지만)처음 프로젝트를 생성하면 생성되는 디렉토리 아래 `context_processors.py`라는 파일을 만듭니다. 그리고 아래와 같은 코드를 작성합니다. 이 코드는 모든 페이지에서 공통으로 접근했으면 하는 모델 데이터를 제공하는 역할을 합니다. 여기서는 `categories`로 가정하겠습니다.  

{% highlight python %}
from myapp.models import Category

def category_list(request):
    categories = Category.objects.all().order_by('ordering')
    return {
        "categories": categories
    }

{% endhighlight %}

2. `settings.py`를 열고 `TEMPLATES`의 `OPTIONS`아래 `context_processors`의 제일 하단에 `myapp.context_processors.category_list`와 같이 위에서 만든 파일의 함수를 연결해줍니다.  

3. 이제 모든 템플릿에서 `categories`의 객체를 참조할 수 있게 되었습니다.  

---

생각보다 간단하지요? :)  
