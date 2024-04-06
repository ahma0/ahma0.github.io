---
title: AWS ec2에 Spring Boot 서버 배포하기
author: ahma0
date:   2024-04-02 18:13:00 +0900
categories: [Study, AWS]
tag: [Study, AWS, ec2]
---

## JAR 파일 복사해서 서버 띄우기

### 1. 서버 빌드

```
$ ./gradlew clean build
```

### 2. JAR 파일 원격지에 복사하기

나는 홈에 ec2서버를 설정하였기에 홈으로 하였다.

```
# scp <jar 파일 경로> <host 이름>:<파일이 저장될 ec2 실행경로>
$ scp build/libs/TeybatGuide-0.0.1-SNAPSHOT.jar teybat-web01:~
```

<br>

### 3. ec2 실행 및 jdk 다운

```
$ ssh <host 이름>        # ec2 실행
$ sudo yum update       # yum 업데이트

# jdk17 다운
$ sudo yum install java-17-amazon-corretto
```

<br>

### 4. 인바운드 추가

Spring Boot는 서버 포트가 8080이기 때문에 8080 포트로 들어오는 요청들을 허용해야하므로 이를 추가해준다. 그리고 SSH 와 http, https 도 함께 추가한 뒤 instance에 추가해준다.

![인바운드 규칙](https://github.com/ahma0/ahma0.github.io/assets/84761609/7c4091c5-cdb1-42b3-b9fd-c39efa5e5713)

<br>

### 5. JAR 파일 실행

```
# java -jar <jar 파일 이름>
$ java -jar TeybatGuide-0.0.1-SNAPSHOT.jar
```

<br>

### 오류

> [에러 메시지] com.mysql.cj.jdbc.exception.CommunicationsException: Communications link failure
{: .prompt-danger }

![오류](https://github.com/ahma0/ahma0.github.io/assets/84761609/11075116-2288-4911-b480-e9614e55eebc)

서버가 mysql, jdbc를 사용하기 때문에 RDS 에러를 낸다.


작성중.. 