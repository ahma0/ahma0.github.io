---
title: 모던 자바 인 액션 - 03. 람다 표현식
author: ahma0
date:   2023-09-16 15:25:00 +0900
categories: [Study, 모던 자바 인 액션]
tag: [Study, 모던 자바 인 액션]
math: true
mermaid: true
image:
  path: https://github.com/ahma0/ahma0.github.io/assets/84761609/be791143-bd96-46a4-b9b4-dd6ed60b3ccf
  alt: book image
---

> [모던 자바 인 액션](https://www.yes24.com/Product/Goods/77125987)을 읽고 정리한 글입니다.
{: .prompt-info }

<br>

## 람다란 무엇인가?

**람다 표현식**은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이라고 할 수 있다.

- 익명
    - 보통의 메서드와 달리 이름이 없으므로 **익명**이라 표현한다.
    - 구현해야 할 코드에 대한 걱정거리가 줄어든다.
- 함수
    - 람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다.
    - 하지만 메서드처럼 파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트를 포함한다.
- 전달
    - 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다.
- 간결성
    - 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없다.

람다 표현식을 이용하면 익명 클래스 등 판에 박힌 코드를 구현할 필요가 없다. 결과적으로 코드가 간결하고 유연해진다. 예를 들어 커스텀 Comparator 객체를 기존보다 간단하게 구현할 수 있다.

- 기존 코드

```java
Comparator<Apple> byWeight = new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
};
```

- 람다

```java
Comparator<Apple> byWeight = 
    (Apple a1, Apple a2) -> a1.getWeight.compareTo(a2.getWeight());
```

<br>

람다 표현식은 파라미터, 화살표, 바디로 이루어진다.

![](https://github.com/ahma0/ahma0/assets/84761609/59ad7b51-f7ac-48ea-9920-6be251777629)

- 파라미터 리스트
    - Comparator의 compare 메서드 파리미터(사과 2개)
- 화살표
    - 화살표 `->`는 람다의 파라미터 리스트와 바디를 구분한다.
- 람다 바디
    - 두 사과의 무게를 비교한다.
    - 람다의 반환값에 해당하는 표현식이다.

<br>

다음은 자바8의 유효한 람다 표현식이다.

```java
(String s) -> s.length() 
// String 형식의 파라미터 하나를 가지며 int를 반환한다.
// 람다 표현식에는 return이 함축되어 있으므로 
// return문을 명시적으로 사용하지 않아도 된다.

(Apple a) -> a.getWeight() > 150
// Apple 형식의 파라미터 하나를 가지며 boolean을 반환한다.

(int x, int y) -> {
    System.out.print("Result: ");
    System.out.println(x + y);
}
// int 형식의 파하미터 2개를 가지며 리턴값이 없다.(void)
// 이 예제에서 볼 수 있듯이 람다 표현식은 여러 행의 문장을
// 포함할 수 있다.

() -> 42 // 파라미터가 없으며 int 42를 반환한다.

(Apple a1, Apple a2) -> 
    a1.getWeight().compareTo(a2.getWeight());
// Apple 형식의 파라미터 2개를 가지며 int를 반환한다.
```

<br>

- 람다의 기본 문법
    - 표현식 스타일 expression style
        - `(parameters) -> expression`
    - 블록 스타일 block-style
        - `(parameters) -> { statements; }`

<br>

## 어디에, 어떻게 람다를 사용할까?

### 함수형 인터페이스

2장에서 만든 `Predicate<T>` 인터페이스로 메서드를 파라미터화할 수 있었다. 바로 `Predicate<T>`가 함수형 인터페이스이다. `Predicate<T>`는 오직 하나의 추상 메서드만 지정하기 때문이다.

```java
public interface Predicate<T> {
    boolean test(T t);
}
```

**함수형 인터페이스**는 정확히 하나의 추상 메서드를 지정하는 인터페이스다. 지금까지 살펴본 자바 API의 함수형 인터페이스로 Comparator, Runnable 등이 있다.

```java
public interface Comparator<T> {
    int compare(T o1, T o2);
}

public interface Ruunable {
    void run();
}

public interface ActionListener extends EventListener {
    void actionPerformed(ActionEvent e);
}

public interface Callable<V> {
    V call() throws Exception;
}

public interface PrivilegedAction<T> {
    T run();
}
```

> 인터페이스는 **디폴트 메서드**를 포함할 수 있다. 많은 디폴트 메서드가 있더라도 **추상 메서드가 오직 하나**면 함수형 인터페이스다.<br>디폴트 메서드: 인터페이스의 메서드를 구현하지 않은 클래스를 고려해서 기본 구현을 제공하는 바디를 포함하는 메서드

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로 **전체 표현식을 함수형 인터페이스의 인스턴스로 취급**(기술적으로 따지면 함수형 인터페이스를 **구현한** 클래스의 인스턴스)할 수 있다. 익명 내부 클래스로도 같은 기능을 구현할 수 있다.

```java
// 람다 사용
Runnable r1 = () -> System.out.println("Hello World 1");

// 익명 클래스 사용
Runnable r2 = new Runnable() {
    public void run() {
        System.out.println("Hello World 2");
    }
};

public static void process(Runnable r) {
    r.run();
}

// Hello World 1 출력
process(r1);
// Hello World 2  출력
process(r2);
// 직접 전달된 람다 표현식으로 'Hello World 3' 출력
process(() -> System.out.println("Hello World 3")); 
```

<br>

### 함수 디스크립터

함수형 인터페이스의 추상 메서드 시그니처<sup>signature</sup>는 람다 표현식의 시그니처를 가리킨다. 람다 표현식의 시그니처를 서술하는 메서드를 **함수 디스크립터**<sup>function descriptor</sup>라고 부른다.

예를 들어 Runnable 인터페이스의 유일한 추상 메서드 run은 인수와 반환값이 없으므로 Runnable 인터페이스는 인수와 반환값이 없는 시그니처로 생각할 수 있다.

<br>

#### 람다와 메소드 호출

조금 이상해보일 수 있지만 아래는 정상적인 람다 표현식이다.

```java
process(() -> System.out.println("This is awesome"));
```

위 코드에서는 중괄호를 사용하지 않았고 `System.out.println`은 void를 반환하므로 완벽한 표현식이 아닌 것처럼 보인다. 그럼 중괄호로 감싸면 어떨까?

```java
process(() -> { System.out.println("This is awesome"); });
```

결론적으로 중괄호는 필요 없다. 자바 언어 명세에서는 void를 반환하는 메소드 호풀과 관련한 특별한 규칙을 정하고 있기 때문이다. 즉 한 개의 void 메소드 호출은 중괄호로 감쌀 필요가 없다.

<br>

#### `@FunctionalInterface`는 무엇인가

`@FunctionalInterface`는 함수형 인터페이스임을 가리키는 어노테이션이다. `@FunctionalInterface`로 인터페이스를 선언했지만 실제로 함수형 인터페이스가 아니면 컴파일러가 에러를 발생시킨다.

<br>

## 람다 활용 : 실행 어라운드 패턴

자원 처리에 사용하는 순환 패턴<sup>recurrent pattern</sup>은 자원을 열고, 처리한 다음에, 자원을 닫는 순서로 이루어진다. 설정<sup>setup</sup>과 정리<sup>cleanup</sup>과정은 대부분 비슷하다. 즉, 실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 갖는다. 이런 형식의 코드를 **실행 어라운드 패턴**<sup>execute around pattern</sup>이라고 부른다.

```java
public String processFile() throws IOException{
    // try-with-resource 구문을 사용하면 자원을 명시적으로
    //닫을 필요가 없어 간결한 코드를 구현하는 데 도움을 준다.
    try(BufferedReader br = 
        new BufferedReader(new FileReader("data.txt"))) {
        return br.readLine(); // 실제 작업을 하는 행
    }
}
```

중복되는 준비 코드와 정리 코드가 작업 A와 작업 B를 감싸고 있다.

![](https://github.com/ahma0/ahma0/assets/84761609/cdd3e54b-ea6e-42a4-9d25-17771a152dab)

<br>

### 1단계 : 동작 파라미터화를 기억하라

현재 코드는 한 번에 한 줄만 읽을 수 있다. 한 번에 두 줄을 읽거나 가장 자주 사용되는 단어를 반환하려면 어떻게 해야할까? 기존의 설정, 정리 과정은 재사용하고 processFile 메서드만 다른 동작을 명령할 수 있다면 좋을것이다. processFile의 동작을 파라미터화하자. processFile 메소드가 BufferedReader를 이용해서 다른 동작을 수행할 수 있도록 processFile 메서드로 동작을 전달해야 한다.

람다를 이용해서 동작을 전달하자. processFile 메서드가 한 번에 두 행을 읽게 하려면 BufferedReader를 인수로 받아서 String을 반환하는 람다가 필요하다.

```java
String result = processFile((BufferedReader br) ->
                        br.readLine() + br.readLine());
```

<br>

### 2단계 : 함수형 인터페이스를 이용해서 동작 전달

함수형 인터페이스 자리에 람다를 사용할 수 있다. BufferedReader -> String과 IOException을 던질 수 있는 시그니처와 일치하는 함수형 인터페이스를 만들어야 한다.

```java
@Functional
public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException;
}
```

정의한 인터페이스를 processFile 메서드의 인수로 전달할 수 있다.

```java
public String processFile(BufferedReaderProcess p) throws IOException {
    ...
}
```

<br>

### 3단계 : 동작 실행

람다 표현식으로 함수형 인터페이스의 인스턴스의 추상 메서드 구현을 직접 전달할 수 있으며 전달된 코드는 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리한다. 따라서 ProcessFile 바디 내에서 BufferedReaderProcessor 객체의 process를 호출할 수 있다.

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
    try (BufferedReader br = 
        new BufferedReader(new FileReader("data.txt"))) {
        return p.process(br);
    }
}
```

<br>

### 4단계 : 람다 전달

한 행을 처리하는 코드

```java
// 한 행을 처리하는 코드
String oneLine = 
    processFile((BufferedReader br) -> br.readLine());

// 두 행을 처리하는 코드
String twoLine = 
    processFile((BufferedReader br) -> 
        br.readLine() + br.readLine())
```

<br>

## 함수형 인터페이스 사용

다양한 람다 표현식을 사용하려면 공통의 함수 디스크립터를 기술하는 함수형 인터페이스 집합이 필요하다.

<br>

### Predicate

