---
title: Spring Study Group Week6 - validation, Exception, Test
author: ahma0
date:   2023-04-06 19:53:00 +0900
categories: [StudyGroup, Spring]
tag: [StudyGroup, Spring, Study, JAVA, KNU-Spring-Study]
math: true
mermaid: true
image:
  path: https://user-images.githubusercontent.com/84761609/220085479-19ee260a-d709-4a47-b788-416275d8a2d8.png
  alt: Spring Image
---

> 스프링 스터디 6주차 공부한 것입니다. [어라운드 허브 스튜디오의 스프링 강의](https://www.youtube.com/playlist?list=PLlTylS8uB2fBOi6uzvMpojFrNe7sRmlzU) 를 듣고 작성하였습니다.
{: .prompt-info }

<br>

# Spring Boot 기초 - 유효성 검사

## 유효성 검사 / 데이터 검증 (Validation)

### 유효성 검사란?

- 서비스의 비즈니스 로직이 올바르게 동작하기 위해 사용되는 데이터에 대한 사전 검증을 하는 작업이 필요함
- 유효성 검사 혹은 데이터 검증이라고 부르는데, 흔히 validation이라 부름
- 데이터의 검증은 여러 계층에서 발생되는 흔한 작업
- Validation은 들어오는 데이터에 대해 의도한 형식의 값이 제대로 들어오는지 체크하는 과정을 뜻함

<br>

### 일반적인 Validation의 문제점

- 일반적인 어플리케이션에서 사용되던 Validation 방식은 몇 가지 문제가 존재
	- 어플리케이션 전체적으로 분산되어 존재
	- 코드의 중복이 심함
	- 비즈니스 로직에 섞여있어 검사 로직 추적이 어려움

![img](https://user-images.githubusercontent.com/84761609/229517490-238af5c6-ac69-4f6f-8b35-6433d642d2c9.png)

<br>

### Bean Validation / Hibernate Validator

- 앞서 나온 문제를 해결하기 위해 <br> Java에서 2009년부터 Bean Validation이라는 데이터 유효성 검사 프레임워크를 제공
- Bean Validation은 어노테이션을 통해 다양한 데이터를 검증할 수 있게 기능을 제공
- Hibernate Validator는 Bean Validation 명세에 대한 구현체
- Spring Boot의 유효성 검사 표준은 Hibernate Validator를 채택
- 이전 버전의 Spring Boot에서는 starter-web에 validation이 포함되어 있었지만, <br> 2.3 버전부터는 starter-validation을 추가해야 함

<br>

### Validation 관련 어노테이션

| 어노테이션 | 설명 |
| --- | --- |
| `@Size` | 문자의 길이 조건 |
| `@NotNull` | null값 불가 |
| `@NotEmpty` | `@NotNull` + "" 값 불가 |
| `@NotBlank` | `@NotEmpty` + " "값 불가 |
| | |
| `@Past` | 과거 날짜 |
| `@PastOrPresent` | `@Past` + 오늘 날짜 |
| `@Future` | 미래 날짜 |
| `@FutureOrPresent` | `@Future` + 오늘 날짜 |
| | |
| `@Pattern` | 정규식을 통한 조건 |
| | |
| `@Max` | 최대값 조건 설정 |
| `@Min` | 최소값 조건 설정 |
| `@AssertTrue / AssertFalse` | 참 / 거짓 조건 설정 |
| | |
| `@Valid` | 해당 객체의 유효성 검사 |

<br>

<hr>

# Spring Boot 기초 - 예외 처리 (Exception)

## 예외처리 (Exception)

### 스프링 부트의 예외 처리 방식

- 스프링 부트의 예외 처리 방식엔 크게 2가지가 존재 
	-  `@ControllerAdvice`를 통한 모든 Controller에서 발생할 수 있는 예외 처리
	-  `@ExceptionHandler`를 통한 특정 Controller에서 발생할 수 있는 예외 처리
- `@ControllerAdvice`로 모든 컨트롤러에서 발생할 예외를 정의하고, <br> `@ExceptionHandler`를 통해 발생하는 예외마다 처리할 메소드를 정의

<br>

### 예외 클래스

![img](https://user-images.githubusercontent.com/84761609/229523310-5a5905bb-7f04-4c11-be0f-7e836c53b92f.png)

- 모든 예외 클래스는 Throwable 클래스를 상속받고 있음
- Exception은 수많은 자식 클래스가 있음
- RuntimeException은 UncheckedException이며, 그 외 Exception은 CheckedException으로 볼 수 있음

| | CheckedException | UncheckedException |
| --- | --- | --- |
| 처리 여부 | 반드시 예외 처리 필요 | 명시적 처리 강제하지 않음 |
| 확인 시점 | 컴파일 단계 | 실행 중 단계 |
| 예외 발생 시 트랜잭션 | 롤백하지 않음 | 롤백함 |
| 대표 예외 | IOException <br> SQLException | NullPointerException <br> IllegalException <br> IndexOutOfBoundException <br> SystemException |

<br>

### @ControllerAdvice, @RestControllerAdvice

- `@ControllerAdvice`는 Spring에서 제공하는 어노테이션
- `@Controller`나 `@RestController`에서 발생하는 예외를 한 곳에서 관리하고 처리할 수 있게 하는 어노테이션
- 설정을 통해 범위 지정이 가능하며, Default 값으로 모든 Controller에 대해 예외 처리를 관리함 
	- `@RestControllerAdvice(basePackages = "aroundhub.thinkground.studio")`와 같이 패키지 범위를 설정할 수 있음
- 예외 발생 시 json의 형태로 결과를 반환받기 위해서는 `@RestControllerAdvice`를 사용하면 됨

<br>

### @ExceptionHandler

- 예외 처리 상황이 발생하면 해당 Handler로 처리하겠다고 명시하는 어노테이션
- 어노테이션 뒤에 괄호를 붙여 어떤 ExceptionClass를 처리할 지 설정할 수 있음
	- `@ExceptionHandler(00Exception.class)`
- `Exception.class`는 최상위 클래스로 하위 세부 예외 처리 클래스로 설정한 핸들러가 존재하면, 그 핸들러가 우선처리하게 되며, 처리되지 못하는 예외 처리에 대해 ExceptionClass에서 핸들링함
- `@ControllerAdvice`로 설정된 클래스 내에서 메소드로 정의할 수 있지만, 각 Controller 안에 설정도 가능
- 전역 설정(`@ControllerAdvice`)보다 지역 설정(`Controller`)으로 정의한 Handler가 우선순위를 가짐

<br>

### 우선순위 도식화

![img](https://user-images.githubusercontent.com/84761609/229527853-29726114-32e6-446d-8a92-169709c2edcd.png)

![img](https://user-images.githubusercontent.com/84761609/229528197-79572251-6076-4f2d-ae1a-46a74089ec71.png)

<br>

<hr>

# Spring Boot 기초 - CustomException

## CustomException

### 목표하는 에러 응답 예시

- 아래와 같이 error type, error code, message를 응답함으로써 Client에 정확히 어떤 에러가 발생했는지 공유하는 것

![img](https://user-images.githubusercontent.com/84761609/229531989-b0cd148a-5916-4258-8101-77b05ab22595.png)

<br>

### 예외 클래스

![img](https://user-images.githubusercontent.com/84761609/229523310-5a5905bb-7f04-4c11-be0f-7e836c53b92f.png)

<br>

### Exception 구조

![img](https://user-images.githubusercontent.com/84761609/229532790-5cbb0b1f-56ec-4d7f-9f13-2dcafb461aad.png)

<br>

### Throwable 구조

![img](https://user-images.githubusercontent.com/84761609/229538479-f8e73dcc-2475-4d8f-a2b8-2f897984c9bf.png)

<br>

### HttpStatus

- HttpStatus는 enum 클래스임

- **Enum Class**
	- 서로 관련이 있는 상수들을 모아 심볼릭한 명칭의 집합으로 정의한 것
	- 클래스처럼 보이게 한 상수

- 주요 항목

![img](https://user-images.githubusercontent.com/84761609/229539439-b727f067-beba-4b2a-8287-dee0a6237493.png)

<br>

### Cutom Exception

- error type, error code, message에서 필요한 내용은 아래와 같음
	- error type: HttpStatus의 reasonPhrase
	- error code: HttpStatus의 value
	- message: 상황별 디테일 Message

![img](https://user-images.githubusercontent.com/84761609/229540736-aeff3f30-bfc9-4065-89da-f51272a93574.png)


<br>

<hr>

# Spring Boot 기초 - JUnit

## TDD에 대한 간단한 정리

- 테스트 주도 개발이라는 의미를 가짐
- 단순하게 표현하자면 테스트를 먼저 설계 및 구축 후 테스트를 통과할 수 있는 코드를 짜는 것
- 코드 작성 후 테스트를 진행하는 지금까지 사용된 일반적인 방식과 다소 차이가 있음
- 애자일 개발 방식 중 하나
	- 코드 설계 시 원하는 단계적 목표에 대해 설정하여 진행하고자 하는 것에 대한 결정 방향의 갭을 줄이고자 함
	- 최초 목표에 맞춘 테스트를 구축하여 그에 맞게 코드를 설계하기 때문에 보다 적은 의견 충돌을 기대할 수 있음 (방향 일치로 인한 피드백과 진행 방향의 충돌 방지)

<br>

## 테스트 코드 작성 목적

- 코드의 안정성을 높일 수 있음
- 기능을 추가하거나 변경하는 과정에서 발생할 수 있는 Side-Effect를 줄일 수 있음
- 해당 코드가 작성된 목적을 명확하게 표현할 수 있음
	- 코드에 불필요한 내용이 들어가는 것을 비교적 줄일 수 있음

<br>

## JUnit이란?

- Java 진영의 대표적인 Test Framework
- 단위 테스트(Unit Test)를 위한 도구를 제공
	- 단위 테스트란?
		- 코드의 특정 모듈이 의도된 대로 동작하는 지 테스트하는 절차를 의미
		- 모든 함수와 메소드에 대한 각각의 테스트 케이스(Test Case)를 작성하는 것
- 어노테이션(Annotation)을 기반으로 테스트를 지원
- 단정문 (Assert)으로 테스트 케이스의 기대값에 대해 수행 결과를 확인할 수 있음
- Spring Boot 2.2 버전부터 JUnit5 버전을 사용
- JUnit 5는 크게 Jupiter, Platform, Vintage 모듈로 구성됨

<br>

## JUnit 모듈 설명

### JUnit Jupiter

- TestEngine API 구현체로 JUnit 5를 구현하고 있음
- 테스트의 실제 구현체는 별도 모듈 역할을 수행하는데, 그 모듈 중 하나가 Jupiter-Engine임
- 이 모듈은 Jupiter-API를 사용하여 작성한 테스트 코드를 발견하고 실행하는 역할을 수행
- 개발자가 테스트 코드를 작성할 때 사용됨

<br>

### JUnit Platform

- Test를 실행하기 위한 뼈대
- Test를 발견하고 테스트 계획을 생성하는 TestEngine 인터페이스를 가지고 있음
- TestEngine을 통해 Test를 발견하고, 수행 및 결과를 보고함
- 그리고 각종 IDE 연동을 보조하는 역할을 수행(콘솔 출력 등)
- (Platform = TestEngine API + Console Launcher + JUnit 4 Based Runner 등)

<br>

### JUnit Vintage

- TestEngine API 구현체로 JUnit 3, 4를 구현하고 있음
- 기존 JUnit 3, 4 버전으로 작성된 테스트 코드를 실행할 때 사용됨
- Vintage-Engine 모듈을 포함하고 있음

![image](https://user-images.githubusercontent.com/84761609/229546460-ebec542e-fabf-4897-a6c9-afad98014e3c.png)

<br>

## JUnit LifeCycle Annotation

- JUnit 5는 아래와 같은 테스트 라이프 사이클을 가지고 있음

| Annotation | Description |
| --- | --- |
| `@Test` | 테스트용 메소드를 표현하는 어노테이션 |
| `@BeforeEach` | 각 테스트 메소드가 시작되기 전에 실행되어야 하는 메소드를 표현 |
| `@AfterEach` | 각 테스트 메소드가 시작된 후 실행되어야 하는 메소드를 표현 |
| `@BeforeAll` | 테스트 시작 전에 실행되어야 하는 메소드를 표현(static 처리 필요) |
| `@AfterAll` | 테스트 종료 후에 실행되어야 하는 메소드를 표현(static 처리 필요) |

<br>

## JUnit Main Annotaion

## @SpringBootTest

- 통합 테스트 용도로 사용됨
- `@SpringBootApplicaion`을 찾아가 하위의 모든 Bean을 스캔하여 로드함
- 그 후 Test용 Application Context를 만들어 Bean을 추가하고, MockBean을 찾아 교체

<br>

### @ExtendWith

- JUint 4에서 `@RenWith`로 사용되던 어노테이션이 `@ExtendWith`로 변경됨
- `@ExtendWith`는 메인으로 실행될 Class를 지정할 수 있음
- `@SpringBootTest`는 기본적으로 `@ExtendWith`가 추가되어 있음

<br>

### @WebMvcTest(Class명.class)

- ()에 작성된 클래스만 실제로 로드하여 테스트를 진행
- 매개변수를 지정해주지 않으면 `@Controller`, `@RestController`, `@RestControllerAdvice` 등 컨트롤러와 연관된 모든 Bean이 로드됨
- 스프링의 모든 Bean을 로드하는 `@SpringBootTest`대신 컨트롤러 관련 코드만 테스트할 경우 사용

<br>

### @Autowired about Mockbean

- Controller의 API를 테스트하는 용도인 MockMvc 객체를 주입 받음
- `perform()` 메소드를 활용하여 컨트롤러의 동작을 확인할 수 있음
	- `.andExpect()`, `.andDo()`, `.andReturn` 등의 메소드를 같이 활용함

<br>

### @Mockbean

- 테스트할 클래스에서 주입 받고 있는 객체에 대해 가짜 객체를 생성해주는 어노테이션
- 해당 객체는 실제 행위를 하지 않음
- `given()` 메소드를 활용하여 가짜 객체의 동작에 대해 정의하여 사용할 수 있음

<br>

### @AutoConfigureMockMvc

- `spring.test.mockmvc`의 설정을 로드하면서 MockMvc의 의존성을 자동으로 주입
- MockMvc 클래스는 REST API 테스트를 할 수 있는 클래스

<br>

### @Import

- 필요한 Class들을 Configuration으로 만들어 사용할 수 있음
- Configuration Conponent 클래스도 의존성 설정할 수 있음
- Import된 클래스는 주입으로 사용 가능

<br>

## 통합 테스트

- 통합 테스트는 여러 기능을 조합하여 전체 비즈니스 로직이 제대로 동작하는 지 확인하는 것을 의미
- 통합 테스트의 경우, `@SpringBootTest`를 사용하여 진행
	- `@SpringBootTest`는 `@SpringBootApplicaion`을 찾아가서 모든 Bean을 스캔하여 로드하게 됨
	- 이 방법을 대규모 프로젝트에서 사용할 경우, 테스트를 실행할 때마다 모든 빈을 스캔하고 로드하는 작업이 반복되어 매번 무거운 작업을 수행해야 함.

<br>

## 단위 테스트

- 단위 테스트는 프로젝트에 필요한 모든 기능에 대한 테스트를 각각 진행하는 것을 의미
- 일반적으로 스프링 부트에서는 <br> `org.springframework.boot:spring-boot-stater-test` 디펜던시만으로 의존성을 모두 가질 수 있음

### F.I.R.S.T 원칙

- Fast: 테스트 코드의 실행은 빠르게 진행되어야 함
- Independent: 독립적인 테스트가 가능해야 함
- Repeatable: 테스트는 매번 같은 결과를 만들어야 함
- Self - Validating: 테스트는 그 자체로 실행하여 결과를 확인할 수 있어야 함
- Timely: 단위 테스트는 비즈니스 코드가 완성되기 전에 구성하고 테스트가 가능해야 함
		>> 코드가 완성되기 전부터 테스트가 따라와야 한다는 TDD의 원칙을 담고 있음
