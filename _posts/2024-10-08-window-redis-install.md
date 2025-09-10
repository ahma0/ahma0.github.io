---
title: Windows에서 Redis 설치하기 with WSL
author: dnya0
date:   2024-10-08 12:05:00 +0900
categories: [Post, Redis]
tag: [Study, Redis, Window, WSL]
math: true
mermaid: true
image:
  path: https://blog.kakaocdn.net/dn/RAW9i/btsEizJaYLD/BHuas9r4K6OZD96rI4x94K/img.png
  alt: redis image
---

## 🥑 들어가며

회사에서 내가 맡고있는 업무에 Redis를 도입하려 한다. 로컬은 윈도우고 서버는 리눅스라서, postgresql을 사용하듯이 Redis도 테스트 서버에 설치하고 로컬에서 연결하여 사용하려 하였다. 그러나 연결이 되지 않는 것이다! 그래서 로컬에 깔아서 테스트하고 서버에 올리려 한다. 윈도우에선 공식적으로 Redis를 지원하지 않는다는 글을 읽었다. 따라서 Redis를 사용하려면 WSL을 이용해서 Redis를 설치해야 한다는 것! 그래서 윈도우에서 Redis 설치 방법을 기록하려 한다.

<br>

## 📌 WSL 설치하기

WSL은 Window Subsystem for Linux의 약자이다. 전부는 아니지만 리눅스의 기능을 일부 사용할 수 있다. WSL을 설치하기 전 *Windows 기능 켜기 끄기*의 상태를 먼저 올리려 한다. 혹시나 중요하게 작용할 수도 있으니까.

