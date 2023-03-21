---
title: Spring Study Group Week4 - Spring 프로젝트 생성, 빌드 도구, 디자인패턴, REST API, MVC
author: ahma0
date:   2023-03-21 09:54:00 +0900
categories: [StudyGroup, Spring]
tag: [StudyGroup, Spring, Study, JAVA]
math: true
mermaid: true
image:
  path: https://user-images.githubusercontent.com/84761609/220085479-19ee260a-d709-4a47-b788-416275d8a2d8.png
  alt: Spring Image
---

> 스프링 스터디 4주차 공부한 것입니다. [어라운드 허브 스튜디오의 스프링 강의]("https://www.youtube.com/playlist?list=PLlTylS8uB2fBOi6uzvMpojFrNe7sRmlzU") 를 듣고 작성하였습니다.
{: .prompt-info }

<br>

# Spring 구조 살펴보기

## 프로젝트 만들기

> IDE: InteliJ Ultimate

<br>

`New Project -> Spring Initiolizer`

<br>

### Project Settings

| Settings | Info |
| --- | --- |
| Name | TestProject |
| Type | Maven |
| Language | Java |
| Group | studio.thinkground |
| Artifact | testproject |
| Project SDK | 1.8 |
| Java | 8 |
| Packaging | Jar |
| Spring Boot | 2.5.0 |
| Dependencies | Lombok, Spring Configuration Processor, Spring Web |

<br>

<hr>

# Meven과 Gradle

## 빌드 관리 도구

### 빌드 관리 도구란?

- 프로젝트에서 필요한 xml, properties, jar 파일들을 자동으로 인식하여 빌드해주는 도구
- 소스 코드를 컴파일, 테스트, 정적분석 등을 하여 실행가능한 앱으로 빌드.
- 프로젝트 정보 관리, 테스트 빌드, 배포 등의 작업을 진행.
- 외부 라이브러리를 참조하여 자동으로 다운로드 및 업데이트 관리.
- 자바의 대표적인 빌드 도구: Ant, Maven, Gradle

<br>

## Maven

### Maven이란?

- 자바의 대표적인 관리 도구얐던 Ant를 대체하기 위해 개발됨.
- 프로젝트의 외부 라이브러리를 쉽게 참조할 수 있게 pom.xml 파일로 명시하여 관리.
- 참조한 외부 라이브러리에 연관된 다른 라이브러리도 자동으로 관리됨.

<br>

### Maven을 사용하는 이유

- 기존에 사용하던 Ant는 빌드의 기능만 가지고 있음.
- 자동으로 라이브러리를 관리해주는 기능이 추가된 Maven을 사용
- 다운받아 사용하던 라이브러리에 변동 사항이 있으면 자동으로 업데이트하여 적용됨.

<br>

| Ant | Maven |
| --- | --- |
| XML 기반의 빌드 스크립트 | XML 기반의 빌드 스크립트 |
| 자유로운 빌드 단위 지정 | 라이프 사이클 도입 |
| 간단하고 사용하기 쉬움 | pom.xml로 편하게 Dependency 관리 |
| 대규모 프로젝트에서 복잡해지는 경향이 있음 | |
| 라이프 사이클이 없음 | |

<br>

### Maven 간단 사용법

- pom.xml 파일을 활용하여 빌드 및 관리

- pom.xml의 역할
	- 프로젝트 정보 관리
	- 해당 프로젝트에서 사용하는 외부 라이브러리 관리
	- 해당 프로젝트의 빌드 관련 설정

<br>

### Maven 대표 태그 설명

| Tag | Info |
| --- | --- |
| modeVersion | maven의 버전을 의미 |
| groupId | 프로젝트 그룹 id를 뜻하며, 일반적으로 대표하는 사이트 도메인을 역순으로 적어 사용 (ex: thinkground.studio -> studio.thinkground) |
| artifactId | groupId외에 다른 프로젝트와는 구분될 수 있는 프로젝트의 Id를 작성 |
| version | 프로젝트의 버전을 의미하며 개발 단계에 따라 구분하여 작성 |
| name | 프로젝트의 이름 |
| description | 해당 프로젝트의 간략한 설명을 작성 |
| properties | pom.xml 파일 내에서 빈번하게 사용되는 중복 상수를 정의하는 영역. 해당 영역의 상수를 사용하기 위해서는 `${태그명}`의 형태로 사용하면 됨 |
| dependencies | 해당 프로젝트에서 의존성을 가지고 사용하는 라이브러리를 정의하는 영역. 각 라이브러리마다 `<dependency>` 태그를 사용하여 구분. |
| build | 프로젝트 빌드와 관련된 정보를 설정하는 영역 |

