---
layout: post
title: 아파치(Apache) 웹서버 conf 파일은 어떤 구조를 가지고 무슨 일을 할까?
tag: apache
comments: true
---

아파치 설정은 꽤 빈번히 만지게 되지만 그 구조와 역할에 대해 그냥 지나치는 경우가 많은 것 같습니다. 아래는 아파치(Apache) 웹서버 설정 파일들이 대략적으로 어떤 구조와 역할을 하는지 아피치 공식 문서의 설명을 번역한 것에 몇 가지 첨언을 더한 것입니다.   
          
## 데비안(Debian)에서 아파치2 설정 파일은 어떻게 동작할까요?
데비안(Debian)은 우분투(Ubuntu)의 부모뻘 되는 운영체제입니다. 그래서 우분투를 데비안 계열 운영체제로 나누기도 합니다. ```/etc/apache2/apache2.conf``` 파일은 이 데비안 계열에서 아파치 웹서버의 메인 설정 파일입니다. 데비안 계열에서 동작하는 아파치2 웹서버 설정은 주로 사용되는 웹서버 설정과 꽤 다른 모습을 보여주는데, 그 이유는 데비안에 기본적으로 설치되어 있는 아파치2가 자동화와 서버 관리를 가능한 쉽게 하기 위한 모듈의 추가/제거, 가상 호스트(Virtual hosts)와 기타 설정 등을 하기 때문입니다.    
    
아파치2 설정 파일이 위치한 ```/etc/apache2/``` 디렉토리에는 아래와 같이 여러 설정 파일이 들어있고 대략 이런 구조를 가지고 있습니다.   
   
```
/etc/apache2/
|-- apache2.conf
|       `--  ports.conf
|-- mods-enabled
|       |-- *.load
|       `-- *.conf
|-- conf-enabled
|       `-- *.conf
`-- sites-enabled
        `-- *.conf
```
     
## apache2.conf
웹서버를 시작할 때 필요한 모든 설정 파일 조각들이 여기에 포함되어 돌아갑니다.
    
## ports.conf
들어오는 연결을 리스닝(listening)하는 포트를 정합니다. IfModule은 뒤에 인자로 나오는 모듈이 있다면 실행하게 하는 구문입니다.
```
<IfModule ssl_module>
    Listen 443
</IfModule>
```
    
## mods-enabled, conf-enabled, sites-enabled
이 디렉토리들은 각각 모듈 관리, 전역(global) 설정 값, 가상 호스트 설정 조각들을 포함하고 있습니다. 이 디렉토리들은 각각 *-available/ 로 끝나는 디렉토리의 설정 파일에서 심볼링 링크 연결을 통해 활성화됩니다. 이 설정은 ```a2enmod/a2dismod```, ```a2ensite/a2dissite``` 그리고 ```a2enconf/a2disconf``` 라는 헬퍼를 통해 관리하는게 좋습니다. 
     
## apache2
apache2라고 불리는 바이너리 파일은 기본 환경 설정 위해서 환경설정변수(environment variables)을 이용하기 때문에 단순히 ```/usr/bin/apache2```의 바이너리 파일을 호출해서는 동작하지 않고 ```/etc/init.d/apache2```나 ```apache2ctl```로 동작하도록 되어 있습니다.
      
---
   
