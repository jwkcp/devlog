---
layout: post
title: 텐서플로우 vs 파이토치
tags: ai tensorflow pytorch
comments: true
---

이 글은 [The Batch](https://www.deeplearning.ai/thebatch/) 중 OCT 17, 2019 ISSUE 25의 내용을 요약한 것입니다.

\_\_
텐서플로우(Tensorflow)와 파이토치(Pytorch)는 대표적인 딥러닝 프레임워크입니다. 굉장히 많은 애플리케이션이 이 두 프레임워크를 사용해 돌고 있습니다. 그리고 텐서플로우가 낫냐, 파이토치가 낫냐 하는 논쟁은 끊임없이 있어 왔습니다. 그리고 팽팽하게 맞서고 있죠. deeplearning.ai가 발행하는 THE BATCH는 이런 논쟁과 관련해 코넬 대학의 한 연구자가 지난 일년 간 둘을 비교한 연구 결과를 [The Gradient](https://thegradient.pub/state-of-ml-frameworks-2019-pytorch-dominates-research-tensorflow-dominates-industry/?utm_campaign=The%20Batch&utm_source=hs_email&utm_medium=email&utm_content=78158122&_hsenc=p2ANqtz-9GDBQu9IYaEF9f2mGKwp-7RJvHsEAyIkFLWIM-8EXlGvmNkugfoArnJ_bAAGN6FFBXrSQBq-jyZICEbiXCSjm1ZcQ28A&_hsmi=78158122)를 통해 공유했습니다.

## 결과

파이토치는 리서치 커뮤니티 사이에서 빠르게 성장하고 있는 반면 텐서플로우는 실제 산업 현장에서 주로 쓰이는 경향이 있다는 겁니다.

-   2018년 TOP 5 AI 컨퍼런스에 제출된 초록을 분석하니 **파이토치** 사용자는 275% 증가, 텐서플로우 사용자는 0.5% 감소
-   3,000개의 구인구직 포스팅 분석 결과 **텐서플로우**가 압도적으로 많음
-   링크드인을 통한 설문에서도 3,230 대 1,200로 **텐서플로우** 압승
-   깃허브 스타를 받은 저장소 비율도 **텐서플로우**가 압도적으로 많음

## 상대적 비교 우위

-   **텐서플로우**는 크고 탄탄한 사용자 베이스를 가지고 있고 이 사실은 업계 특성 상 잘 바뀌지 않음.
-   **텐서플로우**는 파이토치보다 훨씬 효율적임. 모델 실행 시간을 줄이면 기업 수익성에 도움이 되니 선호함.
-   **파이토치**는 파이썬하고 찰떡궁합이며 코드를 쓰기 쉽고 디버깅도 쉬움
-   많은 연구자들이 2016년 이후 거의 변화가 없이 일관성을 유지하고 있는 **파이토치**의 API를 선호함.

## 결론

새로 출시된 **텐서플로우 2.0**은 **파이토치**사용자가 애정하는 파이썬 통합과 Eager 모드를 기본값으로 하는 등 많은 장점들을 흡수했음. 그러나 딥러닝 세계는 많은 연구자들이 이끌어가고 있다는 점을 잊으면 안됨. 이들은 **파이토치**를 주로 사용하기 때문에 그 밑에서 배우는 학생들도 **파이토치**에 자연스럽게 익숙해지게 되고 이들이 직업을 구하기 위해 구인구직 시장에 들어가기 시작하면 판도가 변할 수도 있음.

\_\_
_원문링크: [The Batch](https://www.deeplearning.ai/thebatch/) 중 OCT 17, 2019 ISSUE 25_
