---
layout: post
title: PNU(필지고유번호) 구하는 공식
tags: pnu
comments: true
---

## 이슈

공공데이터포털 등에서 공시지가 조회 등을 하려고 하면 pnu라는 값을 넣어야 한다. pnu는 Parcel Number의 약자로 필지고유번호를 뜻한다. 쉽게 말해 땅의 주민번호라고 생각하면 된다. 그런데 공공데이터포털에도 이 pnu를 조회하는 API는 제공하지 않고 있다. (제가 찾아본 바로 그렇습니다. 혹시 있다면 다른 사용자에게도 큰 도움이 되니 댓글로 알려주시면 감사하겠습니다.)

---

## 방법

pnu는 크게 `행정구역코드` + `상세지번`으로 구성된다. 예를 들어 '충청북도 청주시 오창읍 구룡리 150-6번지'의 경우 아래와 같이 pnu코드가 구성된다.

4311425346101500006

이걸 좀 쪼개보면,

4311425346(행정구역코드) + 1(필지구분, 일반은 1 산은 2) + 0150(본번을 0000으로 패딩) + 0006(부번을 0000으로 패딩)

---

## 다운로드 링크

법정동코드는 행정안전부 사이트에서 내려받을 수 있다.  
[https://www.mois.go.kr/frt/bbs/type001/commonSelectBoardArticle.do?bbsId=BBSMSTR_000000000052&nttId=75252#none](https://www.mois.go.kr/frt/bbs/type001/commonSelectBoardArticle.do?bbsId=BBSMSTR_000000000052&nttId=75252#none)

행정안전부 > 업무안내 > 지방자치분권실 > 주민등록 및 인감 > 주민등록주소코드 변경내역 zip 다운로드 > KIKcd_B.xxxx.xlsx 다운로드
