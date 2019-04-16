---
layout: post
title: AWS 라이트세일(lightsail)에 차근 차근 워드프레스 설치하기
tag: wordpress
comments: true
---

아마존 라이트세일에서 인스턴스를 설치할 때 '비트나미(bitnami)'라는 곳에서 미리 패키징해 둔 워드프레스를 선택하면 클릭과 함께 바로 워드프레스를 사용할 수 있게 됩니다. 이게 가능한 건 비트나미에서 미리 필요할 것을 설치한 후 설정까지 해서 클릭만하면 사용할 수 있게 만들어 뒀기 때문입니다. 
    
**워드프레스를 돌릴 때 필요한 것들. 비트나미는 이런 것을 미리 설치한 패키지를 만들어 배포하고 있다.**
 * wordpress - php로 작성된 블로깅 플랫폼, 보통 CMS(Content Management System)이라고 부른다 
 * php - 프로그래밍 언어 중 하나인 php라는 것을 실행되게 해주는 런타임
 * mysql - 데이터베이스
 * apache - 웹서버
      
비트나미로 설치하는 것이 간편하지만 여러 설정파일이 비트나미에서 만든 구조로 되어 있어 이것이 싫거나, 순수 워드프레스를 깨끗하게 설치하고 싶은 사람들은 위 항목들을 직접 설치하기도 합니다. 여기서는 리눅스 운영체제인 우분트 16.04에 위 프로그램을 설치하여 워드프레스를 돌리는 과정을 소개하겠습니다.
     
**이 글에서 사용하는 환경**
* linux: 우분투 16.04
* apache: Apache/2.4.18
* php: 7.
     
---
    
# 준비
## 권장 사항
* PHP 7.3 또는 그 이상
* MySQL 5.6 또는 MariaDB 10 또는 그 이상
* 텍스트 에디터, 웹브라우저, FTP 클라이언트 소프트웨어 등

# 전체 과정
1. LAMP(Linux, Apache, MySQL, PHP) 설치
2. 데이터베이스를 준비합니다. (이 글에서는 MySQL을 다룹니다.)
3. 워드프레스 패키지를 다운로드 후 압축을 해제합니다.
4. wp-config-sample.php라는 파일을 복사한 후 wp-config.php로 이름을 바꾸고 파일을 열어 데이터베이스 정보를 입력합니다.
5. 웹서버에 압축 해제한 워드프레스 파일을 올립니다.
6. 워드프레스 설치 스크립트를 실행하고 웹 브라우저로 접속해 봅니다.

# 세부 과정
## 1. LAMP(Linux, Apache, MySQL, PHP) 설치
### Ubuntu 16.04를 준비합니다. 이건 알아서.. ^^
### Apache 웹서버 설치
```
# 패키지 업데이트 및 설치
$ sudo apt update
$ sudo apt install apache2

# 설정 테스트
$ sudo apache2ctl configtest

# 설정 테스트에서 에러가 나면 
$ sudo vi /etc/apache2/apache2.conf

# 맨 아래 아래 추가 후 저장
ServerName server_domain_or_IP

# 아파치 재시작
$ sudo systemctl restart apache2
```
     
방화벽 설정을 체크하고 "Apache Full"이 80, 443/tcp가 열려있는지 확인합니다. 열려있지 않다면 ```sudo ufw allow in "Apache Full"``` 명령으로 열어줍니다.   
```
# 현재 설정 보기
$ sudo ufw status verbose

# 허용 프로그램 목록
$ sudo ufw app list
    
# "Apache Full" 세부 사항
$ sudo ufw app info "Apache Full"
```
    
이제 ```http://your_server_IP_address```를 브라우저에 입력해 제대로 아파치 웹서버가 도는지 확인합니다.


### MySQL 데이터베이스 설치
```
# 데이터베이스 설치
$ sudo apt install mysql-server

# 보안 취약점 제거 스크립트 실행. 비밀번호 보안 검사, 테스트DB, User 삭제, root 원격 로그인 비활성화 등 선택 후 즉시 반영
$ mysql_secure_installation
```
     
### PHP 설치
```
# 로케일 설정을 POSIX(C) UTF-8로 변경한다. 로케일 설정이 없거나 POSIX(C) 표준으로 되어 있지 않으면 에러가 발생하는 명령이 있을 수 있다.
$ LC_ALL=C.UTF-8    
    
# PHP 7.3 설치를 위한 ppa 저장소 등록
# 이 ppa를 등록하지 않고 그냥 php를 설치하면 7.0 버전이 설치되는데 워드프레스 권장 버전이 7.3이다. 7.0도 가능하긴 하나 7.3으로 하길 권장한다.   
$ sudo add-apt-repository ppa:ondrej/php

# 아래 명령으로 추가된 저장소 관련 내용을 업데이트합니다.
$ sudo apt udpate

# PHP와 관련 라이브러리 설치
$ sudo apt install php7.3 php7.3-cli php7.3-common libapache2-mod-php php-mcrypt php-mysql

# 워드프레스에서 필요한 PHP 확장 프로그램 설치
$ sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc

# 아파치 웹서버에서 php를 먼저 읽도록 설정 수정 (index.php를 제일 첫번째 순서로 옮겨주세요.)
$ sudo vi /etc/apache2/mods-enabled/dir.conf

# 아파치 재시작 및 상태 확인
$ sudo systemctl restart apache2
$ sudo systemctl status apache2
```
     
그 다음, /var/www/html에 ```info.php```라는 파일을 만들고 ```<?php phpinfo(); ?>```라고 입력 후 저장한다. 웹브라우저에서 ```http://your_server_IP_address/info.php```하면 php버전이 노출되고 7.3이 보이면 성공이다.
       
---
     
## 2. 데이터베이스를 준비합니다. 
```mysql -u root -p```로 데이터베이스 접속합니다. 비밀번호는 위에서 mysql 설치할 때 입력한 것을 넣으면 됩니다.
     
```
# 데이터베이스 생성
CREATE DATABASE 데이터베이스명 DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;

# 위에서 생성한 데이터베이스를 사용할 사용자 생성
GRANT ALL ON 데이터베이스명.* to '생성할사용자명'@'localhost' IDENTIFIED BY '생성할비밀번호*!';

# 권한 적용
FLUSH PRIVILEGES;

# Mysql 화면에서 나가기
EXIT;
```


    
## 3. 워드프레스 패키지를 다운로드 후 압축을 해제합니다.
```wget https://wordpress.org/latest.tar.gz```으로 워드프레스 파일을 내려받고 ```tar -xzvf latest.tar.gz```로 압축을 풀어줍니다.


## 4. wp-config-sample.php라는 파일을 복사한 후 wp-config.php로 이름을 바꾸고 파일을 열어 데이터베이스 정보를 입력합니다.
## 5. 웹서버에 압축 해제한 워드프레스 파일을 올립니다.
## 6. 워드프레스 설치 스크립트를 실행하고 웹 브라우저로 접속해 봅니다.



*참고링크*    
* [How To Install WordPress with LAMP on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lamp-on-ubuntu-16-04)
* [How To Install Linux, Apache, MySQL, PHP (LAMP) stack on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-16-04)
