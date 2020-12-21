---
layout: post
title: AWS S3 cors 설정 시 CORSRules to be an Array 에러가 발생하는 경우
tags: aws s3
comments: true
---

# 문제

aws s3 cors 설정 시 아래와 같은 에러가 발생한다.  

`Expected params.CORSConfiguration.CORSRules to be an Array`  

---

# 원인

아마 `CORSConfiguration`로 둘러싸인 설정을 입력했을 것이다. 이건 XML문법인데 JSON 문법으로 입력하면 에러가 발생하지 않는다. (아마존도 XML, JSON 타입 둘 다 설명하면서 왜 이런 에러가 나는지는 잘 모르겠다.)  

---

# 해결방법

JSON형식으로 설정을 입력한다. XML과 JSON의 동일 설정은 대략 아래와 같고 [이 페이지](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html)를 참고하자.  

**XML버전**  
{% highlight xml %}
<CORSConfiguration>
 <CORSRule>
   <AllowedOrigin>http://www.example.com</AllowedOrigin>
   <AllowedMethod>PUT</AllowedMethod>
   <AllowedMethod>POST</AllowedMethod>
   <AllowedMethod>DELETE</AllowedMethod>
   <AllowedHeader>*</AllowedHeader>
  <MaxAgeSeconds>3000</MaxAgeSeconds>
  <ExposeHeader>x-amz-server-side-encryption</ExposeHeader>
  <ExposeHeader>x-amz-request-id</ExposeHeader>
  <ExposeHeader>x-amz-id-2</ExposeHeader>
 </CORSRule>
</CORSConfiguration>
{% endhighlight %}

**JSON버전**  
{% highlight json %}
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "http://www.example.com"
        ],
        "ExposeHeaders": [
            "x-amz-server-side-encryption",
            "x-amz-request-id",
            "x-amz-id-2"
        ],
        "MaxAgeSeconds": 3000
    }
]
{% endhighlight %}

