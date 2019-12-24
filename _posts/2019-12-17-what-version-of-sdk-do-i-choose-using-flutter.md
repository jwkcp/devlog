---
layout: post
title: 플러터(Flutter) 배포 시 어떤 버전의 안드로이드 SDK를 선택해야 할까.
tags: ubuntu
comments: true
---

# TL;DR

플러터 최소 지원 버전은 `API 16`, 그러나 사실 상 `API 26`이 필수, 앞으로를 생각하면 `API 28` 권장  

---

플러터(Flutter)는 공식적으로 `API 16(4.1.x / 젤리빈)` 부터 지원한다. 하지만 `API 16`으로 설정하면 아래와 같이 최소한 `API 26(8.0 / 오레오)`이상이 되어야 한다는 경고 메시지가 나온다. *2018년 2분기 기준으로 구글 플레이는 사용자 경험 향상과 보안, 성능 개선을 위해 새로운 앱과 앱 업데이트 시 API 26 이상을 요구한다는 안내다.* 그래서 플러터 타겟 SDK를 `API 26`으로 설정하면 Android X 마이그레이션을 한 경우 문제가 발생한다. `API 28 (9 / 파이)` 이상을 요구한다. 첩첩 산중이다. 결국 AndroidX 마이그레이션을 적용한다고 하면 **플러터의 targetSdkVersion은 `API 28`로 해주는 게 좋다.** 하위 기기 지원은 minSdkVersion을 통해 지정할 수 있는데 얼마만큼 문제없이 동작할지는 잘 모르겠다.  

---

**구글 플레이 스토어 관련 안내**  

> Google Play requires that apps target API level 26 or higher.
>
> Inspection info: As of the second half of 2018, Google paly requires that new apps and app updates target API level 26 or higher.
>
> Configuring your app to target a recent API level ensures that users benefit from significant security and performance improvements, whitle still allowing your app to run on older Android verions (down to the minSdkVersion).
>
> To update your targetSdkVersion, follow the steps from "Meeting Google Play requirements for target API level",
> https://developer.android.com/distribute/best-practices/develop/target-sdk.html  
>
> Issue Id: ExpiredTargetSdkVersion
>
> More info:
> https://support.google.com/googleplay/android-developer/answer/113469#targetsdk
> https://support.google.com/googleplay/android-developer/answer/113469#targetsdk
> https://developer.android.com/distribute/best-practices/developer/target-sdk.html


---

**AndroidX 란**  

> AndroidX는 Android 팀이 Jetpack 내에서 라이브러리를 개발, 테스트, 패키징, 버전 관리, 출시하는 데 사용하는 오픈소스 프로젝트입니다.  
> [AndroidX 개요](https://developer.android.com/jetpack/androidx)  
> [Android Jetpack](https://developer.android.com/jetpack?hl=ko)  
  
---

## 참고링크

안드로이드 버전 별 API 및 코드네임  
![https://upload.wikimedia.org/wikipedia/commons/thumb/6/66/2019.05.07%EA%B9%8C%EC%A7%80_%EC%88%98%EC%A7%91%EB%90%9C_%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C_%EC%A0%90%EC%9C%A0%EC%9C%A8.jpg/1920px-2019.05.07%EA%B9%8C%EC%A7%80_%EC%88%98%EC%A7%91%EB%90%9C_%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C_%EC%A0%90%EC%9C%A0%EC%9C%A8.jpg](https://upload.wikimedia.org/wikipedia/commons/thumb/6/66/2019.05.07%EA%B9%8C%EC%A7%80_%EC%88%98%EC%A7%91%EB%90%9C_%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C_%EC%A0%90%EC%9C%A0%EC%9C%A8.jpg/1920px-2019.05.07%EA%B9%8C%EC%A7%80_%EC%88%98%EC%A7%91%EB%90%9C_%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C_%EC%A0%90%EC%9C%A0%EC%9C%A8.jpg)

[https://source.android.com/setup/start/build-numbers](https://source.android.com/setup/start/build-numbers)  
