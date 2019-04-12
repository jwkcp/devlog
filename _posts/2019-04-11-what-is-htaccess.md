---
layout: post
title: .htaccess 파일은 도대체 뭔가요?
tag: webserver
comments: true
---

워드프레스를 설치한다던지 웹서버로 뭔가 할 때 .htaccess 파일을 자주 보게 된다. 이 파일의 역할은 도대체 뭘까?   
    
### .htaccess의 역할
htaccess는 "hypertext access"의 약자다. 파일명 앞의 점은 숨김 파일이란 뜻이다.(보통 시스템 파일 앞에 이렇게 점을 붙여 숨김 파일로 만든다.) 이 파일은 디렉토리에 대한 설정 옵션을 제공한다. 이게 무슨 말이냐면 아파치같은 웹서버에서 브라우저같은 클라이언트에서 접근할 때 어떤 식으로 서비스를 제공할 지 apache2.conf와 sites-enabled 가상 호스트 설정 파일을 통해 결정하는데, 이때 서비스할 파일이 위치한 곳의 디렉토리 경로를 지정하게 된다. 그리고 이 디렉토리에 접근을 허용할지 말지, 어떻게 보여줄지 등을 정하게 된다. sites-enabled에 있는 파일에서 ```<Directory>```블럭을 통해 이것을 지정하게 되는데 .htaccess 파일은 이것과 똑같은 역할을 한다. ```<Directory>```블럭을 통한 서버의 전역 설정이 있는 상태에서 .htaccess 파일을 사용하면 .htaccess가 위치한 디렉토리에 대한 허용 규칙을 오버라이드(덮어쓰게)하게 된다.
     
### .htaccess의 용도
허가, 인증, URL재작성, 스팸봇 차단, SSL, 디렉토리 리스팅, 에러 응답 커스터마이징 등

### 아파치 웹서버의 설정 탐색 순서
클라이언트가 웹서버에 접근하는 과정을 설정파일을 위주로 간단히 보면 이런 식이다. 1번부터 순서대로 보면 된다.

1. 웹브라우저(클라이언트)
2. apache2.conf 로드
3. apache2.conf에 포함된 include 구문으로 sites-enabled 로드
4. 서비스 디렉토리 권한 등 옵션 적용 후
5. 다시 웹브라우저에 결과 리턴
    
4번의 설정에 따라 서비스할 디렉토리에 접근이 차단된다든지 하는 결과가 나타난다. 그럼 ```<Directory>``` 블럭과 똑같은 역할을 뭐하러 만들었나? ```<Directory>```는 sites-enabled 설정 파일 안에 있는 항목 중 하나다. .htaccess 파일은 이 파일을 디렉토리에 딱 두면 그 디렉토리의 하위 디렉토리를 포함해 디렉토리 권한 설정이 적용된다. [아파치 공식 문서](https://httpd.apache.org/docs/2.2/en/howto/htaccess.html)에 따르면 꼭 필요한 경우는 모르겠지만 가급적 이 파일을 사용하지 않기를 권한다. 대신 ```<Directory>```항목 설정을 이용하면 된다. .htaccess 파일이 있는지 없는지 탐색하면서 속도가 느려질 뿐만 아니라 무분별하게 사용하면 소스가 있는 디렉토리에 접근 권한을 클라이언트에게 넘기는 위험한 일이 일어날 수 있기 때문이다.
    
### 그럼 .htaccess는 왜 쓰나?
그런데 이렇게만 쓰면 .htaccess는 꼭 기능은 있지만 없는거나 마찬가지로 치부되고 쓸모도 없는 프로그래밍 세계의 goto문 같은 느낌이다. 하지만 .htaccess도 장점과 필요한 경우가 있다. 일시적으로 어떤 요청에 기반하여 웹서버가 동작해야 하는 경우가 그렇다. ```<Directory>``` 설정은 웹서버를 재시작할 때만 적용된다.(느낌 오지요?) 권한 없는 사용자가 서버 전체에 접근하도록 하지 않고 일시적으로 최소한의 필요 권한만 주어서 요청 작업을 처리할 때도 .htaccess 파일은 유용하다.   
    
---

참고링크:    
* [https://httpd.apache.org/docs/2.2/en/howto/htaccess.html](https://httpd.apache.org/docs/2.2/en/howto/htaccess.html)
* [https://www.ostraining.com/blog/coding/what-are-htaccess-files/](https://www.ostraining.com/blog/coding/what-are-htaccess-files/)