<br>

## Gradle 

### Gradle이란?

- Groovy 스크립트를 활용한 빌드 관리 도구
- 안드로이드 프로젝트의 표준 빌드 시스템으로 채택
- 멀티 프로젝트(Multi-Project)의 빌드에 최적화하여 설계됨
- Maven에 비해 더 빠른 처리속도와 더 간결한 구성 가능

<br>

### Gradle과 Maven의 비교

- Gradle에 비해 Maven이 점유율이 더 높은 상황(점차 Gradle 점유율 오르는 중)
- Gradle에 비해 Maven의 성능이 떨어짐
- Maven에 비해 Gradle이 대규모 프로젝트에서의 성능이 좋음
- Maven: pom.xml
- Gradle: build.gradle
- Gradle은 설치 없이 사용할 수 있다. (Gradle Wrapper)

<br>

### Gradle 대표 용어 설명

| Term | Info |
| --- | --- |
| repositories | 라이브러리가 저장된 위치 등 설명 |
| mavenCentral | 기본 Maven Repository |
| dependencies | 라이브러리를 사용하기 위한 의존성 설정 |

<br>

<hr>

# Spring Boot 기초 - Design Pattern

## 디자인패턴

### 디자인패턴이란?

- (소프트웨어) 디자인 패턴이란 특정 문맥에서 공통적으로 발생하는 문제에 대해 쓰이는 재사용 가능한 해결책
- 목적별로 일정한 패턴이 제시되어 있음
- 완전한 정답이 되는 알고리즘과 달리 현재 상황에 맞춰 최적화된 패턴을 결정하여 사용하는 것이 좋음
- 대표적으로 구체화된 디자인 패턴은 GoF(Gang of Four)에서 제시한 총 23개의 패턴이 있음

> Gang of Four: Erich Gamma, Richard Helm, Ralph Johnson, John Vissides, 총 4명을 일컬음

<br>

### 디자인 패턴의 장단점

| 장점 | 단점 |
| --- | --- |
| 개발자 간의 원활한 협업이 가능 | 객체지향적 설계를 고려하여 진행해야 함 |
| 소프트웨어의 구조를 파악하기 용이함 | 초기 투자 비용이 많이 들어감 (돈 뿐만 아니라 시간 등 포함) |
| 재사용을 위해 개발 시간 단축 | |
| 설계 변경이 있을 경우 비교적 원활하게 조치가 가능 | |

<br>

## GoF의 디자인 패턴

### 목적에 따른 분류

- 생성 패턴, 구조 패턴, 행동 패턴 총 3가지로 구분됨
- 각 패턴이 어떤 작업을 위해 생성되는 것인지에 따른 구분

| 생성 패턴 | 구조 패턴 | 행동 패턴 |
| --- | --- | --- |
| Abstract Factory | Adapter | Chain of Responsibility |
| *Builder* | Bridge | Command |
| *Factory Method* | Composite | Interpreter |
| Prototype | Decorator | Iterator |
| Singleton | Facade | Mediator |
| | Flyweight | Memento |
| | Proxy | Observer |
| | | State |
| | | Strategy |
| | | Template Method |
| | | Visitor |

<br>

### 생성 패턴

- 생성 패턴은 객체의 생성과 관련된 패턴
- 특정 객체가 생성되거나 변경되어도 프로그램 구조에 영향을 최소화할 수 있도록 유연성 제공

| 생성 패턴 | 의도 |
| --- | --- |
| 추상 팩토리 (Abstract Factory) | 구체적인 클래스를 지정하지 않고 인터페이스를 통해 연관되는 객체들을 묶어줌 |
| 빌더 (Builder) | 객체의 생성과 표현을 분리하여 객체를 생성 |
| 팩토리 메소드 (Factory Method) | 객체 생성을 서브클래스로 분리하여 위임 (캡슐화) |
| 프로토타입 (Prototype) | 원본 객체를 복사하여 객체를 생성 (클론) |
| 싱글톤 (Singleton) | 한 클래스마다 인스턴스를 하나만 생성하여 어디서든 참조 |

<br>

### 구조 패턴

- 구조 패턴은 프로그램 내 자료 구조나 인터페이스 구조 등 프로그램 구조를 설계하는데 사용되는 패턴
- 클래스나 객체를 조합하여 더 큰 구조를 만들 수 있게 해줌

