---
title: Spring Study Group Week2 - 의존관계(의존성)
author: dnya0
date:   2023-02-14 21:52:00 +0900
categories: [StudyGroup, Spring]
tag: [StudyGroup, Spring, Study, JAVA, KNU-Spring-Study]
math: true
mermaid: true
image:
  path: https://user-images.githubusercontent.com/84761609/220085479-19ee260a-d709-4a47-b788-416275d8a2d8.png
  alt: Spring Image
---

> 스프링 스터디 2주차 공부한 것입니다. [회원가입/로그인 과제 코드 보러가기](https://github.com/KNU-Spring-Study/Nayeong-Spring-Week2)
{: .prompt-info }

# 의존관계(의존성) dependency

스프링 부트에선 의존성 관리 기능이 있다.

<br>

## 의존관계

*어떤 클래스가 다른 클래스에 접근할 수 있는 경로를 가지거나 해당 클래스의 객체의 메소드를 호출하는 경우* 두 클래스에 사이에 의존관계가 있다고 말한다.

- 의존관계는 클래스 또는 *모듈이 다른 클래스 또는 모듈에 의존할 때 형성된다*.
- 의존관계 주입은 의존관계에 있는 오브젝트들을 런타임 시 연결해주는 작업이다.
- 스프링 IoC의 대표적인 동작원리는 의존관계 주입이다.
- 두 개의 클래스가 의존관계에 있다고 확정지을 때는 항상 방향성을 부여해줘야한다. 즉, 누가 누구에게 의존하는 지에 대해 방향성을 부여해야 한다.

> 코드 시점의 의존관계와 실행 시점의 의존관계가 다를 수 있다.

<br>

### 의존하고 있다는 것의 의미

어떤 객체가 동작하기 위해 다른 객체를 필요로 할 때 두 객체 사이에 의존 관계가 형성된다.
이렇게 의존 관계가 형성되면 MovieReader의 기능이 추가되거나 또는 변경되었을 때 그 영향이 MovieFinder에게 전달된다.

의존관계는 객체와 객체가 협력하기 위해서는 반드시 필요 하지만, 과도한 의존관계는 애플리케이션을 수정하기 어렵게 만든다.

객체지향의 핵심은 협력을 위해 **필요한 의존관계는 유지**하면서도 **변경을 방해하는 의존관계는 제거**하는데 있다.

이런 관점에서 **객체 지향 설계**란 **의존관계를 관리하는 것**이고 **객체가 변경을 받아들일 수 있게 의존관계를 정리하는 기술**이라고 할 수 있다.


### UserDao의 의존관계

```java
public class UserDao { 
	private ConnectionMaker connectoinmaker; 
	,,, 
}
```

UserDao가 ConnectionMaker를 의존하고 있는 형태이다.

![image](https://user-images.githubusercontent.com/84761609/218458518-b2a3cd94-95c0-48d2-afd8-32004250be94.png)

<br>

- UserDao가 ConnectoinMaker 인터페이스를 의존하고 있으므로, **ConnectionMaker 인터페이스가 변화하면 UserDao도 영향을 받을 것이다.**
- 하지만, ConnectionMaker 인터페이스를 구현한 클래스인 **DConnectionMaker를 다른 것으로 바뀌거나 내부의 메소드에서 변화가 생겨도 UserDao에는 영향을 주지 않는다.** _UserDao는 DConnectionMaker 의 존재 여부도 모른다._
- **즉, 인터페이스에 대해서만 의존관계를 만들어두면 인터페이스 구현 클래스와의 관계는 느슨해지면서 변화에 영향을 덜 받는 상태가 되는 것이다. _결합도가 낮다고 설명할 수 있는 것이다._**

<br>

**의존관계란, 한쪽의 변화가 다른 쪽에 영향을 주는 것이니, 인터페이스를 통해 의존관계를 제한해주면 그만큼 변경에 자유로워지는 셈이다.**

<br>

**런타임 시 오브젝트 의존관계**

-   모델이나 코드에서 클래스와 인터페이스를 통해 드러나는 의존관계(UserDao, ConnectionMaker) 말고, **런타임 시에 오브젝트 사이에서 만들어지는 의존관계도 있다.**
	-   인터페이스를 통해 설계 시점에 느슨한 의존관계를 갖는 경우에는 UserDao의 오브젝트가 런타임 시 사용할 오브젝트가 어떤 클래스로 만든 것인지 미리 알 수가 없다.(현재는 DConnectionMaker)
	-   **프로그램이 시작되고 UserDao 오브젝트가 만들어지고 나서 런타임 시에 의존관계를 맺는 대상, 즉 실제 사용대상인 오브젝트를 _의존 오브젝트_ 라고 말한다.**
-   **의존관계 주입**은 이렇게 **구체적인 의존 오브젝트**(UserDao)와 **그것을 사용할 주체, 보통 클라이언트라고 부르는 오브젝트**(DConnectionMaker)를 **런타임 시에 연결해주는 작업을 말한다.**

<br>

## 의존관계 주입(DI)

외부의 독립적인 존재가 객체를 생성한 후 이를 전달해서 의존 관계를 해결하는 방법을 의존 관계 주입이라고 부른다. 외부에서 의존 관계 대상을 해결한 후 이를 사용하는 객체 쪽으로 주입하기 때문이다. 

**어떤 객체가 사용하는 의존 객체를 직접 만들어 사용하는게 아니라, 주입 받아 사용하는 방법**이다. (**new 연산자**를 이용해서 객체를 생성하는 것이라고 생각하면 된다)


의존관계 주입 방법에는 아래와 같은 총 3가지의 방법이 있다.

| 의존관계 주입 방법 | 설명 |
| --- | --- |
| 생성자 주입(Constructor Injection) | 객체를 생성하는 시점에 생성자를 통한 의존관계를 주입한다. |
| 설정자 주입(Setter Injection) | 객체를 생성 후 설정자 메서드를 통한 의존관계를 주입한다. |
| 메소드 주입(Method Injection) | 메서드 실행 시 인자를 이용한 의존관계를 주입한다. |

<br>

### 의존관계 주입의 3가지 조건

1. 클래스 모델이나 코드에는 **런타임 시점의 의존관계가 드러나지 않는다.** 그러기 위해서는 **인터페이스에만 의존하고 있어야 한다.**
2.  **런타임 시점의 의존관계는 컨테이너나 팩토리 같은 제3의 존재가 결정한다.** (NConnectoinMaker 혹은 DConnectionMaker 선택)
3.  **의존관계는 사용할 오브젝트에 대한 레퍼런스를 외부에서 제공해줌으로써 만들어진다.**

**_의존관계 주입의 핵심은 설계 시점에는 알지 못했던 두 오브젝트의 관계를 맺도록 도와주는 제3의 존재가 있다는 것이다._**
 -   DI에서 말하는 **제 3의 존재**는 전략 패턴에서 등장하는 **클라이언트나 DaoFactory**, 또는 DaoFactory 같은 작업을 일반화해서 만들어진 **애플리케이션 컨텍스느, 빈 팩토리, IoC 컨테이너** 등이 **모두 외부에서 오브젝트 사이의 런타임 관계를 맺어주는 책임을 지닌 제3의 존재이다.**

<br>

#### 강한 결합

객체 내부에서 다른 객체를 생성하는 것은 강한 결합도를 가지는 구조이다. A 클래스 내부에서 B 라는 객체를 직접 생성하고 있다면, B 객체를 C 객체로 바꾸고 싶은 경우에 A 클래스도 수정해야 하는 방식이기 때문에 강한 결합이다.


#### 느슨한 결합

객체를 주입 받는다는 것은 외부에서 생성된 객체를 인터페이스를 통해서 넘겨받는 것이다. 이렇게 하면 결합도를 낮출 수 있고, 런타임시에 의존관계가 결정되기 때문에 유연한 구조를 가진다.

<br>

### UserDao의 의존관계 주입

```java
public UserDao() { 
	connectionMaker = new DconnectionMaker(); 
}
```

-   이 코드의 문제는 이미 런타임 시의 의존관계가 코드 속에 결정되어 있다는 것이다.
-   그로 인해, **IoC 방식을 써서 UserDao의 런타임 의존관계를 드러내는 코드를 제거(파라미터로 인터페이스를 받음)하고, 제3의 존재(DaoFactory)에 런타임 의존관계 결정 권한을 위임한 것이다.**


**_UserDao에 적용된 의존관계 주입 기술을 의존관계 주입 세 가지 조건으로 확인하자면_**

1.  런타임 의존관계를 드러내지 않기 위해, 인터페이스에만 의존했다.

```java
public class UserDao { 
	private ConnectoinMaker connectionMaker; 
}
```

2. 제3의 존재인 DaoFactory를 생성하여 런타임 의존관계를 결정했다.

```java
public class DaoFactory { 
	public UserDao userDao() { 
		return new UserDao(connectionMaker()); 
	} 
	
	public ConnectionMaker connectionMaker() { 
		return new DConnectionMaker(); 
	} 
}
```

3. 의존관계는 사용할 오브젝트에 대한 레퍼런스를 외부에서 제공 받았다.

```java
public class UserDao { 
	private ConnectoinMaker connectionMaker; 
	
	//connectionMaker 정보를 외부에서 받음. 
	public UserDao(ConnectionMaker connectionMaker) { 
		this.connectionMaker = connectionMaker; 
	} 
}
```

-  DaoFactory는 **_두 오브젝트 사이의 런타임 의존관계를 설정해주는 의존관계 주입 작업을 주도하는 존재(DConectionMaker 혹은 NConnectionMaker 구현체를 의존관계로 전달)_**이며, **_동시에 IoC 방식으로 오브젝트의 생성과 초기화, 제공(new, 파라미터 제공) 등의 작업을 수행하는 컨테이너_**이다.
-  **이로인해, 여기서 DI 컨테이너는 DaoFactory가 되는 것이다.**
-  DI 컨테이너는 자신이 결정한 의존관계를 맺어줄 클래스의 오브젝트를 만들고 이 생성자의 파라미터로 오브젝트의 레퍼런스를 전달해준다.
	-  Ex. UserDao를 생성하는 시점에 생성자의 파라미터로 이미 만들어진 DConnectionMaker 오브젝트의 레퍼런스를 전달한다.

```java
//Ex 작업을 수행하기 위한 필수적인 코드
public class UserDao { 
	private ConnectoinMaker connectionMaker; 
	
	//connectionMaker 정보를 외부에서 받음. 
	public UserDao(ConnectionMaker connectionMaker) { 
		this.connectionMaker = connectionMaker; 
	} 
}
```

-  **이런 과정을 통해 두 개의 오브젝트 간(USerDao, ConnectionMaker)에 런타임 의존관계가 만들어진 것이다.**
-  해당 그림은 **런타임 의존관계 주입** 과 그것으로 발생하는 **런타임 사용 의존관계** 의 모습을 보여준다.

![image](https://user-images.githubusercontent.com/84761609/218477351-0a25ce89-d32c-4076-9691-a86a939f2d08.png)

**DI는 자신이 사용할 오브젝트에 대한 선택과 생성 제어권을 외부로 넘기고, 자신은 그저 주입받은 오브젝트를 사용한다는 점에서 IoC의 개념에 잘 들어맞는다. 이로인해, 스프링 컨테이너의 IoC는 주로 의존관계 주입 또는 DI라는 데 초점이 맞춰져 있다.**

<br>

### **의존관계 검색과 주입**

-   스프링이 제공하는 IoC 방법에는 의존관계 주입만 있는 것이 아니다. **의존관계를 맺는 방법이 외부로부터의 주입이 아니라 스스로 검색을 이용하기 때문에 _의존관계 검색_이라고 불리는 것도 있다.**
-   의존관계 검색은 런타임 시 의존관계를 맺을 오브젝트를 결정하는 것과 오브젝트의 생성 작업은 외부 컨테이너에게 IoC로 맡기지만, **이를 가져올 때는 메소드나 생성자를 통한 주입 대신 _스스로 컨테이너에게 요청하는 방법_을 사용한다.**
-   Ex. UserDao 생성자를 아래와 같이 작성해보자

```
public UserDao() { 
	DaoFactory daoFactory = new DaoFactory(); 
	this.connectionMaker = daoFactory.connectionMaker(); 
}
```

- 이렇게 작성을 해도 UserDao는 여전히 자신히 어떤 ConnectionMaker 오브젝트를 사용할지 미리 알지 못한다. 또한, 여전히 코드의 의존대상은 ConnectionMaker 인터페이스 뿐이다.
-   런타임 시에 DaoFactory가 만들어서 돌려주는 오브젝트와 런타임 의존관계를 맺으므로, IoC 개념을 잘 따르고 있다.
-   **하지만, 이 소스는 외부로부터의 주입이 아니라 스스로 IoC 컨테이너인 DaoFactory에게 요청하는 것이다.**
    -   이 경우를 **스프링의 애플리케이션 컨텍스트라면 미리 정해놓은 이름을 전달받아 그 이름에 해당하는 오브젝트를 찾게된다.**
    -   또한, **그 대상이 런타임 의존관계를 가질 오브젝트이므로 의존관계 검색이라고 부르는 것이다.**

<br>

### **_스프링의 의존관계 검색_**

-   스프링의 IoC 컨테이너인 애플리케이션 컨텍스트는 **getBean()**이라는 메소드를 제공한다. **바로 이 메소드가 의존관계 검색에 사용되는 것이다.**
-   의존관계 검색을 이용한 UserDao 생성자를 확인해보자

```java
public UserDao() { 
	ApplicationContext context = 
			new AnnotationConfigApplicationContext(DaoFactory.class);
	this.connectionMaker = 
			context.getBean("connectionMaker", ConnectionMaker.class); 
}
```

-   **의존관계 검색은 기존 의존관계 주입의 모든 장점을 가지고 있다. 하지만 코드상으로 봤을 때는 의존관계 주입 쪽이 훨씬 단순하고 깔끔하다.** _즉, 의존관계 주입을 사용하는 것이 더 낫다._
-   그런데 가끔, 의존관계 검색 방식을 사용할 때가 있다.
    -   바로 main()에서 애플리케이션 기동 시점에서는 적어도 한 번 의존관계 검색 방식을 사용하여 오브젝트를 가져와야 한다.

<br>

### **_의존관계 검색(DL)과 의존관계 주입(DI) 적용 시 중요한 차이점_**

-   **의존관계 검색 방식에서는 검색하는 오브젝트(UserDao)는 자신이 스프링의 Bean일 필요가 없다.**
    -   Ex. UserDao에서 스프링의 getBean()을 사용하여 의존관계 검색 방법을 사용하면 getBean()을 통해 검색되어야 하는 ConnectionMaker만 Bean으로 등록되어 있으면 된다.
-   **의존관계 주입 방식에서는 UserDao와 ConnectionMaker 사이에 DI가 적용되려면 UserDao도 반드시 Bean으로 등록되어야 한다.**
    -   컨테이너가 UserDao에 ConnectionMaker 오브젝트를 주입해주려면 UserDao에 대한 생성과 초기화 권한을 갖고 있어야함으로 Bean으로 등록되어야 한다.
-   **DI를 원하는 오브젝트는 먼저 자기 자신이 컨테이너가 관리하는 Bean이 되어야 한다는 점을 잊지 말자.**

### **의존관계 주입의 응용**

-   DI 기술의 장점은 무엇일까?
    -   DaoFactory 방식이 DI 방식을 구현한 것이니, 해당 장점을 그대로 이어받는다.
        1.  코드에는 런타임 클래스에 대한 의존관계가 나타나지 않는다.
        2.  인터페이스를 통해 결합도가 낮은 코드를 만든다.
        3.  다른 책임을 가진 객체(여러 ConnectionMaker)가 바뀌거가 변경되더라고 자신(UserDao)은 영향을 받지 않는다.
        4.  변경을 통한 다양한 확장 방법이 자유롭다.

-   **UserDao가 ConnectionMaker라는 인터페이스에만 의존한다는 것은, 어떤 객체든 ConnectionMaker를 구현하기만 하고 있다면 어떤 오브젝트든지 사용할 수 있다는 뜻이다.**


#### **메소드를 이용한 의존관계 주입**

-   지금까지 살펴본 의존관계 주입은 생성자를 통해 주입을 했는데, 꼭 생성자를 사용해야 하는 것은 아니다. **생성자가 아닌 일반 메소드를 이용해 의존 관계를 주입할 수 있는데 그 방법을 살펴보자.**

_수정자(Setter) 메소드를 이용한 주입_

-   수정자 메소드는 외부에서 오브젝트 내부의 Attribute값을 변경하려는 용도로 자주 사용된다.
-   수정자 메소드는 **외부로부터 제공받은 오브젝트 레퍼런스를 저장해뒀다가, 내부의 메소드에서 사용하게 하는 DI 방식에서 활용하기에 적당하다.**

_일반 메소드를 이용한 주입_

-   수정자 메소드처럼 메소드 이름이 set으로 시작되어야하고, 한 번에 한 개의 파라미터만 가질 수 있다는 제약 대신, **일반 메소드를 사용하여 DI용을 사용**할 수 있다.
-   **임의의 초기화 메소드를 이용하는 DI를 사용하면 적절한 개수의 파라미터를 가진 여러 개의 초기화 메소드를 만들 수 있어, 필요한 모든 메소드를 한 번에 받아야 하는 생성자보다 낫다.**

-   UserDao도 수정자 메소드 DI 방식을 사용하도록 해보자

```java
public class UserDao { 
	private ConnectionMaker connectionMaker; 
	public void setConnectionMaker(ConnectionMaker connectionMaker) { 
		this.connectionMaker = connectionMaker; 
	} 
} 

//Factory 
@Bean 
public UserDao userDao() { 
	//return new UserDao(connectionMaker());
	UserDao userDao = new UserDao(); 
	userDao.setConnectionMaker(connectionMaker()); 
	return userDao; 
}
```
