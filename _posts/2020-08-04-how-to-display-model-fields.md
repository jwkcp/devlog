---
layout: post
title: Django에서 모델 필드 모두 보는 방법
tags: django
comments: true
---

Django(장고)에서 모델의 모든 필드를 보려면 어떻게 해야 할까요? 예를 들어, `django.contrib.auth.models`에 있는 `User` 모델에 어떤 필드가 있는지 보려면 어떻게 해야할까요? 아래와 같이 한 줄이면 됩니다.  

{% highlight python %}
User._meta.get_fields()
{% endhighlight %}

그러면 아래와 같이 필드 목록이 표시됩니다.

{% highlight python %}
(<ManyToOneRel: admin.logentry>, <ManyToOneRel: account.emailaddress>, <ManyToOneRel: socialaccount.socialaccount>, <OneToOneRel: klass_account.profile>, <ManyToOneRel: klass_account.consultenablehistory>, <ManyToOneRel: common.feedback>, <django.db.models.fields.AutoField: id>, <django.db.models.fields.CharField: password>, <django.db.models.fields.DateTimeField: last_login>, <django.db.models.fields.BooleanField: is_superuser>, <django.db.models.fields.CharField: username>, <django.db.models.fields.CharField: first_name>, <django.db.models.fields.CharField: last_name>, <django.db.models.fields.EmailField: email>, <django.db.models.fields.BooleanField: is_staff>, <django.db.models.fields.BooleanField: is_active>, <django.db.models.fields.DateTimeField: date_joined>, <django.db.models.fields.related.ManyToManyField: groups>, <django.db.models.fields.related.ManyToManyField: user_permissions>)
{% endhighlight %}

---

보기가 복잡하다면 각 필드의 `name`만 표시하면 좀 더 깔끔하게 표시됩니다.

{% highlight python %}
[field.name for field in User._meta.get_fields()]
{% endhighlight %}

그러면 이런 식으로 표시됩니다.

{% highlight python %}
['logentry', 'emailaddress', 'socialaccount', 'profile', 'consultenablehistory', 'feedback', 'id', 'password', 'last_login', 'is_superuser', 'username', 'first_name', 'last_name', 'email', 'is_staff', 'is_active', 'date_joined', 'groups', 'user_permissions']
{% endhighlight %}
