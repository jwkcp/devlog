---
layout: post
title: DRF Serializer에서 app_name 사용 시 기본 url 에러 및 해결 방법
tags: django django-rest-framework
comments: true
---

## 문제

장고는 url 패턴 설정 시 네임스페이스 구분을 위해 `app_name`을 사용하는 경우가 많은데 DRF serializer에서 `HyperlinkedModelSerializer`를 사용할 경우 아래와 같은 에러 메시지와 함께 url을 찾지 못하는 경우가 발생한다.

{% highlight python %}
ImproperlyConfigured at /users/
Could not resolve URL for hyperlinked relationship using view name "user-detail". You may have failed to include the related model in your API, or incorrectly configured the `lookup_field` attribute on this field.
{% endhighlight %}

## 원인

DRF(django-rest-framework)는 `url` 필드가 기본적으로 `{MODELNAME}-detail`와 같은 형식으로 설정되게 되어 있다. 따라서 `app_name`을 `myapp`이라고 설정했을 경우 url 탐색을 `myapp:user-detail`로 하게되지만 기본 url 설정이 `user-detail`로 되어 있어 url을 찾지 못하는 경우가 생긴다.

## 해결 방법

`app_name`을 사용하지 않으면, 그러니까 네임스페이스를 사용하지 않으면 이런 문제가 발생하지 않는다. 하지만 장고에서 `app_name`을 통한 네임스페이스 설정은 흔하며 권장되는 사항이다. 그렇기 때문에 DRF를 위해 `app_name`을 제거하는 것은 좋은 해결책이 아니다.

아래와 같은 방법을 통해 기본 url을 `app_name`이 지정하는 네임스페이스를 포함하는 형식으로 변경할 수 있다.

{% highlight python %}
class UserSerializer(serializers.HyperlinkedModelSerializer):
snippets = serializers.HyperlinkedRelatedField(many=True, view_name='snippets:snippet-detail', read_only=True)

    class Meta:
        model = get_user_model()
        fields = ['url', 'username', 'snippets', ]
        extra_kwargs = {'url': {'view_name': 'snippets:user-detail'}}

{% endhighlight %}

눈여겨 봐야 할 부분은 `extra_kwargs = {'url': {'view_name': 'snippets:user-detail'}}` 부분이다. `extra_kwargs`를 통해 기본 url을 네임스페이스를 포함한 url로 변경 설정할 수 있다.