| 구조 패턴 | 의도 |
| --- | --- |
| 어댑터 (Adapter) | 클래스의 인터페이스를 어떤 클래스에서든 이용할 수 있도록 변환 |
| 브리지 (Bridge) | 구현부에서 추상층을 분리하여 각자 독립적으로 변형하고 확장할 수 있도록 함 |
| 컴포지트 (Composite) | 객체들의 관계를 트리 구조로 구성하여 표현하는 방식으로 복합 객체와 단일 객체를 구분 없이 다룸 |
| 데코레이터 (Decorator) | 주어진 상황에 따라 객체에 다른 객체를 덧붙임 |
| 파사드 (Facade) | 서브 시스템에 있는 인터페이스 집합에 대해 통합된 인터페이스 제공 |
| 플라이웨이트 (Flyweight) | 크기가 작은 여러 개의 객체를 매번 생성하지 않고 최대한 공유하여 사용하도록 메모리 절약 |
| 프록시 (Proxy) | 실제 기능을 수행하는 대신 가상의 객체를 사용해 로직의 흐름을 제어 |

<br>

### 행동(행위) 패턴

- 행동 패턴은 반복적으로 사용되는 객체들의 커뮤니케이션을 패턴화
- 객체 사이에 알고리즘 또는 책임을 분배하는 방법에 대해 정의됨
- 결합도를 최소화하는 것이 주 목적

| 행동 패턴 | 의미 |
| --- | --- |
| 책임 연쇄 (Chain of Responsibility) | 요청을 받는 객체를 연쇄적으로 묶어 요청을 처리하는 객체를 만날 때까지 객체 Chain을 따라 요청을 전달 |
| 커맨드 (Command) | 요청을 객체의 형태로 캡슐화하여 재사용하거나 취소 |
| 인터프리터 (Interpreter) | 특정 언어의 문법 표현을 정의 |
| 반복자 (Iterator) | 컬렉션 구현 방법을 노출하지 않으면서 모든 항목에 접근할 수 있는 방법을 제공 |
| 중재자 (Mediator) | 한 집합에 속해있는 객체들의 상호작용을 캡슐화하여 새로운 객체로 정의 |
| 메멘토 (Memento) | 객체가 특정 상태로 다시 되돌아올 수 있도록 내부 상태를 실체화 |
| 옵저버 (Observer) | 객체 상태가 변할 때 관련 객체들이 그 변화를 전달받아 자동으로 갱신 |
| 상태 (State) | 객체의 상태에 따라 동일한 동작을 다르게 처리 |
| 전략 (Strategy) | 동일 계열의 알고리즘군을 정의하고 캡슐화하여 상호 교환이 가능하게 함 |
| 템플릿 메소드 (Template Method) | 상위 클래스는 알고리즘의 골격만을 작성하고 구체적인 처리는 서브 클래스로 위임 |
| 방문자 (Visitor) | 객체의 원소에 대해 수행할 연산을 분리하여 별도의 클래스로 구성 |

<br>

<hr>

# Spring Boot 기초 - RestAPI

## REST API

### API란?

- Application Programming Interface의 줄임말
- 응용 프로그램에서 사용할 수 있도록 다른 응용 프로그램을 제어할 수 있게 만든 인터페이스
- API를 사용하면 내부 구현 로직을 알지 못해도 정의되어 있는 기능을 쉽게 사용할 수 있음

- 여기서 인터페이스(Interface)란 어떤 장치간 정보를 교환하기 위한 수단이나 방법을 의미
	- 예) 마우스, 키보드, 터치패드 등

<br>

### REST란?

- REST는 Representational State Transfer의 줄임말
- 자원의 이름으로 구분하여 해당 자원의 상태를 교환하는 것
- REST는 서버와 클라이언트의 통신 방식 중 하나
- HTTP URI(Uniform Resource Identifier)를 통해 자원을 명시하고 HTTP Method를 통해 자원을 교환하는 것
	- HTTP Method: CRUD

<br>

### REST 특징

#### Server-Client 구조

- 자원이 있는 쪽이 Server, 요청하는 쪽이 Client
- 클라이언트와 서버가 독립적으로 분리되어 있어야 함

#### Stateless

- 요청 간에 클라이언트 정보가 서버에 저장되지 않음
- 서버는 각각의 요청을 완전히 별개의 것으로 인식하고 처리

#### Cacheable

