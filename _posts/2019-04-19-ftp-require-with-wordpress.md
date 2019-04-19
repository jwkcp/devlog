---
layout: post
title: 워드프레스 설치 후 업데이트나 플러그인 설치 시 ftp 정보 입력 창이 나오는 경우
tags: wordpress
comments: true
---
    
### 증상
AWS lightsail이나 기타 호스팅 서비스에 워드프레스를 직접 수동으로 설치 시 워드프레스 버전 업데이트, 플러그인 설치와 같은 작업을 할 때 ftp 계정 정보를 넣으라는 창이 뜰 때가 있습니다.   
    
### 원인
워드프레스가 설치된 디렉토리에 아파치 사용자(보통 기본값이면 우분투에서는 www-data)가 쓰기 권한이 없기 때문입니다.    
    
워드프레스 버전 업데이트, 플러그인 설치 등은 워드프레스가 설치된 디렉토리의 쓰기 권한이 필요한 작업입니다. 그런데 이 디렉토리를 생성한 사용자와 워드프레스를 돌리는 사용자, 즉 아파치를 실행한 사용자가 다른기 때문에 이 상태로는 워드프레스 업데이트 파일이나 플러그인 파일 등을 내려받는게(쓰기 권한) 불가능하니 이게 가능한 계정 정보를 넣음으로써 파일을 다운로드하여 해당 작업을 마무리하겠다는 것입니다.  
     
조금 쉽게 예를 들어 보겠습니다. AWS lightsail에서 우분투를 선택해 설치하면 기본적으로 ```ubuntu```라는 사용자로 로그인하게 됩니다. 이 사용자로 로그인한 상태에서 wordpress.tar.gz를 다운로드하여 압축을 풀고 wordpress 디렉토리를 만들었다면 이 디렉토리는 사용자명이 ```ubuntu```이고 그룹명이 ```ubuntu```인 그룹에 속합니다. 자, 이제 아파치를 설치하고 ```ps -ef | grep apache2``` 명령으로 아파치를 누가 실행했는지 봅시다. ```www-data```라는 사용자일 겁니다. 그리고 이 사용자는 ```www-data``` 그룹에 속해있습니다. 사용자가 다르고 디렉토리 소유권이 다른 것이지요.       
     
다시 처음으로 돌아가서, 워드프레스 주소를 웹브라우저에 입력해 웹페이지를 띄우고 워드프레스를 업데이트하거나 플러그인을 설치하는 것은, 서버의 ```www-data```라는 사용자가 실행한 아파치 웹서버에게 '워드프레스가 설치된 디렉토리'위치에 파일을 저장해야 한다는 것을 말합니다. 그런데 이 '워드프레스가 설치된 디렉토리'의 소유자는 ```ubuntu```라는 사용자입니다. 공책에 그림을 그리려고 하는데 공책 주인이 다른 사람인 셈이지요. 그래서 워드프레스는 파일 저장이 가능한 ftp 계정을 요구하는 것입니다.   
     
### 해결 방법
여러가지 해결 방법이 있습니다. 원인이 디렉토리 소유자와 아파치 실행자가 다름으로 인한 권한문제라는 것을 생각해보면 아파치 실행자를 바꿀 수도 있고, 워드프레스 설치 디렉토리 소유자를 바꿀 수도 있습니다. ftp 계정 정보를 워드프레스 팝업 창에 넣을 수도 있지요. 아래는 그 여러 방법 중에서 제가 선택한 방법입니다. 워드프레스 설치 디렉토리의 소유자를 아파치 실행자인 ```www-data```로 바꿔줘서 파일 저장이 가능하게 만들어 주는 것이지요.    

---

워드프레스가 /home/ubuntu/wp 에 설치되어 있다고 가정하고 하나씩 살펴보겠습니다.    
    
먼저 각 사용자가 어떤 그룹에 속해있는지 봅시다.     
```
# 현재 ubuntu 사용자의 소속 그룹 보기
$ id ubuntu

# 현재 www-data 사용자의 소속 그룹 보기
$ id www-data

# 그룹 목록 보기
$ groups

# 모든 사용자 보기
$ compgen -u

# 모든 그룹 보기
$ compgen -g
```
     
그리고 워드프레스 디렉토 소유자는 누구인지 봅시다.
```
$ ls -al /home/ubuntu
```
      
