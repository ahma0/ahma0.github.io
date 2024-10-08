---
title: Windows에서 Redis 설치하기 with WSL
author: ahma0
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

### 🚨 오류 해결

설치 위치가 usr/local/bin인데, redis-server를 입력해서 실행해보면 usr/bin에 파일이 없다는 엉뚱한 소리를 하는 오류가 생길 때가 있다. 이 경우 아래의 명령어를 작성해주면 된다. 

```shell
# 홈 경로로 이동한 후 Redis 서버 실행 파일과 redis-cli를 시스템의 /usr/bin 디렉터리로 복사
$ cd ~
$ sudo cp ~/redis-7.4.1/src/redis-server /usr/bin
$ sudo cp ~/redis-7.4.1/src/redis-cli /usr/bin

# 서버 실행
$ redis-server
```
