---
layout: post
title: 도커(Docker)에서 컨테이너 삭제 시, Postgresql의 볼륨이 유지되지 않고 삭제되는 경우 문제와 해결방법
tags: docker postgresql
comments: true
---

# 문제

도커에서 Postgresql을 가져다 실행(`docker run -dp 5432:5432 --name mydb -e POSTGRES_PASSWORD=1234 postgres`)하면 수 초만에 서비스가 구동됩니다. 사용자도 만들고 디비도 만들고 테이블도 만들고 해두었는데 컨테이너를 삭제(`docker rm -f mydb`)하고 다시 생성하면 지금까지 했던 모든 작업이 다 초기화 되어 있습니다. @.@   

이런 문제를 겪지 않기 위해 볼륨(Volume)을 이용해 Postgresql의 데이터를 유지할 수 있습니다. 볼륨은 도커 내 관리되는 파일인 네임드 볼륨(named volume)이 있고, 호스트(내 컴퓨터)의 디렉토리와 1:1 매핑하는 바인드 볼륨(bind volume)이 있습니다.   

그래서 아래와 같이 run 명령어를 돌렸습니다. 그런데 네임드 볼륨, 바인드 볼륨 어떤 방법을 써도 볼륨이 생성되어 데이터가 유지되지 않고 컨테이너를 삭제 후 재생성하면 모든게 다 날아가 있습니다. 왜 그런걸까요?  

**오류 명령 예시**  
`docker run -dp 5432:5432 --name mydb -e POSTGRES_PASSWORD=1234 -v pgdata:/var/lib/postgres/data postgres`  

이 명령에는 문제가 있습니다. 한번 찾아보세요.  

---

# 원인 

원인은 볼륨의 경로에 있습니다. 잘 보시면 `/var/lib/postgres/data`로 되어 있는데 `postgres`는 오타입니다. `postgresql`이 맞습니다. 이 오타 때문에 정상적으로 경로를 찾지 못해 우리가 원하는 볼륨 유지가 안되고 컨테이터 재생성 시 마다 초기화되었던 것입니다.  

postgresql은 기본적으로 도커로 올리면 자체 볼륨을 생성합니다. `docker volume ls`로 해당 목록을 볼 수 있습니다. 그러나 사용자가 `-v`나 `--volume` 옵션을 주고 구동했을 경우는 예외입니다. 이 경우는 자체 볼륨을 만들지 않고 사용자의 볼륨을 사용합니다. 위에서 처럼 경로가 올바르지 않은 등 볼륨 설정에 문제가 있으면 postgresql은 자체 볼륨을 사용합니다.  

이런 문제가 자신에게 있고 같은 문제인지 확신이 들지 않는다면 `docker inspect 컨테이너ID 또는 이름`으로 컨테이너를 살펴보세요. 위 예의 경우 `--name`으로 `mydb`라는 이름을 주었으므로 `docker inspect mydb`하면 됩니다. 그러면 중간에 `Mounts`라는 항목을 찾을 수 있는데 여기에 2개의 볼륨이 들어 있을 것입니다. 하나는 내가 설정한 볼륨, 또 하나는 postgresql이 만든 자체 볼륨. 정상적으로 내가 설정한 볼륨이 마운팅 되었다면 여기에 내가 설정한 1개의 볼륨만 보여야 합니다. 에를 들어, 이런 식입니다.  

{% highlight python %}
"Mounts": [
    {
        "Type": "bind",
        "Source": "/Users/me/apps/todo_app/pgdata",
        "Destination": "/var/lib/postgresql/data",
        "Mode": "",
        "RW": true,
        "Propagation": "rprivate"
    }
],
{% endhighlight %}

---

# 해결방법

해결방법은 위에서 이미 말씀드렸습니다. 올바른 볼륨 설정, 특히 올바른 postgresql의 경로를 입력하는 겁니다. 이게 빨리 치다보면 철자도 좀 오타가 나기 쉽고 postgres와 postgresql의 명칭 사이에 혼란도 있어 곤란을 겪는 분이 적지 않으실 것 같습니다. (postgres는 postgresql의 약자로 널리 통용되나 공식 명칭은 postgresql입니다.)  

---

# 추가 팁

- 바인드 볼륨 경로에 상대 경로를 쓰지 말고, 절대 경로를 쓰세요.  
- 바인드 볼륨 경로 입력 시 `$(pwd)`를 쓰면 현재 경로를 쉽게 넣을 수 있습니다.  
- `--name`로 이름을 입력하면 길고 복잡한 컨테이너ID를 대신 할 수 있습니다.  
- `--rm` 옵션을 주면 컨테이너 종료 시 자동으로 삭제됩니다.  
- `-e POSTGRES_PASSWORD=비밀번호`를 주지 않으면 postgresql 서비스가 올라가지 않습니다. 쉽게 말하면 초기 비밀번호를 주는 것입니다.
- 이후, `docker run exec mydb(내가올린postgresql도커이름) bash`하면 쉘 접속할 수 있고 `psql -U postgres`하면 디비에 접속 할 수 있습니다.
- 사용자도 만들어보고(`create user myuser password 'secret' superuser`) 디비도 만들어(`create database mydatabase owner myuser`) 보세요. `\c mydb`로 해당 디비에 연결하고, `\l`로 디비 목록도 볼 수 있고, `\dt`로 테이블 목록도 볼 수 있습니다.  

__

아래는 예시이고 `앞부분:뒷부분` 중에서 `앞부분`에 해당하는 내용입니다. postgresql의 경우 `뒷부분`은 특별한 이유가 없다면 `/var/lib/postgresql/data`로 고정입니다.  

**네임드 볼륨**  
`mymydata`  
그러니까 `-v mymydata:/var/lib/postgresql/data`  
볼륨은 `docker volume ls`로 확인 가능  

**바인드 볼륨**  
`$(pwd)/mymydata`
그러니까 `-v $(pwd)/mymydata:/var/lib/postgresql/data`   
볼륨은 `$(pwd)/mymydata`에서 확인 가능
