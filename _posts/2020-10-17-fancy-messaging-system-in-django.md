---
layout: post
title: 장고(Django)에서 간단하면서 매력적인 메시지 알림 만들고 사용하기
tags: django message bootstrap
comments: true
---

아래와 같이 웹사이트 상단에서부터 살짝 나타났다가 사라지는 메시지로 사용자에게 알림을 주는 경우를 본 적이 있을 것입니다. 장고의 메시지 라이브러리와 부트스트랩을 사용하면 이렇게 매력적인 알림을 별도의 라이브러리 없이 간단히 구현할 수 있습니다.  
![메시지예시](https://devopspoints.com/uploads/library/bootstrap4/img_5db28f0b1a58a.png)

---

## 1단계 (동작하게 만들기) 
**장고 뷰**  
뷰에서 아래와 같이 메시지를 보내는 코드를 작성합니다.  

{% highlight django %}{% raw %}
from django.contrib import messages

def some_function(request):
    ...
    messages.warning(request, "권한이 없습니다.")
    ...
{% endraw %}{% endhighlight %}

**HTML 템플릿**  
템플릿에서는 이 메시지를 받아 표시해줍니다. 아래와 같이 `messages`를 돌면서 메시지 아이템에 접근할 수 있습니다. `message.tags`에는 메시지 레벨이 들어있고, `message`에는 사용자가 쓴 메시지가 들어 있습니다.  
{% highlight django %}{% raw %}
{% for message in messages %}
    {{ message.tag }}
    {{ message }}
{% endfor %}
{% endraw %}{% endhighlight %}

---

## 2단계 (에쁘게 예쁘게)
그러나 이렇게만 해두면.. 안 예쁩니다. 더 예쁘게 만들어 보겠습니다. 아래와 같이 부트스트랩을 이용해서 스타일링을 해줍니다.   

**HTML 템플릿**  
{% highlight django+html %}{% raw %}
{% for message in messages %}
    <div class="alert {{ message.tags }} alert-auto-dismissible alert-dismissible notification-container text-center" role="alert">
        <button type="button" class="close" data-dismiss="alert" aria-label="Close">
            <span aria-hidden="true">&times;</span>
        </button>
        {{ message }}
    </div>
{% endfor %}
{% endraw %}{% endhighlight %}

여기서 중요한 부분은 `message.tags`를 클래스 중간에 넣어준 것입니다. 그리고 `settings.py`에 아래 설정을 해줍니다. 이렇게 하면 메시지 레벨이 바뀔 때마다 자동으로 부트스트랩 alert의 클래스를 바꿔주기 때문에 경고는 주황색으로, 성공은 녹색으로 알림이 나타납니다.    

**Django의 settings.py**  
{% highlight django %}{% raw %}
MESSAGE_TAGS = {
    messages.DEBUG: 'alert-info',
    messages.INFO: 'alert-info',
    messages.SUCCESS: 'alert-success',
    messages.WARNING: 'alert-warning',
    messages.ERROR: 'alert-danger',
}
{% endraw %}{% endhighlight %}

---

## 3단계 (멋지게)
여기까지하면 뷰에서 보낸 메시지 레벨에 맞춰 템플릿에서 알맞은 색상의 부트스트랩 alert으로 표현해주고 있을 겁니다. 그런데 사용자가 일일이 오른쪽 x를 눌러 알림을 끄도록 하는게 좋은 생각같지는 않네요. 아래와 같이 간단한 자바스크립트를 쓰면 원하는 시간 후에 사라지도록 만들 수 있습니다. 4초로 한번 해볼까요?  

{% highlight javascript %}
window.setTimeout(function() {
    $(".alert-auto-dismissible").fadeTo(500, 0).slideUp(500, function(){
        $(this).remove();
    });
}, 4000);
{% endhighlight %}

---

여기까지 마쳤다면 끝입니다. 이제 간단히 뷰에서 `message.warning(request, "안녕")` 해주는 것만으로 웹브라우저에 멋진 알림이 나타났다가 사라지게 됩니다. `alert`은 이제.. 안녕..  
