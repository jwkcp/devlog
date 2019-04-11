---
layout: post
title: apt와 apt-get의 차이점. 둘 중에 뭘 써야 할까?
tag: wordpress
comments: true
---

APT는 Advanced Packaging Tool의 약자로 우분투와 같은 리눅스 배포판의 시초인 데비안의 패키지 시스템을 일컫는 말입니다. 이렇게 APT를 준수해 패키지를 설치하고 제거하는 등의 용도로 만들어진 툴들이 apt-get, aptitude, apt-cache 등이죠. 다른 여러 툴도 있었지만 얘네들이 제일 유명하고 인기있었습니다. 그런데 이렇게 툴이 여러 개 생기고 리눅스 유저들이 자주 쓰는 커맨드들을 이런 여러 툴에 흩어져있다 보니까 점점 문제가 생기기 시작한겁니다. 그 결과 apt-get이나 apt-cache 등에서 듣보잡 기능과 옵션들을 다 빼고 리눅스 유저들에게 필요한 기능만 사용하기 편리하게 쏙쏙 넣어 apt가 탄생했습니다. apt가 처음 나온 건 2014년이었지만 우분투 16.04가 런칭되면 2014년 부터 사람들에게 알려지기 시작했습니다.
     
간단한 예로 apt를 쓰면 설치 진행 상황을 프로그레스바로 보여주고, 업그레이드할 수 있는 패키지 숫자도 보여줍니다. apt-get은 별로 옵션 커맨드를 써야 가능했던 기능이죠. apt는 사람들이 자주 쓰고 사용자 친화적인 기능을 아예 기본으로 넣어버린 겁니다. apt 커맨드는 apt-get과 apt-cache에서 자주 쓰는 것을 그대로 쓰기 때문에 뭐 별도로 다시 외워야 하는 것도 거의 없습니다. apt list 같은 명령을 새로 추가되었으니까 알아두면 편리하겠네요.
      
뭘 써야 할지 잘모르겠다면 apt 를 쓰세요. 특별한 경우가 아니면 apt이 더 나을거에요.
     
*참고 링크: [https://itsfoss.com/apt-vs-apt-get-difference/](https://itsfoss.com/apt-vs-apt-get-difference/*)*