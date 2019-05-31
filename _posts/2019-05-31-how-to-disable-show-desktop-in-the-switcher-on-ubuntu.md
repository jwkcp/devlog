---
layout: post
title: 나를 환장하게 만드는 우분투 Alt+Tab '데스크톱 보이기' 비활성화하기
tags: ubuntu
comments: true
---
     
## 문제
우분투(이 글에서는 16.04 기준)에서 Alt+Tab을 해보면 '데스크톱 보이기'라는 스위처 메뉴가 있다. 아무리 조심하려고 해도 가끔 실수로 알트탭을 하다가 실수로 이게 선택되면 화면에 창 배치해둔 것이 다 최소화된다. 윈도우에서는 이 기능이 Ctrl+D 로 동작하는데 그나마 토글(Toggle) 방식이라 다시 Ctrl+D를 누르면 모든 창이 원상복귀된다. 하지만 우분투는 그런 거 없다. 다시 모든 창을 일일이 눌러 원위치시켜야 한다. 아 짜증나.. ㅋ
  
---
   
## 해결방법
기본적으로 우분투 설정에는 스위처(Switcher)에서 이 기능을 비활성하는 방법이 없다. 아래의 방법으로 '컴파즈 설정 관리자(CompizConfig Settings Manager)'를 설치하고 이 기능을 꺼보자.    
     
### 설치
```
# 설치할 수 있도록 저장소를 등록해주고
sudo apt-add-repository ppa:izx/askubuntu

# 업데이트
sudo apt update

# 설치
sudo apt install unity
```

---
     
### 비활성화하기
1. 설치된 목록 검색(혹은 윈도우키를 눌러) compiz라고 처보면 '컴파즈 설정 관리자(CompizConfig Settings Manager)가 보일 것이다.      
     
2. 선택하고 실행하면 왼쪽에 '필터' '분류' '기본설정' '고급검색' 메뉴바와 함께 오른쪽에 선택된 항목에 대한 내용이 나온다. 탐색기와 비슷하게 생겼다.     
      
3. 여기서 '분류' > '모두' 를 선택한다.     
       
4. 이후 오른쪽 패널에서 '데스크톱' 항목에 'Ubuntu Unity Plugin'을 선택한다. 그러면 여러 탭을 가진 상세 내용이 나오는데 이중에 'Switcher'라는 탭을 선택한다.    
     
5. 그리고 아래쪽을 살펴보면 'Disable Show Desktop in the Switcher'라는 항목이 있다. 이걸 체크해주면 된다.        
끝! 아 좋다! ^^
     

     