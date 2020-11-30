---
layout: post
title: 장고(Django)에서 썸머노트(summernote) 사용 시 너비를 반응형으로 만드는 방법
tags: django summernote
comments: true
---

## 썸머노트

장고에서 summernote를 사용하는 방법은 간단합니다. 설치(`pip install django-summernote`)하고, 앱에 추가(`INSTALLED_APPS += ('django_summernote', )`)해주고, URL 패턴 넣어주고(`url(r'^summernote/', include('django_summernote.urls')),`), MEDIA_URL 넣어주고(`if settings.DEBUG: urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)`) DB마이그레이션(`python manage.py migrate`)해주고 나면 폼에서 `SummernoteWidget()`을 위젯에 대입해주거나, `SummernoteTextFormField`를 필드로 생성해주면 끝이죠. (쓰고 보니 기네요.)  

이런 내용은 `django-summernote` [깃헙 페이지](https://github.com/summernote/django-summernote)에 순서대로 잘 설명되어 있습니다. 

---

## 썸머노트 너비 변경 방법

그런데 이렇게 추가하고 나면 정해진 너비를 에디터가 넘거나, 반응형으로 조절되지 않는 현상을 경험할 수 있는데요. 이때 에디터를 개발자 도구로 열어보면 너비가 768px 정도로 고정되어 있는 것을 볼 수 있습니다. 이 설정을 바꿔주면 됩니다. summernote의 설정은 `settings.py`에서 할 수 있습니다. [여기](https://github.com/summernote/django-summernote#options)에 설정 가능한 옵션과 방법이 나와있는데요. 너비, 높이만 조절할거라면 아래와 같이 하면 됩니다. width를 100%로 주는 것이죠. height는 원하는대로 하시면 됩니다.  

{% highlight django+python %}
SUMMERNOTE_CONFIG = {
    'summernote': {
        'width': '100%',
        'height': '480',
    }
}
{% endhighlight %}

---

## 부가 정보

추가로 장고 3.x를 쓰고 있다면 아래 X_FRAME_OPTIONS을 줘야 하고요.  

{% highlight django+python %}
X_FRAME_OPTIONS = 'SAMEORIGIN'
{% endhighlight %}

테마는 아래와 같이 지정해줄 수 있습니다.  

{% highlight django+python %}
SUMMERNOTE_THEME = 'bs4'
{% endhighlight %}