아파치는 누가 실행했나 봅시다.
```
$ ps -ef | grep apache2
```
    
**이게 워드프레스 설치 디렉토리의 소유자를 바꿀 차례입니다.**
```
$ sudo chown -R www-data:www-data /home/ubuntu/wp
```
이렇게 하면 ubuntu:ubuntu로 되어 있던 사용자:그룹이 www-data:www-data로 바뀝니다. -R 옵션은 재귀적(Recursive)하게 적용하라는 뜻으로 디렉토리 안에 디렉토리가 여럿 들어 있을 수 있으므로 전부 다 돌면서 소유자 변경을 하라는 뜻입니다.    
     
다 되었습니다. 이렇게 하고 웹브라우저에서 다시 접속해 봅시다. 이제 ftp 계정 입력 창이 나타나지 않습니다. 워드프레스 디렉토리 소유자와 아파치 웹서버 사용자가 같기 때문에 쓰기 권한이 생긴 것이지요.   

---

### 하나 더!
이렇게 하면 반대로 나의 기본 로그인 사용자인 ```ubuntu``` 사용자는 ```www-data``` 소유인 워드프레스 디렉토리에 접근하고 파일 쓰고 하는게 안되게 됩니다. 위에 과정을 이해했다면 당연한 것이지요. 설상가상으로 ```www-data```사용자는 아파치 사용자로 로그인이 안되는 계정입니다. ;; 어떻게 해야 할까요?   
    
이 역시 여러가지 방법이 있지만 저의 경우는 ```ubuntu```사용자의 보조 그룹에 ```www-data```그룹을 추가해줬습니다. 그리고 워드프레스의 권한 설정을 그룹까지 가능하도록 수정해 주었습니다. 이렇게 되면 ```ubuntu```사용자는 ```www-data```그룹의 구성원이기도 하기 때문에 워드프레스 설치 디렉토리에 접근하고 쓰는 것에 아무런 문제가 없게 됩니다. 아래는 그룹을 추가하고 권한을 설정하는 명령입니다.   

```www-data``` 보조 그룹에 ```ubuntu``` 사용자를 추가하라는 명령입니다. 옵션 설명은 아래를 참고하세요.     
```
$ sudo usermod -a -G www-data ubuntu
```
     
워드프레스 디렉토리 권한 설정. chmod는 r(읽기 4), w(쓰기 2), x(실행 1)의 숫자를 더하여 권한을 설정합니다. 아래는 자기 자신을 전부(r+w+x) 해당 그룹에 속한 사용자도 전부(r+w+x) 그 외 사용자는 (r)이라고 지정한 것입니다. 왜 그룹 사용자에게 디렉토리 실행(x) 권한도 주었나 싶을텐데, 디렉토리의 실행 권한은 해당 디렉토리를 여는 권한입니다. 디렉토리 실행 권한이 없으면 ```cd /home/ubuntu/wp``` 명령으로 디렉토리 안으로 이동할 수 없게 됩니다.   
```
$ sudo chmod 774 /home/ubuntu/wp
```

---

```
Usage: usermod [options] LOGIN

Options:
  -c, --comment COMMENT         new value of the GECOS field
  -d, --home HOME_DIR           new home directory for the user account
  -e, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -f, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -g, --gid GROUP               force use GROUP as new primary group
  -G, --groups GROUPS           new list of supplementary GROUPS
  -a, --append                  append the user to the supplemental GROUPS
                                mentioned by the -G option without removing
                                him/her from other groups
  -h, --help                    display this help message and exit
  -l, --login NEW_LOGIN         new value of the login name
  -L, --lock                    lock the user account
  -m, --move-home               move contents of the home directory to the
                                new location (use only with -d)
  -o, --non-unique              allow using duplicate (non-unique) UID
  -p, --password PASSWORD       use encrypted password for the new password
  -R, --root CHROOT_DIR         directory to chroot into
  -s, --shell SHELL             new login shell for the user account
  -u, --uid UID                 new UID for the user account
  -U, --unlock                  unlock the user account
  -v, --add-subuids FIRST-LAST  add range of subordinate uids
  -V, --del-subuids FIRST-LAST  remove range of subordinate uids
  -w, --add-subgids FIRST-LAST  add range of subordinate gids
  -W, --del-subgids FIRST-LAST  remove range of subordinate gids
  -Z, --selinux-user SEUSER     new SELinux user mapping for the user account
```