---
title: Spring Study Group Week5 - GET, POST, PUT, DELETE, Entity, Repository, DAO, DTO, ORM, JPA
author: ahma0
date:   2023-03-27 13:56:00 +0900
categories: [StudyGroup, Spring]
tag: [StudyGroup, Spring, Study, JAVA, KNU-Spring-Study]
math: true
mermaid: true
image:
  path: https://user-images.githubusercontent.com/84761609/220085479-19ee260a-d709-4a47-b788-416275d8a2d8.png
  alt: Spring Image
---

> 스프링 스터디 5주차 공부한 것입니다. [어라운드 허브 스튜디오의 스프링 강의](https://www.youtube.com/playlist?list=PLlTylS8uB2fBOi6uzvMpojFrNe7sRmlzU) 를 듣고 작성하였습니다.
{: .prompt-info }

<br>

# Spring Boot 기초 - GET API

## @RequestMapping

- value와 method로 정의하여 API를 개발하는 방식
- 이제는 고전적인 방법으로 사용하지 않음

```java
// http://localhost:8080/api/v1/get-api/hello
@RequestMapping(value="/hello", method=RequestMethod.GET)
public String getHello() {
	return "HelloWorld";
}
```

<br>

## @GetMapping

- 별도의 파라미터 없이 GET API를 호출하는 경우 사용되는 방법

```java
// http://localhost:8080/api/v1/get-api/name
@GetMapping(value="/name")
public String getName() {
	return "Flature";
}
```

<br>

## @PathVariable

- GET 형식의 요청에서 파라미터를 전달하기 위해 URL에 값을 담아 요청하는 방법
- 아래 방식은 `@GetMapping`에서 사용된 `{변수}`의 이름과 메소드의 매개변수와 일치시켜야 함

```java
@GetMapping(value="/variable1/{valiable}")
public String getVariable1(@PathVariable String variable) {
	return variable;
}
```

- 아래 방식은 `@GetMapping`에서 사용된 `{변수}`의 이름과 메소드의 매개변수가 다를 경우 사용하는 방식
- 변수의 관리의 용이를 위해 사용되는 방식

```java
@GetMapping(value="/variable2/{variable}")
public String getVariable2(@PathVariable("variable") String var) {
	return var;
}
```

<br>

## RequestParam

- Get 형식의 요청에서 쿼리 문자열을 전달하기 위해 사용되는 방법
- `?`를 기준으로 우측에 `{키} = {값}`의 형태로 전달되며, 복수 형태로 전달할 경우 `&`를 사용함

`http://localhost:8080/api/v1/get-api/request1?name=flature&email=thinkground.flature@Gmail.com&organization=thinkground`

```java
@GetMapping(value="/request1")
public String getRequestParam1(
						@RequestParam String name, 
						@RequestParam String email, 
						@RequestParam String organization) {
	return name + " " + email + " " + organization;
}
```

- 아래 예시 코드는 어떤 요청 값이 들어올 지 모를 경우 사용하는 방식

```java
@GetMapping(value="/request2")
public String getRequestParam2(@RequestParam Map<String, String> param) {
	StringBuilder sb = new StringBuilder();
	
	param.entrySet().forEach(map -> {
		sb.append(map.getKey() + ": " + map.getValue() + "\n");
	});
	
	return sb.toString();
}
```

<br>

## DTO 사용

- GET 형식의 요청에서 쿼리 문자열을 전달하기 위해 사용되는 방법
- key와 value가 정해져있지만, 받아야할 파라미터가 많을 경우 DTO 객체를 사용한 방식

```java
@GetMapping(value="/request3")
public String getRequestParam3(MemberDTO memberDTO) {
	//return memberDTO.getName + " " 
				+ memberDTO.getEmail + " " 
				+ memberDTO.Organization;
	
	return memberDTO.toString;
}
```

```java
public class MemberDTO {
	private String name;
	private String email;
	private String organization;
	
	...
}
```

<br>

<hr>

# Spring Boot 기초 - POST API

## Post API

- 리소스를 추가하기 위해 사용되는 API
- @PostMapping: POST API를 제작하기 위해 사용되는 어노테이션(Annotation) <br>                          `@ReauestMapping` + POST method의 조합
- 일반적으로 추가하고자 하는 Resource를 http body에 추가하여 서버에 요청
- 그렇기 때문에 `@RequestBody`를 이용하여 body에 담겨있는 값을 받아야 함

```java
@PostMapping(value="/member")
public String postMember(@RequestBody Map<String, Object> postData) {
	StringBuilder sb = new StringBuilder();
	
	postData.entrySet()forEach(map -> {
		sb.append(map.getKey() + ": " + map.getValue() + "\n");
	});
	
	return sb.toString();
}
```

- key와 value가 정해져있지만 받아야할 파라미터가 많을 경우 DTO 객체를 사용한 방식

```java
@PostMapping(value="/member2")
public String postMemberDTO(@RequestBody MemberDTO memberDTO) {
	return memberDTO.toString();
}
```

<br>

<hr>

# Spring Boot 기초 - PUT, DELETE API

## Put API

- 해당 리소스가 존재하면 갱신하고, 리소스가 없을 경우에는 새로 생성해주는 API
- 업데이트를 위한 메소드
- 기본적인 동작 방식은 Post API와 동일

<br>

## DELETE API

- 서버를 통해 리소스를 삭제하기 위해 사용되는 API
- 일반적으로 `@PathVariable`을 통해 리소스 ID 등을 받아 처리

<br>

## ResponseEntity

- Spring Framework에서 제공하는 클래스 중 HttpEntity라는 클래스를 상속받아 사용하는 클래스
- 사용자의 HttpRequest에 대한 응답 데이터를 포함

- 포함하는 클래스
	- HttpStatus
	- HttpHeader
	- HttpBody

<br>

<hr>

# Spring Boot 기초 - Entity, DAO, Repository, DTO

## Spring Boot 서비스 구조

![image](https://user-images.githubusercontent.com/84761609/227774602-77d27b71-fa66-4696-93a9-a511630785e9.png)

<br>

## Entity

### Entity(Domain)

- 데이터베이스에 쓰일 컬럼과 여러 엔티티 간의 연관관계를 정의
- 데이터베이스의 테이블을 하나의 엔티티로 생각해도 무방함
- 실제 데이터베이스의 테이블과 1:1로 매핑됨
- 이 클래스의 필드는 각 테이블 내부의 컬럼(Column)을 의미

<br>

## DAO

### Repository

- Entity에 의해 생성된 데이터베이스에 접근하는 메소드를 사용하기 위한 인터페이스
- Service와 DB를 연결하는 고리의 역할을 수행
- 데이터베이스에 적용하고자 하는 CRUD를 정의하는 영역

<br>

### DAO (Data Access Object)

- 데이터베이스에 접근하는 객체를 의미 (Persistance Layer)
- Service가 DB에 연결할 수 있게 해주는 역할
- DB를 사용하여 데이터를 조회하거나 조작하는 기능을 전담

<br>

## DTO

### DTO(Data Transfer Object)

- DTO는 VO(Value Object)로 불리기도 하며, 계층간 데이터 교환을 위한 객체를 의미
- VO의 경우 Read Only의 개념을 가지고 있음

<br>

<hr>

# Spring Boot 기초 - ORM과 JPA

## ORM(Object Relational Mapping)

### ORM이란?

- **어플리케이션의 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑해주는 것을 의미**
	- Java의 데이터 클래스와 관계형 데이터베이스의 테이블을 매핑
- 객체지향 프로그래밍과 관계형 데이터베이스의 차이로 발생하는 제약사항을 해결해주는 역할을 수행
- 대표적으로 JPA, Hibernate 등이 있음 (Persistent API)

![image](https://user-images.githubusercontent.com/84761609/227775564-9dd8bbfe-1c7a-4441-8cbd-6998627fb90b.png)

<br>

### ORM의 장점

- **SQL 쿼리가 아닌 직관적인 코드로 데이터를 조작할 수 있음**
	- **개발자가 보다 비즈니스 로직에 집중할 수 있음**
- **재사용 및 유지보수가 편리**
	- **ORM은 독립적으로 작성되어 있어 재사용이 가능**
	- **매핑 정보를 명확하게 설계하기 때문에 따로 데이터베이스를 볼 필요가 없음**
- DBMS에 대한 종속성이 줄어듬
	- DBMS를 교체하는 작업을 비교적 적은 리스크로 수행 가능

<br>

### ORM의 단점

- 복잡성이 커질 경우 ORM만으로 구현하기 어려움
	- 직접 쿼리를 구현하지 않아 복잡한 설계가 어려움
- 잘못 구현할 경우 속도 저하 발생
- 대형 쿼리는 별도의 튜닝이 필요할 수 있음

<br>

## JPA (Java Persistance API)

### JPA란?

- JPA는 Java Persistance API의 줄임말이며, ORM과 관련된 인터페이스의 모음
- Java 진영에서 표준 ORM으로 채택되어 있음
- ORM이 큰 개념이라고 하면, JPA는 더 구체화시킨 스펙을 포함하고 있음

<br>

### Hibernate

- ORM Framework 중 하나
- JPA의 실제 구현체 중 하나이며, 현재 JPA 구현체 중 가장 많이 사용됨

![image](https://user-images.githubusercontent.com/84761609/227776089-2116fb16-218c-452e-8da6-8d7f4cd51007.png)

<br>

### Spring Data JPA

- Spring Framework에서 JPA를 편리하게 사용할 수 있게 지원하는 라이브러리
	- CRUD 처리용 인터페이스 제공
	- Repository 개발 시 인터페이스만 작성하면 구현 객체를 동적으로 생성해서 주입
	- 데이터 접근 계층 개발 시 인터페이스만 작성해도 됨
- Hibernate에서 자주 사용되는 기능을 조금 더 쉽게 사용할 수 있게 구현

![img](https://user-images.githubusercontent.com/84761609/227843669-d5e02915-422b-432f-accc-f16440403c04.png)


<br>

