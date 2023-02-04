---
title: Spring Study Week1 - 객체지향 특징, 원칙과 IoC, DI
author: NadudAn
date:   2023-02-03 22:56:00 +0900
categories: [Study, Spring]
tag: [Spring, Study, JAVA, ERROR]
---

> 스프링 스터디 1주차 공부한 것입니다.
{: .prompt-info }

# 객체지향

현실 세계의 개체(Entity)를 기계의 부품처럼 하나의 객체(Object)로 만들어, 기계적인 부품들을 조립하여 제품을 만들듯이 소프트웨어를 개발할 때에도 객체들을 조립하여 작성할 수 있는 기법

<br>

## 4가지 특징

 - 캡슐화: 데이터와 코드 형태를 외부로부터 알 수 없게 하고, 데이터의 구조와 역할, 기능을 하나의 캡슐 형태로 만드는 방법
 - 추상화: 클래스들의 공통적인 속성(변수, 메소드 등)들을 묶어 표현하는 것
 - 상속: 부모클래스에 정의된 변수 및 메소드들을 자식 클래스에서 상속받아 사용하는 것
 - 다형성: 메시지에 의해 객체가 연산을 수행하게 될 때, 하나의 메시지에 대해 각 객체가 가지고있는 고유한 방법으로 응답할 수 있는 능력
	 - Overloading
	 - Overriding

<br>

## 5가지 원칙 SOLID

### 단일 책임 원칙(SRP: Single Responsibility Principle)
> 한 클래스는 하나의 책임만 가져야 한다.

