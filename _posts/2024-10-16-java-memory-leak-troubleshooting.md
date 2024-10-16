---
title: 자바 메모리 누수 해결해보기
author: ahma0
date:   2024-10-16 10:05:00 +0900
categories: [Post, TroubleShooting]
tag: [Study, Linux, TroubleShooting, Java, MemoryLeak]
---

## 🥑 들어가며

먼저 나는 현재 회사에서 순수 자바코드로 크롤러를 만들고 있다. 크롤러는 멀티 스레드, 멀티 프로세스로 동작하는데 난 셀레니움 파트를 맡아서 개발중이다. 사실 크롤러에서 가장 중요한 부분이기에 인턴인 내가 이걸 맡아도 되나 싶기도 하다. 크롤러 매니저에서 크롤러를 실행·종료시카는데, 크롤러를 강제 중지할 일이 많다. 그래서 크롬과 크롬드라이버 관리를 위해 레디스를 도입하였다. 크롤러에서 크롬과 크롬 드라이버의 포트를 레디스에 저장하여 이후 크롤러를 강제 중지시킬 때 매니저에서 레디스에 있는 값을 찾아 제거시키기로 하였다. 그런데 레디스를 추가하고 코드를 고치는 과정에서 계속 메모리 누수가 일어나는 것 같았다.

<br>

## 문제 상황

![image](https://github.com/user-attachments/assets/d6751a83-09ab-4de5-92bb-90797c85de7d)

![image](https://github.com/user-attachments/assets/2af5e648-45eb-47cf-aa5e-edbd0e7f3ad0)



10분도 안되는 시간 동안 java의 메모리 점유율이 30%가 넘고, 메모리가 야금야금 올라가는 상황. GC가 실행되지 않고 계속 쌓이는건가 싶어 `System.gc()`를 스레드가 끝날 때 마다 호출하였다. 그러나 이건 급한 불을 끄는 것일 뿐 좋은 해결방안이 아니다. 위의 사진들이 GC를 호출하게 하여 하루동안 크롤러를 돌린 상태이다. 그래서 검색을 해보았고 IntelliJ의 VisualVM이라는 플러그인을 발견하였으나, `cannot open requested application`이라는 오류로 인해 모니터링을 하지 못하는 상황이다. 아직 GC가 꽉 찼다는 메세지를 받은 것도 아니고 OutOfMemory가 뜬 것도 아니지만 메모리 점유율이 높은건 좋은 상황이 아니기에 빠르게 해결해야 한다. eclipse의 Memory Analyzer(MAT)를 사용하기로 결정하였다.

<br>

## VisualVM으로 확인하기

IntelliJ에 플러그인을 설치한 후 VisualVM 프로그램을 다운받아주면 준비가 끝난다.

![image](https://github.com/user-attachments/assets/175955d0-13e7-422a-b15b-17ecd6cd99e3)

로컬에서 VisualVM으로 확인해본 결과, 멀쩡하게 동작하지만 어딘가 파란색 그래프가 점점 높아지고 있다는 느낌이 들었다. 확실히 하기 위해 dump 파일을 확인하기로 했다.


<br>

## dump.hprof 생성하기

먼저 자바 애플리케이션이 실행 중에 메모리 누수 등의 문제가 발생했을 때 관련된 문제를 정리하여 덤프파일을 생성할 수 있다. 상황이 발생했을 때 힙덤프를 생성하는 선택사항(`-XX:+HeapDumpOnOutOfMemoryError`)과 위치를 지정하는 선택사항(`-XX:HeapDumpPath=/var/log`)를 추가한다. 이렇게 설정해놓으면 OutOfMemoryError가 발생했을 때 java_pid{pid}.hprof파일이 지정된 위치에 생성된다.

우선 `System.gc()`를 주석처리한 뒤 `-XX:+HeapDumpOnOutOfMemoryError`와 `-XX:HeapDumpPath=/var/log`를 추가하였다.

```java
...
//메모리 덤프 생성 옵션 설정
processStartCmd.append("-XX:+HeapDumpOnOutOfMemoryError").append(blank);
processStartCmd.append("-XX:HeapDumpPath=/var/log").append(blank);
...
```

```gradle
application {
    applicationDefaultJvmArgs = ['-Dgreeting.language=ko',
                                 '-Xms1024m',
                                 '-Xmx8096m'
                                 , '-XX:+HeapDumpOnOutOfMemoryError', '-XX:HeapDumpPath=/webfilter/logs'
    ]
    mainClass = '<메인클래스 경로>'
}
```

이제 OutOfMemory가 발생할 경우 dump 파일을 확인하면 된다. 나의 경우 주기적으로 JVM을 내렸다가 다시 올려 OutOfMemory가 나려면 시간이 꽤 걸릴 것 같았다 그래서 dump 파일을 생성하기로 결정하였다.

```shell
# pid 확인
$ jps

# dump.hprof 생성
$ jmap -dump:format=b,file=heap_dump.hprof <PID>
Dumping heap to /webfilter/logs/heap_dump.hprof ...
Heap dump file created [2350510466 bytes in 60.630 secs]

$ ll
-rw-------  1 webfilter webfilter 2350510466 10월 16 18:12 heap_dump.hprof
```

이제 이 파일을 이용해 분석을 하면 된다.

<br>

## Eclipse MAT(Memory Analyzer Tool) 사용해보기

MAT는 자바 힙 분석을 위한 풍부한 기능을 제공하여 메모리 누수 및 메모리 소비 감축요소를 찾을 수 있도록 돕는다.

우선 [Eclipse MAT 사이트](https://eclipse.dev/mat/)에 접속해 본인 운영체제에 맞는 MAT를 다운받는다.



...작성중