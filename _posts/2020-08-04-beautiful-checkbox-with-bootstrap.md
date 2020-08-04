---
layout: post
title: 부트스트랩을 이용해 초간단 체크박스 예쁘게 만들기
tags: bootstrap
comments: true
---

체크박스는 전형적인 모습을 가지고 있는 컨트롤입니다. 별도의 css작업을 해주지 않으면 사각형에 체크되는 방식인데 부트스트랩을 쓰더라도 기본적으로 체크박스의 모습이 그렇~게 세련되게 바뀌지 않습니다. 하지만 아래 코드를 쓰면 iOS에서 사용되는 토글 버튼 같은 모양으로 바꿀 수 있습니다. 별도로 CSS를 작성하지 않아도 됩니다.

## 핵심 코드

{% highlight html %}
<div class="custom-control custom-switch">
    <input type="checkbox" class="custom-control-input" id="customSwitch1">
    <label class="custom-control-label" for="customSwitch1">Toggle switch</label>
</div>
{% endhighlight %}

---

## 전체 코드

{% highlight html %}
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Bootstrap 4 Toggle Switch</title>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css">
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css">
        <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js"></script>
        <style>
            .bs-example{
                margin: 20px;        
            }
        </style>
    </head>
    <body>
        <div class="bs-example">
            <form>
                <div class="custom-control custom-switch">
                    <input type="checkbox" class="custom-control-input" id="customSwitch1">
                    <label class="custom-control-label" for="customSwitch1">Toggle switch</label>
                </div>
                <div class="custom-control custom-switch mt-2">
                    <input type="checkbox" class="custom-control-input" disabled id="customSwitch2">
                    <label class="custom-control-label" for="customSwitch2">Disabled switch element</label>
                </div>
            </form>
            <p class="mt-4"><strong>Note:</strong> Click on the toggle switch to see how it works.</p>
        </div>
    </body>
</html>
{% endhighlight %}
  
*Reference link: https://www.tutorialrepublic.com/codelab.php?topic=bootstrap&file=toggle-switch*
