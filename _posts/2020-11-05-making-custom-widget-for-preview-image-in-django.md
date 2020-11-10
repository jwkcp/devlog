---
layout: post
title: 장고(Django) 위젯(Widget)을 이용해 사진 미리보기 가능한 업로드 컨트롤 만들기
tags: django
comments: true
---

# 개요

## 왜 커스텀 위젯인가  

장고(Django)로 폼을 만들다보면 반복해서 쓰는 컨트롤인데 기본 컨트롤로는 뭔가 좀 부족할때가 있습니다. 장고에서는 이런 컨트롤을 위젯(Widget)이라고 부릅니다. 예를 들어, 숫자를 입력했을 때 음수이면 텍스트박스가 빨간색으로, 양수이면 파란색으로 바뀌는 Input컨트롤이 필요하다면 이걸 위젯으로 만들수도 있습니다. 당연히 이런 간단한 기능은 위젯 없이 그냥 HTML, CSS, JS로도 충분합니다. 굳이 커스텀 위젯까지 만들어 쓰는게 비효율적입니다. 그러나 사용자가 사진을 업로드하면 업로드한 사진을 보여주어야 한다면 어떻게 해야 할까요. 그리고 폼에서 붕어빵처럼 찍어내고 싶다면요. 위도와 경도를 받으면 지도를 표시하고 마커를 찍는 컨트롤이 필요하다만 어떻게 해야 할까요.  

커스컴 위젯은 장고의 기본 컨트롤만으로는 좀 부족하고, HTML과 Javascript만 쓰는게 아니라 장고의 폼과 좀 더 긴밀하게 동작해야 하는 경우 유용합니다. 물론 커스텀 위젯 내부에서는 HTML과 CSS로 모양을 꾸미고, JS로 동작을 제어를 구현합니다. 이렇게 만든 위젯을 폼에서 그냥 한 줄로 불러와주면 간단하게 다양한 기능이 들어간 위젯을 팡!팡! 찍어낼 수 있습니다. 이 글에서는 장고의 커스텀 위젯을 이용해 이미지 필드의 폼을 구현해보겠습니다. 기존 이미지가 있다면 기존 이미지를 보여주고 사용자가 새로 이미지를 업로드하면 기존 이미지 대신 새로 업로드한 이미지를 보여줄겁니다.   

# 프리뷰 가능한 이미지 업로드 위젯 만들기  
이미지를 업로드하는 기본 위젯를 템플릿에 불러보면 이게 참 있을 기능은 다 있지만 좀 부족합니다. 기존 파일은 보여주지도 않고, 업로드한 파일도 보여주지 않습니다. 그냥 URL경로와 삭제 기능이 있는 취소 체크박스만 덜렁 보여주지요. 이 이미지 필드는 모델에서 `ImageField`로 정의됩니다. 모델폼을 이용해 템플릿에 표시하면 Input 상자와 버튼이 나타나는데 이 기본 위젯은 `from django.forms import ClearableFileInput`를 임포트한 후 `ClearableFileInput`을 상속해서 만듭니다.   
  
**여기서 잠깐**  
폼셋을 
{% hightlight django+python %}{% raw %}
{{ formset.as_p }}
{% endraw %}{% endhighlight %}
이렇게 쓰면 상관이 없지만 각 필드를 개별적으로 템플릿에서 불러 쓴다면 잊지 말아야 할 것이 있습니다.  
1. management_form
2. form.id
3. can_delete를 설정한다면 form.DELETE
4. can_order를 설정한다면 form.ORDER

{% hightlight django+python %}{% raw %}

{{ formset.management_form }}
{% if formset.can_delete %}
    {% for form in formset %}
    {{ form.id }}
    {{ form.DELETE }} # 옵션에 따라 선택사항
    {{ form.ORDER }} # 옵션에 따라 선택사항
    {% endfor %}
{% endif %}

{% endraw %}{% endhighlight %}

## 구조
크게 아래와 같은 소스 파일로 되어 있습니다.  

- **forms.py**: 우리가 알고 있는 그 장고 폼 맞습니다. 여기에서 `class Meta`에 우리가 만든 위젯을 넣어줄거에요.  
- **custom_widgets.py**: 여기에 우리가 만들 위젯을 구현합니다.  
- **preview_imagefield.html**: 여기에 우리가 만들 위젯을 꾸며줍니다. 겉으로 보여지는 모습부터 버튼을 눌렀을 때 프론트엔드의 동작을 자바스크립트로 구현할 수 있습니다.  

---

### custom_widgets.py 위젯 본체
길어 보이지만 대부분 주석입니다. render 함수를 유심히보시면 됩니다.  
  
{% highlight django+python %}
from django.forms import ClearableFileInput
from django.template.loader import render_to_string