![image](https://user-images.githubusercontent.com/84761609/216751041-4a35caa3-064c-4e81-ac1a-a0519b8568aa.png)


<br>

#### SRP가 지켜지지 않은 경우

~~~
class 강아지 { 
	final static Boolean 수컷 = true; 
	final static Boolean 암컷 = false; 
	Boolean 성별; 
	
	void 소변보다() { 
		if (this.성별 == 수컷) {
			// 한쪽 다리를 들고 소변을 보다. 
		} else { 
			// 뒷다리 두 개를 굽혀 앉은 자세로 소변을 본다. 
		} 
	} 
}
~~~

#### SRP가 지켜진 경우

~~~
abstract class 강아지 { 
	abstract void 소변보다(); 
} 

class 수컷강아지 extends 강아지 { 
	void 소변보다() { 
		// 한쪽 다리를 들고 소변을 본다. 
	} 
} 

class 암컷강아지 extends 강아지 { 
	void 소변보다() { 
		// 뒷다리 두 개로 앉은 자세로 소변을 본다. 
	} 
}
~~~

<br>

### 개방 폐쇄 원칙(OCP: Open/Closed Principle)
> 확장에는 열려있으나 변경에는 닫혀있어야 한다.

![image](https://user-images.githubusercontent.com/84761609/216751066-3e47dddc-2a6c-41da-96ce-e9ede35929d6.png)


<br>

### 리스코프 치환 원칙(LSP: Liskov's Substitution Principle)
> 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.

-   `하위 클래스 is a kind of 상위 클래스` - 하위 분류는 상위 분류의 한 종류다.
-   `구현 클래스 is able to 인터페이스`: 구현 분류는 인터페이스할 수 있어야 한다.

![image](https://user-images.githubusercontent.com/84761609/216751083-c54628e2-3691-4500-a123-8549aa449ff5.png)


<br>

### 인터페이스 분리 원칙(ISP: Interface Segregation Principle)
>특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.

 결론적으로는 단일 책임 원칙(SRP)과 인터페이스 분할 원칙(ISP)은 같은 문제에 대한 두 가지 다른 해결책이라고 볼 수 있다. 하지만 특별한 경우가 아니라면 `단일 책임 원칙`을 적용하는 것이 더 좋은 해결책이라고 할 수 있다.


![image](https://user-images.githubusercontent.com/84761609/216751098-f53410ba-819f-4d2c-a82e-c27471903eb2.png)


-   `상위 클래스는 풍성할수록 좋다.`
    -   풍성할수록 하위 클래스에게 많은 기능을 확장시켜주는 것이고, 형변환, 코드 중복을 줄여줍니다.
-   `인터페이스 내에 메소드는 최소한 일수록 좋다.`
    -   인터페이스는 하위 클래스에게 구현을 강제하도록 하는 역할입니다. 즉, 최소한의 기능만 제공하면서 하나의 역할에 집중하라는 뜻입니다.

<br>

### 의존관계 역전 원칙(DIP: Dependency Inversion Principle)
>추상화에 의존한다. 구체화에 의존하면 안된다.

고차원 모듈은 저차원 모듈에 의존하면 안 된다. 이 두 모듈 모두 다른 추상화된 것에 의존해야 한다.
추상화된 것은 구체적인 것에 의존하면 안 된다. 구체적인 것이 추상화된 것에 의존해야 한다.
자주 변경되는 구체(Concrete) 클래스에 의존하지 마라


![image](https://user-images.githubusercontent.com/84761609/216751119-51115a6c-9964-4e62-9a58-84ad55d6ca74.png)


자동차가 타이어에 의존하면 안된다. 자동차가 구체적인 타이어가 아닌 타이어 인터페이스에 의존하게 해서 어떤 타이어를 끼우든 문제가 없게해야 한다.

이처럼 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 하는 것이 의존 역전 원칙이다.

상위 클래스일수록, 인터페이스일수록, 추상 클래스일수록 변하지 않을 가능성이 높기에 하위 클래스나 구체 클래스가 아닌 상위 클래스, 인터페이스, 추상 클래스를 통해 의존하라는 것이 바로 의존 역전 원칙이다.

<br>

---

# Spring

## DI: Dependency Injection

`DI(Dependency Injection)`란 **스프링이 다른 프레임워크와 차별화되어 제공하는 의존 관계 주입 기능**으로,  
**객체를 직접 생성하는 게 아니라 외부에서 생성한 후 주입 시켜주는 방식**이다.

**DI(의존성 주입)를 통해서 모듈 간의 결합도가 낮아지고 유연성이 높아진다.**

![image](https://user-images.githubusercontent.com/84761609/216751169-983b18c4-0e7b-4af6-8c7a-6af381d0f0df.png)


첫번째 방법은 A객체가 B와 C객체를 New 생성자를 통해서 직접 생성하는 방법이고, 

두번째 방법은 **외부에서 생성 된 객체를 setter()를 통해 사용하는 방법**이다. 

이러한 두번째 방식이 의존성 주입의 예시인데,  
`A 객체`에서 **`B, C객체`를 사용(의존)할 때** `A 객체`에서 **직접 생성 하는 것이 아니라** **`외부(IOC컨테이너)`에서 생성된 `B, C객체`를 조립(주입)시켜 `setter` 혹은 `생성자`를 통해 사용하는 방식**이다.

![image](https://user-images.githubusercontent.com/84761609/216751178-912bf2dc-fb50-4de8-91f9-c2ccef6d962f.png)

**스프링에서는 객체를 `Bean`** 이라고 부르며, 프로젝트가 실행될때 사용자가 Bean으로 관리하는 객체들의 생성과 소멸에 관련된 작업을 자동적으로 수행해주는데 객체가 생성되는 곳을 스프링에서는 Bean 컨테이너라고 부른다.

<br>

## IoC: Inversion of Control

`   IoC(Inversion of Control)`란 "제어의 역전" 이라는 의미로, 말 그대로 **메소드나 객체의 호출작업을 개발자가 결정하는 것이 아니라, 외부에서 결정되는 것을 의미**한다.

`IoC`는 **제어의 역전이라고 말하며, 간단히 말해 "제어의 흐름을 바꾼다"**라고 한다.

객체의 **의존성을 역전시켜 객체 간의 결합도를 줄이고 유연한 코드를 작성**할 수 있게 하여 **가독성 및 코드 중복, 유지 보수를 편하게** 할 수 있게 한다.

기존에는 다음과 순서로 객체가 만들어지고 실행되었다.

1.  객체 생성
    
2.  의존성 객체 생성  
    _클래스 내부에서 생성_
    
3.  의존성 객체 메소드 호출 
    

하지만, 스프링에서는 다음과 같은 순서로 객체가 만들어지고 실행된다.

1.  객체 생성
    
2.  의존성 객체 주입  
    _스스로가 만드는것이 아니라 제어권을 **스프링에게 위임하여 스프링이 만들어놓은 객체를 주입**한다._
    
3.  의존성 객체 메소드 호출
    

**스프링이 모든 의존성 객체를 스프링이 실행될때 다 만들어주고 필요한곳에 주입**시켜줌으로써 **Bean들은 `싱글턴 패턴`의 특징**을 가지며, 

**제어의 흐름을 사용자가 컨트롤 하는 것이 아니라 스프링에게 맡겨 작업을 처리**하게 된다. 

