---
layout: post
title: 우분투에서 bazel 설치 방법
tags: linux
comments: true
---

[Bazel](https://www.bazel.build/)은 구글에서 만든 빌드 및 테스트 자동화 도구 입니다. 원래는 Blaze라고 구글 내부에서 사용하는 빌드 툴이었는데 2015년 Bazel라는 이름으로 오픈 소스로 공개되었습니다. 바젤? 바질? 한국어로는 어떻게 써야 할지 모르겠는데 초기 로고가 아래와 같이 알파벳 b를 바질(Basil)잎사귀 두 개로 표현한 것 보니 식물 바질(basil)을 좀 중의적 표현으로 쓴 것 아닌가 하는 생각을 해봅니다.

![Bazel 초기 로고](https://upload.wikimedia.org/wikipedia/en/f/f6/Bazel_logo.png)  
Bazel 초기 로고 모습

![Basil 식물 모습](https://upload.wikimedia.org/wikipedia/commons/9/90/Basil-Basilico-Ocimum_basilicum-albahaca.jpg)  
바질(Basil) 식물의 모습

![새로운 Bazel 로고](https://upload.wikimedia.org/wikipedia/en/thumb/7/7d/Bazel_logo.svg/220px-Bazel_logo.svg.png)  
Bazel의 새로운 로고

---

이름이 영어 사전에 안나오는 단어이고 생소하여 무슨 뜻인지 찾아보느라 말이 길어졌네요. 각설하고 우분투에서 어떻게 설치하는지 알아보겠습니다. 아마 Bazel을 설치하려는 분들 중 다수가 텐서플로우 때문일겁니다. 텐서플로우 저장소에서 받아서 ./configure 명령을 하면 아래와 같은 메시지가 나옵니다.

`Cannot find bazel. Please install bazel. Configuration finished`

그래서 설치를 위해 `sudo apt install bazel`하면 패키지를 찾을 수 없다고 나옵니다. @.@ 어떻게 해야 할까요?

---

Bazel을 설치하려면 먼저 Bazel 저장소를 apt로 설치할 수 있도록 저장소와 그에 맞는 키를 등록해줘야 합니다. 두 가지 모두 리눅스 사용자들에겐 흔히 접하는 상황이나 그렇지 않은 분들은 좀 당황스럽고 난해하게 느껴질수도 있을 것 같습니다. 쉽게 말하면 Bazel을 내려받을 주소를 리눅스에 알려주고 -> 올바른 설치인지 인증하기 위해 키를 등록해주고 -> 설치 이렇게 보시면 됩니다.

## 패키지 저장소 목록에 추가하기

{% highlight shell %}
echo "deb [arch=amd64] https://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list
sudo apt-get install curl
curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -
{% endhighlight %}

한 줄씩 설명하면 -  
`echo "deb [arch=amd64] https://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list`  
리눅스에서 패키지 배포 목록을 보관하고 있는 sources.list.d에 bazel.list를 등록해줍니다.

`sudo apt-get install curl`  
파일을 내려받을 수 있는 유틸리티인 curl을 설치합니다. 이미 설치한 분은 그냥 넘어가셔도 됩니다.

`curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -`  
Bazel 설치를 위한 gpg 키를 리눅스에 등록합니다.

## 설치하기

{% highlight shell %}
sudo apt-get update
sudo apt-get install bazel
{% endhighlight %}

`sudo apt-get update`
패키지 배포 목록이 새로 추가되었으니 update 해주고요.

`sudo apt-get install bazel`  
이제 Bazel을 설치합니다.
