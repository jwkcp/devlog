---
layout: post
title: 장고(Django)에서 썸머노트(summernote) 첨무 파일 사이즈 용량 제한 설정 방법
tags: django summernote
comments: true
---

# 문제

장고에서 썸머노트에 이미지 등 파일 첨부를 해보면 1MB 이상은 업로드가 되지 않습니다. 이 제한을 어떻게 바꿀 수 있을까요?  

---

# 원인

썸머노트의 기본설정이 `1024 * 1024`로 되어 있어서 그렇습니다. 기본 바이트(Byte) 단위이므로 `1024 = 1KB`이니 `1024 * 1024`는 `1MB`를 뜻합니다. 썸머노트의 [소스코드](https://github.com/summernote/django-summernote/blob/master/django_summernote/apps.py)를 살펴보면 알 수 있는데요. `get_default_config`라는 메소드 안에 아래와 같이 설정되어 있습니다.  

{% highlight django+python %}
def get_default_config(self):
    return {
        ...

        # Attachment settings
        'disable_attachment': False,
        'attachment_upload_to': uploaded_filepath,
        'attachment_storage_class': None,

        'attachment_filesize_limit': 1024 * 1024,
        
        'attachment_require_authentication': False,
        'attachment_model': 'django_summernote.Attachment',
        'attachment_absolute_uri': False,

        ...
    }
{% endhighlight %}

---

# 방법

`settings.py`에서 `attachment_filesize_limit`에 값을 지정해주면 됩니다.


{% highlight django+python %}
SUMMERNOTE_CONFIG = {
    'attachment_filesize_limit': 5 * 1024 * 1024
}
{% endhighlight %}
