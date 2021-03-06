---
layout: post
title: 맥북프로 사용 시 듀얼 모니터를 쓰기로 결정하기 전 반드시 알아야 할 사항
tags: mac tip
comments: true
---

---

맥북프로는 더 나은 그래픽 성능을 위해 그래픽 카드가 2개 달려있다. 무난한 작업에서는 내장 그래픽을, 고성능이 필요한 작업에서는 외장 그래픽을 쓴다. 그런데 듀얼 모니터를 쓰기 위해 별도의 모니터를 연결하면 맥북프로는 외장 그래픽 카드를 활성화시킨다. 이 때문에 펜소음과 발열이 발생하고 배터리 소모가 급격히 늘어난다. 배터리 소모는 충분히 예상 가능하고 어댑터를 사용하는 환경을 고려하면 무시할 만 하다. 펜소음 역시 그럭저럭 참아줄만하다고 생각한다. 하지만 터치바 위쪽으로 발생하는 발열은 상상 이상이다. 맥북프로의 수명이 줄어들지 않을까 하는 생각이 수시로 든다.

터치바가 있는 맥북프로에 외장 모니터를 연결해 사용하려면 모니터는 당연하고 [USB-C Digital AV Multiport 어댑터](https://www.apple.com/kr/shop/product/MJ1K2FE/A/usb-c-digital-av-multiport-%EC%96%B4%EB%8C%91%ED%84%B0)와 같은 usb-c to hdmi 같은 케이블이 필요하다. 정품 커텍터는 애플 사이트에서 8.9만원이다.  

맥에서 활성 그래픽 카드를 선택할 수 있도록 도와주는 [gfxCardStatus](https://github.com/steveschow/gfxCardStatus)라는 프로그램도 있지만 외장 모니터를 연결했을 때는 강제로 내장 모니터 전환이 불가능하다.  

듀얼 모니터 환경을 구축하고 싶은 분은 이런 단점을 충분히 고려해 결정하자.  

**맥북프로에 외장 모니터를 연결해 사용할 경우 단점**   

1. 터치바 위쪽으로 발생하는 상당한 발열이 사용 내내 지속된다.
2. 평소에 거의 못 느끼는 펜소음을 자주 느낄 수 있다.
3. 배터리 소모가 심하다.

---

### Update: 2018.05.09  
아무래도 아쉬워서 다시 외부 모니터를 연결하고 동일한 발열이 발생하는지 확인해봤다. 관련 모니터링 및 제어 프로그램으로 2가지 프로그램을 사용했다.

[Turbo Boost Switcher for OS X](https://www.rugarciap.com/turbo-boost-switcher-for-os-x/)라는 것이 있는데 개인적으로 일반 버전을 사용해봤는데 큰 효과? 도움?는 받지 못했지만 나름 알려진 프로그램이다. 일반 버전과 Pro버전이 있고 Pro버전만 세부 조작 및 편의 기능 설정이 가능하다.  

[iStat Menus](https://bjango.com/mac/istatmenus/) 역시 유명한 프로그램이다. [BACK TO THE MAC](http://macnews.tistory.com/5578)에서도 리뷰한 바 있다. [Turbo Boost Switcher for OS X](https://www.rugarciap.com/turbo-boost-switcher-for-os-x/)보다 더 다양한 정보를 제공한다. 맥OS 상단바에 상태가 나타나며 선택할 수 있고 '센서' 부분에서 온도를 확인할 수 있다. 현재 약 3시간 가까이 외장 모니터를 연결하고 IDE와 터미널, 크롬 탭 20여개를 열어둔 상태에서 약 55~60도를 가리킨다. 맥북 터치바 상단 패널을 만졌을 때는 여전히 발열이 있었다. 외부 모니터를 연결하면 그렇지 않을 때 보다 확실히 체감 발열이 심하긴 하다. 그러나 숫자로 나타나는 온도를 보니 크게 우려할만한 수준은 아니겠다는 생각이다. 펜소음은 거의 없는 것 같다. 얼마간 이 상태로 사용해봐야겠다. 처음 외부 모니터를 연결하고 맥북에서 발열이 발생하는 증상을 처음 겪어봐서 우려가 더 크지 않았나 싶다.
