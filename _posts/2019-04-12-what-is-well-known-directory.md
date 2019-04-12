---
layout: post
title: .well-known 디렉토리는 도대체 뭔가요?
tag: webserver
comments: true
---

```.well-known``` 디렉토리를 아시나요? 외국 커뮤니티에서도 이 디렉토리를 처음 본 사람들은 "나는 만든 적이 없는데 뭔가 수상한데, 해킹 이런거 아니야?" 이렇게 질문하는 것을 자주 볼 수 있습니다. 보통 숨김 파일은 시스템과 관련된 파일일 경우가 많은데 이 디렉토리는 도데체 어떤 역할을 할까요?      
     
```.well-known``` 디렉토리는 [RFC5785](https://tools.ietf.org/html/rfc5785)에 그 의미가 설명되어 있습니다. [여기](https://tools.ietf.org/html/rfc5785)를 방문해서 잠깐 훓어보세요. 유심히 봐야 할 부분은 아래와 같습니다.    
    
> This memo defines a path prefix for "well-known locations", "/.well-known/", in selected Uniform Resource Identifier (URI) schemes.          
     
해석해보면,    
> /.well-known/는 정해진 URI 스키마 상에서 잘 알려진 위치들(well-known locations)의 경로 프리픽스를 정의해 줍니다.
     
뭔 말인가 싶지만 곰곰히 들어다보면 그 의미가 보이는 것 같습니다.      
우리가 웹서버를 통해 뭔가를 서비스할 때 습관처럼 항상 해야하거나 제공해야하는 정보가 있습니다. 검색엔진 봇을 제어하는 robots.txt도 그렇고, 파비콘 favicon.ico나 favicon.jpg 도 그렇습니다. 사이트를 작성한 사람이 누군지 나타내는 [humans.txt](http://humanstxt.org/)도 있구요. 보안 취약점이 발견되었을 때 연락할 수 있도록 제공하는 [security.txt](https://securitytxt.org/) 파일도 우리가 웹서비스에서 흔히 제공하는 정보입니다. 이런 파일을 어디에 둬야 할까요? RFC5785는 이런 리소스를 .well-known 디렉토리에 두기를 제안하고 있습니다.   
     
몇 가지 예를 더 들어볼까요?    
iOS 앱을 개발할 때 유니버셜 링크를 쓸 때도 ```.well-known``` 디렉토리에 [apple-app-site-association](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html)을 넣고요. 안드로이드에서는 웹사이트에 공개, 인증관련 내용을 기록하는 [디지털 에셋 링크](https://developers.google.com/digital-asset-links/v1/getting-started)도 ```.well-known``` 디렉토리에 assetlinks.json을 넣습니다. 이렇게 보니 그냥 누가 아무 디렉토리 이름이 만들어서 막 쓰는게 아니라, 공식적으로 널리 애용(?)되고 있다는 걸 아시겠지요?    
     
참 하나 중요한 예를 빼먹었네요. 무료 SSL인증서를 발급하게 해주는 고마운 서비스 Let's Encrypt도 이 폴더를 이용합니다. Let's Encrypt를 통해 SSL 인증서를 발급받는 과정에서 .well-known/acme-challenge 폴더가 생성되지요.

---

**함께보면 좋은 글:**   
* [SSL 인증서는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-ssl/)
* [mod_rewrite는 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/12/what-is-mod-rewrite/)
* [.htaccess 파일은 도대체 뭔가요?](https://devlog.jwgo.kr/2019/04/11/what-is-htaccess/)
