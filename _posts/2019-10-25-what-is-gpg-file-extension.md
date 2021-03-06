---
layout: post
title: tf-nightly 란?
tags: ai tensorflow
comments: true
---

`tf-nightly`란 텐서플로우(tensorflow)의 일일 빌드 버전입니다. 쉽게 설명하면 아래와 같습니다.

-   `tensorflow`: 안정(stable)된 버전의 텐서플로우. 안정적이나 버그 패치 등 현재 개발 중인 최신 내용이 포함되어 있지 않다.
-   `tf-nightly`: 개발(develop)중인 버전의 텐서플로우. 매일 매일 개발, 수정되는 텐서플로우를 빌드하여 배포한 버전입니다. 최신 기능과 버그 패치가 포함되어 있으나 불안정 할 수 있다.

---

## 뭘 설치해야 하나

일반적으로 `tensorflow`를 설치하면 됩니다. `tf-nightly`는 최신 기능이나 본인이 사용하는 기능에 버그 패치 등이 필요한 경우에만 설치하면 됩니다.

## 설치, 사용에 관해

사용방법은 기존과 같습니다. `import tensorflow as tf`로 호출할 수 있습니다.

기존 `tensorflow`가 설치된 상태에서 `tf-nightly`를 설치하면 `tf-nightly` 버전의 텐서플로우가 호출됩니다. 반대의 경우 `tensorflow` 버전의 텐서플로우가 호출됩니다. 그러니까 마지막에 설치한 버전의 텐서플로우가 호출되는 것을 직접 설치해보며 확인했습니다. 둘 중 하나를 지우면 텐서플로우가 호출되지 않아 원하는 버전의 텐서플로우를 다시 설치해줘야 했습니다. 그러면 정상적으로 동작합니다.