![image](https://github.com/user-attachments/assets/420b2543-08fa-42ec-8f95-0175ac7b0265)

`가상머신 플랫폼`과 `Linux용 Windows 하위 시스템`이 체크되어 있다.

그리고 `Windows PowerShell`에 들어가 순서대로 쳐주면 된다.

```shell
# wsl 설치(설치가 되고 나면 Unix의 username과 password를 설정하게 된다.)
$ wsl --install

# Redis 패키지의 GPG 키를 추가하여, Redis를 안전하게 설치할 수 있도록 시스템에 신뢰할 수 있는 패키지 키를 설정
$ curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

# wsl을 설치하였으니 apt를 update 및 upgrade 해야한다.
$ sudo apt update
$ sudo apt upgrade

# build-essential, pkg-config 설치
# build-essential: 소스 코드를 빌드할 때 필요한 기본적인 패키지를 모아둔 것
# 설치 과정에서 yes를 요구하는 질의에 대해 전부 yes를 할 것이므로 -y 플래그를 넣는다.
$ sudo apt-get install build-essential pkg-config -y
```

<br>

## 📌 Redis 설치

WLS를 설치했으니 이제 Redis를 설치하면 된다.

```shell
# Redis 7.4.1 파일 다운로드
# Redis의 버전들은 https://download.redis.io/releases/에서 볼 수 있다.
$ wget https://download.redis.io/releases/redis-7.4.1.tar.gz

# tar.gz 파일 압축 해제 및 삭제
$ tar xvfz redis-*.tar.gz
$ rm redis-*.tar.gz

# 심볼릭 링크 생성
$ ln -s /home/ubuntu/app/redis* redis

# redis 파일로 이동
$ cd redis
-bash: cd: redis: No such file or directory

$ ll
total 28
drwxr-x--- 4 user user 4096 Oct  8 11:54 ./
drwxr-xr-x 3 root  root  4096 Oct  8 11:29 ../
-rw-r--r-- 1 user user  220 Oct  8 11:29 .bash_logout
-rw-r--r-- 1 user user 3771 Oct  8 11:29 .bashrc
drwx------ 2 user user 4096 Oct  8 11:29 .cache/
-rw-r--r-- 1 user user    0 Oct  8 11:29 .motd_shown
-rw-r--r-- 1 user user  807 Oct  8 11:29 .profile
-rw-r--r-- 1 user user    0 Oct  8 11:43 .sudo_as_admin_successful
lrwxrwxrwx 1 user user   23 Oct  8 11:54 redis -> '/home/ubuntu/app/redis*'
drwxr-xr-x 8 user user 4096 Oct  3 04:04 redis-7.4.1/

# 왜인진 모르겠으나 redis로 이동이 되지 않았다.
$ cd redis-7.4.1

# 관리자 권한으로 Makefile에 정의된 빌드 프로세스를 실행
$ sudo make

$ redis-server
6865:C 08 Oct 2024 12:00:39.621 # WARNING Memory overcommit must be enabled! Without it, a background save or replication may fail under low memory condition. Being disabled, it can also cause failures without low memory condition, see https://github.com/jemalloc/jemalloc/issues/1328. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
6865:C 08 Oct 2024 12:00:39.621 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
6865:C 08 Oct 2024 12:00:39.621 * Redis version=7.4.1, bits=64, commit=00000000, modified=1, pid=6865, just started
6865:C 08 Oct 2024 12:00:39.621 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
6865:M 08 Oct 2024 12:00:39.622 * Increased maximum number of open files to 10032 (it was originally set to 1024).
6865:M 08 Oct 2024 12:00:39.622 * monotonic clock: POSIX clock_gettime
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis Community Edition
  .-`` .-```.  ```\/    _.,_ ''-._     7.4.1 (00000000/1) 64 bit
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 6865
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           https://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

6865:M 08 Oct 2024 12:00:39.623 * Server initialized
6865:M 08 Oct 2024 12:00:39.623 * Ready to accept connections tcp
^C
6865:signal-handler (1728356443) Received SIGINT scheduling shutdown...
6865:M 08 Oct 2024 12:00:43.149 * User requested shutdown...
6865:M 08 Oct 2024 12:00:43.149 * Saving the final RDB snapshot before exiting.
6865:M 08 Oct 2024 12:00:43.163 * DB saved on disk
6865:M 08 Oct 2024 12:00:43.163 # Redis is now ready to exit, bye bye...
```

<br>

### 🚨 오류 해결

설치 위치가 `usr/local/bin`인데, `redis-server`를 입력해서 실행해보면 `usr/bin`에 파일이 없다는 엉뚱한 소리를 하는 오류가 생길 때가 있다. 이 경우 아래의 명령어를 작성해주면 된다. 

```shell
# 홈 경로로 이동한 후 Redis 서버 실행 파일과 `redis-cli`를 시스템의 `/usr/bin` 디렉터리로 복사
$ cd ~
$ sudo cp ~/redis-7.4.1/src/redis-server /usr/bin
$ sudo cp ~/redis-7.4.1/src/redis-cli /usr/bin

# 서버 실행
$ redis-server
```

<br>

## 🍥 Redis 백그라운드 실행하기

Redis를 실행하기 위해 `sudo systemctl start redis`를 입력하였다.

```shell
$ sudo systemctl start redis
[sudo] password for user:
Failed to start redis.service: Unit redis.service not found.
```

Redis 설치를 끝낸 줄 알았는데 `redis.service`가 없다니! 새로 만들어주기로 결정하였다.

```shell
# redis-server가 설치되어 있는지 확인
# 하지만 우리는 방금 redis-server를 입력해 서버가 실행되는 것을 확인했으니 생략해도 된다.
$ which redis-server
/usr/bin/redis-server

# redis-server와 redis-cli의 위치 찾기
$ whereis redis-server
redis-server: /usr/bin/redis-server
$ whereis redis-cli
redis-cli: /usr/bin/redis-cli

# redis.service 생성
# 나는 vi를 사용해서 작성하였다. nano를 사용한다면 nano를 써도 된다.
$ sudo vi /etc/systemd/system/redis.service
sudo mv ~/jiran/redis-7.4.1/* /etc/redis/
```

<br>

### 🥨 Service 파일 작성

`redis.service` 파일을 아래와 같이 작성한다. `ExecStart`와 `ExecStop`에 들어갈 경로는 아까 `whereis` 커맨드를 입력해서 찾은 경로를 입력해줘야 한다. 나는 `/usr/bin`로 떠서 저렇게 입력해줬다. 만약 `/usr/local/bin`으로 뜬다면 `/usr/local/bin`으로 작성하면 된다. `/etc/redis/redis.conf` 또한 본인의 'redis.conf' 경로를 입력해주면 된다.

```ini
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/bin/redis-cli shutdown
User=redis
Group=redis
Restart=always

[Install]
WantedBy=multi-user.target
```

전부 작성을 끝냈다. 이제 실행해보자.

```shell
# 서비스 리로드
$ sudo systemctl daemon-reload

# 백그라운드로 Redis 실행
$ sudo systemctl start redis

# 제대로 실행되었는지 확인하기 위해 ping 보내기
# 백그라운드에 떠있다면 PONG이 와야한다.
$ redis-cli ping
PONG
```

<br>

### 🚨 오류 해결

나는 PONG이 오지 않았다.

```shell
$ redis-cli ping
Could not connect to Redis at 127.0.0.1:6379: Connection refused

# redis status 확인
$ sudo systemctl status redis
× redis.service - Redis In-Memory Data Store
     Loaded: loaded (/etc/systemd/system/redis.service; disabled; preset: enabled)
     Active: failed (Result: exit-code) since Tue 2024-10-08 15:47:18 KST; 2min 40s ago
   Duration: 4ms
    Process: 7326 ExecStart=/usr/bin/redis-server /etc/redis/redis.conf (code=exited, status=217/USER)
   Main PID: 7326 (code=exited, status=217/USER)

Oct 08 15:47:18 computerName systemd[1]: redis.service: Scheduled restart job, restart counter is at 5.
Oct 08 15:47:18 computerName systemd[1]: redis.service: Start request repeated too quickly.
Oct 08 15:47:18 computerName systemd[1]: redis.service: Failed with result 'exit-code'.
Oct 08 15:47:18 computerName systemd[1]: Failed to start redis.service - Redis In-Memory Data Store.
```

`status=217/USER` 오류로 보아 사용자 관련 문제로 인해 발생한 것 같다. user를 지정해주지 않았는데 `redis.service` 파일에 작성해놨기 때문이다. 해결 방법은 간단하다. 주석치면 된다.

```ini
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/bin/redis-cli shutdown
# User=redis
# Group=redis
Restart=always

[Install]
WantedBy=multi-user.target
```

<br>

### 🚨 오류 해결 2

만약 아래와 같은 오류가 떴다면 `redis.service`의 `redis.conf` 경로가 제대로 되어있는지 확인해보자.

```shell
$ redis-cli ping
Could not connect to Redis at 127.0.0.1:6379: Connection refused

$ sudo systemctl status redis
× redis.service - Redis In-Memory Data Store
     Loaded: loaded (/etc/systemd/system/redis.service; disabled; preset: enabled)
     Active: failed (Result: exit-code) since Tue 2024-10-08 15:54:25 KST; 12s ago
   Duration: 19ms
    Process: 7490 ExecStart=/usr/bin/redis-server /etc/redis/redis.conf (code=exited, status=1/FAILURE)
   Main PID: 7490 (code=exited, status=1/FAILURE)

Oct 08 15:54:25 computerName systemd[1]: redis.service: Scheduled restart job, restart counter is at 5.
Oct 08 15:54:25 computerName systemd[1]: redis.service: Start request repeated too quickly.
Oct 08 15:54:25 computerName systemd[1]: redis.service: Failed with result 'exit-code'.
Oct 08 15:54:25 computerName systemd[1]: Failed to start redis.service - Redis In-Memory Data Store.
```

이 오류는 간단하다. `redis.conf`의 경로만 알맞게 바꿔주면 된다.

<br>

### +) redis 폴더 내의 모든 파일과 서브폴더 옮기기

이건 하지 않아도 된다. 나는 찝찝해서 옮겨놓았다.

```shell
$ cd ~/redis-7.4.1

# redis-7.4.1에 있는 폴더 리스트 출력
$ ll
total 316
drwxr-xr-x  8 user user   4096 Oct  8 14:50 ./
drwxr-x---  4 user user   4096 Oct  8 15:49 ../
drwxr-xr-x  2 user user   4096 Oct  3 04:04 .codespell/
-rw-r--r--  1 user user    405 Oct  3 04:04 .gitattributes
drwxr-xr-x  4 user user   4096 Oct  3 04:04 .github/
-rw-r--r--  1 user user    559 Oct  3 04:04 .gitignore
-rw-r--r--  1 user user  10420 Oct  3 04:04 00-RELEASENOTES
-rw-r--r--  1 user user     51 Oct  3 04:04 BUGS
-rw-r--r--  1 user user   5023 Oct  3 04:04 CODE_OF_CONDUCT.md
-rw-r--r--  1 user user   7178 Oct  3 04:04 CONTRIBUTING.md
-rw-r--r--  1 user user     11 Oct  3 04:04 INSTALL
-rw-r--r--  1 user user  37493 Oct  3 04:04 LICENSE.txt
-rw-r--r--  1 user user   6888 Oct  3 04:04 MANIFESTO
-rw-r--r--  1 user user    151 Oct  3 04:04 Makefile
-rw-r--r--  1 user user  23845 Oct  3 04:04 README.md
-rw-r--r--  1 user user   1805 Oct  3 04:04 REDISCONTRIBUTIONS.txt
-rw-r--r--  1 user user   1480 Oct  3 04:04 SECURITY.md
-rw-r--r--  1 user user   3628 Oct  3 04:04 TLS.md
drwxr-xr-x  8 user user   4096 Oct  8 11:55 deps/
-rw-r--r--  1 user user    343 Oct  8 14:50 dump.rdb
-rw-r--r--  1 user user 108981 Oct  3 04:04 redis.conf
-rwxr-xr-x  1 user user    279 Oct  3 04:04 runtest*
-rwxr-xr-x  1 user user    283 Oct  3 04:04 runtest-cluster*
-rwxr-xr-x  1 user user   1804 Oct  3 04:04 runtest-moduleapi*
-rwxr-xr-x  1 user user    285 Oct  3 04:04 runtest-sentinel*
-rw-r--r--  1 user user  14700 Oct  3 04:04 sentinel.conf
drwxr-xr-x  4 user user  12288 Oct  8 11:56 src/
drwxr-xr-x 11 user user   4096 Oct  3 04:04 tests/
drwxr-xr-x  9 user user   4096 Oct  3 04:04 utils/

# 폴더 내 모든 파일과 서브폴더를 /etc/redis로 옮긴다.
$ sudo mv ~/redis-7.4.1/{.,}* /etc/redis/

# 이렇게 뜨면 성공이다.
$ ll
total 8
drwxr-xr-x 2 user user 4096 Oct  8 16:06 ./
drwxr-x--- 4 user user 4096 Oct  8 16:03 ../
```
