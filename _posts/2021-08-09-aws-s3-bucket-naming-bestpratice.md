---
layout: post
title: AWS S3 버킷 이름 짓는 방법
tags: aws s3
comments: true
---

# 상황

AWS S3 버킷 이름을 짓다 보면 언더스코어를 써야할지, 하이픈을 써야할지, Camel case가 나을지, Snake case가 나을지 고민이 됩니다.  

---

# 권장방법

AWS 문서를 보면 아래와 같은 방법을 권장하고 있습니다.  
  
### 권장:
- docexamplebucket1
- log-delivery-march-2020
- my-hosted-content

### 정적 호스팅 용도 외 비권장
- docexamplewebsite.com
- www.docexamplewebsite.com
- my.example.s3.bucket

### 금지
- doc_example_bucket (언더스코어 사용)
- DocExampleBucket (대문자 포함)
- doc-example-bucket- (하이픈으로 끝남)

---

참고링크: [https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html)
