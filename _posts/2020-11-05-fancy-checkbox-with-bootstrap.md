---
layout: post
title: 부트스트랩을 이용해 쪼금 더 나은 체크박스 만들기
tags: bootstrap html css
comments: true
---

{% highlight html %}
<!DOCTYPE html>
<head>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.16.0/umd/popper.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
</head>
<body>
    <form>
        <div class="custom-control custom-checkbox">
            <input type="checkbox" class="custom-control-input" id="customCheck" name="exampleCheckbox">
            <label class="custom-control-label" for="customCheck">Fancy checkbox</label>
        </div>
        <button type="submit" class="btn btn-primary">저장</button>
    </form>
</body>
</html>
{% endhighlight %}