class PreviewImageFileWidget(ClearableFileInput):
    # 아래와 같이 템플릿을 지정해서 구현할수도 있습니다. 여기서는 render_to_string을 쓸 예정이니 주석처리
    # template_name = 'common/widgets/preview_imagefile_widget.html'

    # 아래와 같이 js코드 링크를 불러올수도 있습니다. 여기서는 이 방법은 안쓰니 주석처리
    # class Media:
    #     js = [
    #         'https://code.jquery.com/jquery-3.4.1.min.js'
    #     ]

    #############################################
    # render 함수를 오버라이딩 해줍니다.
    #############################################
    # -- value는 이미지가 들어갑니다. (여기서는 ClearableFileInput이므로)
    # -- name은 ClearableFileInput이 적용되는 우리 폼의 imagefield 이름이 들어갑니다. 이 이름은 우리가 PreviewImageFileWidget을 쓸 HTML코드에 있겠죠?
    # -- attrs에는 id 등의 값이 dict 형태로 들어갑니다.
    ##############################################

    def render(self, name, value, attrs=None, renderer=None):
        # 커스텀 위젯 템플릿으로 전달할 context를 만들어주고
        context = {
            'value': value,
            'name': name,
            'id': attrs['id']
        }

        # render_to_string을 이용해 HTML코드와 context를 잘 버무려줍니다.
        html = render_to_string(
            'common/widgets/preview_imagefile_widget.html', context)

        # 아래 코드를 통해 상속받은 위젯의 HTML코드를 불러울 수 있습니다.
        # parent_html = super().render(name, value, attrs, renderer)

        # 또 아래와 같이 .py 코드에서 스크립트를 짜서 동작토록 할수도 있습니다.
        # 이렇게 하려면 아래 return html + inline_code 이런식으로 하면 되겠죠?  
        # inline_code = mark_safe(
        #    <script>
        #        $('.btn').change(function() {
        #            alert('테스트');
        #        });
        #    </script>
        )

        # 그리고 return 해주면 끝
        return html

{% endhighlight %}

---

### preview_imagefield_widget.html 위젯 꾸미기
{% highlight html+javascript %}{% raw %}
<!-- 기존이미지 -->
<div>
    <img id='imagePreview' src='{{ value.url }}'/>
    <button id='imageClearBtn' type='button'>삭제</button>
    
    <!-- 기존 ClearableFileInput의 삭제 기능을 활용하기 위해 위에 삭제 버튼을 누르면 체크되도록 하되 안보이게 설정 -->
    <div class="custom-control custom-checkbox d-none">
        <input type="checkbox" id="chk-{{ name }}" class="custom-control-input" name="chk-{{ name }}">
        <label class="custom-control-label" for="chk-{{ name }}">이미지를 삭제하려면 체크하세요.</label>
    </div>

    <!-- 파일 업로드 폼 좀 더 예쁘게(선택사항) -->
    <div class="custom-file">
        <input type="file" id="{{ id }}" class="custom-file-input" name="{{ name }}" accept="image/*" style="cursor: pointer;">
        <label for="{{ id }}" class="custom-file-label">파일 선택</label>
    </div>

    <!-- 여기서부터 스크립트 -->
    <script>
        <!-- 삭제 버튼이 클릭되면 이미지 안보이게 한 후 숨겨진 삭제 체크박스 값 변경 -->
        document.getElementById('imageClearBtn').addEventListener('click', function(e) {
            document.getElementById('imagePreview').src = "";
            document.getElementById('chk-{{ name }}).checked = true;
        });

        // 파일 선택 이벤트 등록
        var imgFieldCtrl = document.getElementById('{{ id }}')
        imgFieldCtrl.addEventListener('change', function(e) {
            if (imgFieldCtrl.files && imgFieldCtrl.files[0]) {
                var reader = new FileReader();
                reader.onload = function(e) {   
                    document.getElementById('imagePreview').src = e.target.result;
                }

                // convert to base64 string
                reader.readAsDataURL(document.getElementById('{{ id }}').files[0]); 
            }
        });
    </script>
</div>
{% endraw %}{% endhighlight %}

---

### forms.py 위젯 사용
{% highlight django+python %}
from common.custom_widgets import PreviewImageFileWidget

class MyForm(forms.ModelForm):
    class Meta:
        model = School
        widgets = {
            'image': PreviewImageFileWidget()
        }
{% endhighlight %}

---

# 마무리
위와 같이 상속을 이용한 파이썬 위젯 코드, 위젯 HTML과 스크립트 코드, 그리고 이렇게 완성된 위젯을 `class Meta`에서 지정해주는 코드, 크게 3부분으로 나눌 수 있습니다. 이제 예전에 하던데로 폼 위젯 중에 이미지 프리뷰 업로드 기능이 필요한 경우 `class Meta`에서 `widgets`에 `PreviewImageFileWidget()`면 모든 준비가 끝납니다.  