- HTTP 프로토콜을 그대로 사용하기 때문에 HTTP의 특징인 캐싱 기능을 적용
- 대량의 요청을 효율적으로 처리하기 위해 캐시를 사용

#### 계층화 (Layered System)

- 클라이언트는 서버의 구성과 상관없이 REST API 서버로 요청
- 서버는 다중 계층으로 구성될 수 있음 (로드밸런싱, 보안 요소, 캐시 등)

#### Code on Demand (Optinal)

- 요청을 받으면 서버에서 클라이언트로 코드 또는 스크립트(로직)을 전달하여 클라이언트 기능 확장

#### 인터페이스 일관성 (Uniform Interface)

- 정보가 표준 형식으로 전송되기 위해 구성 요소간 통합 인터페이스를 제공
- HTTP 프로토콜을 따르는 모든 플랫폼에서 사용 가능하게끔 설계

<br>

### REST의 장점

- HTTP 표준 프로토콜을 사용하는 모든 플랫폼에서 호환 가능
- 서버와 클라이언트의 역할을 명확하게 분리
- 여러 서비스 설계에서 생길 수 있는 문제를 최소화

<br>

### REST API란?

- REST 아키텍처의 조건을 준수하는 어플리케이션 프로그래밍 인터페이스
- 최근 많은 API가 REST API로 제공되고 있음
- 일반적으로 REST 아키텍처를 구현하는 웹 서비스를 RESTful하다고 표현

<br>

### REST API 특징

- REST 기반으로 시스템을 분산하여 확장성과 재사용성을 높임
- HTTP 표준을 따르고 있어 여러 프로그래밍 언어로 구현할 수 있음

<br>

### REST API 설계 규칙

- 웹 기반의 REST API를 설계할 경우에는 URI를 통해 자원을 표현해야 함.
	- https://thinkground.studio/member/589
	- Resource: member
	- Resource Id: 589
- 자원에 대한 조작은 HTTP Method(CRUD)를 통해 표현해야 함
	- URI에 행위가 들어가면 안됨
	- HEADER를 통해 CRUD를 표현하여 동작을 요청해야 함
- 메세지를 통한 리소스 조작
	- HEADER를 통해 content-type을 지정하여 데이터를 전달
	- 대표적 형식으로는 HTML, XML, TEXT가 있음
- URI에는 소문자를 사용
- Resource의 이름이나 URI가 길어질 경우 하이픈`-`을 통해 가독성을 높일 수 있음
- 언더바`_`는 사용하지 않음
- 파일 확장자를 표현하지 않음

<br>

<hr>

# Spring Boot 기초 - MVC 패턴

## MVC 패턴

### MVC (Model View Controller)

- 디자인 패턴 중 하나인 MVC 패턴은 Model, View, Controller의 줄임말로 어플리케이션을 구성할 때 그 구성요소를 세 가지의 역할로 구분한 패턴을 의미
- 사용자 인터페이스로부터 비즈니스 로직을 분리하여 서로 영향 없이 쉽게 고칠 수 있는 설계가 가능

![MVC 패턴 구조](https://user-images.githubusercontent.com/84761609/226183730-6284853f-6084-411e-94d6-792346ca3a7c.png)

<br>

### 컨트롤러 (Controller)

- 모델(Model)과 뷰(View) 사이에서 브릿지 역할을 수행
- 앱의 사용자로부터 입력에 대한 응답으로 모델 및 뷰를 업데이트 하는 로직을 포함
- 사용자의 요청은 모두 컨트롤러를 통해 진행되어야 함
- 컨트롤러로 들어온 요청은 어떻게 처리할지 결정하여 모델로 요청 전달

<br>

### 모델 (Model)

- 데이터를 처리하는 영역
- 데이터베이스와 연동을 위한 DAO(Data Access Object)와 데이터 구조를 표현하는 DO(Data Object)로 구성
- 데이터와 비즈니스 로직 관리

<br>

### 뷰(View)

- 데이터를 보여주는 화면 자체의 영역을 뜻함
- 사용자 인터페이스(UI) 요소들이 여기에 포함되며, 데이터를 각 요소에 배치함
- 뷰에서는 별도의 데이터를 보관하지 않음

<br>

### MVC 패턴의 특징

- 어플리케이션의 역할을 세 구간으로 나누어 설계함으로써 서로 간의 의존성이 낮아짐
- 각 영역이 독립적으로 구성되어 개발자 간 분업 및 협업이 원활해짐
- 한 영역을 업데이트 하더라도 다른 곳에 영향을 주지 않음



