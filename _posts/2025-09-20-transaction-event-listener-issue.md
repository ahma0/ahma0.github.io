---
title: TransactionalEventListener 사용 시 DB Insert가 나가지 않는 이슈
author: dnya0
date: 2025-09-20 00:37:00 +0900
categories: [Study, Spring]
tag: [Study, Spring, Java, Event]
math: true
mermaid: true
image:
  path: https://user-images.githubusercontent.com/84761609/220085479-19ee260a-d709-4a47-b788-416275d8a2d8.png
  alt: Spring Image
---

## 🥑 들어가며

토이 프로젝트의 MVP를 개발하고 기능 추가를 하던 중 새로운 Issue가 생겼다. 분명 이벤트를 발행해서 로직이 실행된 것을 확인하였는데 DB Insert문이 실행되지 않는 것이었다. `TransactionalEventListener`에 대해 제대로 알지 못하고 사용했던 것 같아서 이에 대해 공부해보기로 했다.

<br>

## 📌 Event와 Transactional

이전에 AOP와 Transactional의 관계에 대해 적었던 것처럼, Transactional은 외부에서 접근해야 트랜잭션이 걸린다. 그렇다면, 만약 이벤트를 발행했을 때 이 이벤트가 트랜잭션 로직 내에 있다면? 그리고 이벤트가 Transactional이 걸린 로직을 실행해야한다면 어떤 식으로 동작할까??

### ✔️ Spring AOP와 Spring Event와의 관계

Spring AOP는 간단히 말해서 **여러 모듈에 흩어진 공통 관심사(로깅, 트랜잭션 관리 등)를 분리하여 Aspect라는 단위로 묶는 것**이고, Spring Event는 **객체 간의 의존성을 낮추고 낮은 결합도를 유지**하면서 부가적인 로직을 처리하기 위해 사용되는 메커니즘이다. 

AOP는 코드 변경 없이 기존 기능에 추가 동작을 주입하고, Event는 특정 이벤트가 발생했을 때 그 이벤트를 발행하고 구독하는 방식으로 동작한다.

- AOP
  - 횡단 관심사(로깅, 트랜잭션 등) 분리 -> 비즈니스 로직과 분리
  - 메서드 실행 지점을 가로채서 삽입 (@Around, @Before, etc.)
  - 대상 클래스의 특정 포인트에 **강하게 결합**
  - 런타임 시, 프록시 기반으로 삽입
  - 프록시 패턴(Proxy Pattern) 기반
- Event
  - 비동기/비결합 이벤트 처리 -> 주 로직과 분리
  - 이벤트 발행-리스너 구조로 분리 (ApplicationEventPublisher, @EventListener)
  - 발행자-리스너 간 **느슨한 결합**
  - 런타임 시, 이벤트 발생 후 리스너에 전달
  - 관찰자 패턴(Observer Pattern) 기반

**AOP를 사용해야하는 때**

- 여러 메서드에 반복적으로 적용되는 로직이 있을 때
  - 예: 로깅, 트랜잭션 처리, API 타이머, 인증 체크 등
- 메서드 실행 시점 전후로 무언가 실행해야 할 때
  - 예: 실행 전 파라미터 검증, 실행 후 결과 로깅
- 비즈니스 로직과 분리된 **기술적 처리**를 할 때
  - 예: Redis 캐싱, 접근제어 검사 등

**Event를 사용해야하는 때**

- 하나의 동작 이후, 후속 처리를 분리하고 싶을 때
  - 예: 회원가입 → 웰컴 이메일 발송, 추천인 포인트 적립
- 여러 곳에서 동일 이벤트를 처리할 필요가 있을 때
  - 예: 주문 이벤트 발생 → 포인트 적립 & 알림 발송 (여러 리스너로 나눔)
- 비동기/트랜잭션 종료 후 후속 작업이 필요할 때
  - 예: DB 저장 후 외부 API 호출
- 결합도를 줄이고 확장 가능한 구조를 원할 때
  - 예: 이벤트만 던지고, 누가 처리하든 상관없게 만들고 싶을 때

<br>

## 📌 TransactionEventListener란?

EventListener에 **트랜잭션의 경계에 따라 동작 시점을 제어할 수 있는 기능**을 추가한 것이다. 일반 이벤트 리스너는 트랜잭션 내에서 이벤트를 발행한다 하더라도 트랜잭션과 무관하게 즉시 실행된다. 따라서 트랜잭션이 실패해도 이벤트는 이미 발행되었기 때문에 **데이터 불일치**가 일어날 수 있다.

반면에 TransactionEventListener는 스프링의 트랜잭션 관리 기능과 통합되어 트랜잭션 상태를 기준으로 이벤트 발생시킬 수 있다.

### ⚙️ 옵션

 - AFTER_COMMIT (기본값) - 트랜잭션이 성공적으로 마무리(commit)됬을 때 이벤트 실행
 - AFTER_ROLLBACK – 트랜잭션이 rollback 됬을 때 이벤트 실행
 - AFTER_COMPLETION – 트랜잭션이 마무리 됐을 때(commit or rollback) 이벤트 실행
 - BEFORE_COMMIT - 트랜잭션의 커밋 전에 이벤트 실행

### 🤔 왜 DB접근이 안됐던걸까?

나의 경우엔 조회로직 -> 퍼블리싱 -> 리스너 -> DB 호출의 순서였다. DB에서 데이터를 조회한 후 없을 경우 openAPI로 데이터를 조회한 후 이벤트를 발행, 사용자에게 응답해주는 방식이었다. 조회 로직이었기 때문에 트랜잭션을 걸지 않았으나, 이 때문에 리스너가 제대로 동작되지 않았던 것! 나의 리스너엔 `@TransactionEventListener`가 걸려있었기 때문에.

따라서 **이벤트를 발행하는 메소드에 트랜잭션이 없으니 커밋될 일도 없었고** AFTER_COMMIT으로 설정되어 리스너가 동작하지 않았던 것!

#### 당시 로직

```kotlin
class SearchService(
    fun hasWordInDictionary(word: String, wordJamo: List<String>): Boolean {
        //some logics...

        if (flag) {
            publisher.publishEvent(NewWordEvent(word, wordJamo, isKorean))
        }
        return flag
    }
}


@Component
class NewWordEventListener(
    private val wordTransactionalService: WordTransactionalService
) {
    private val log = logger()

    @Async
    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    fun processNewWordEvent(event: NewWordEvent) {
        wordTransactionalService.save(event.word, event.jamo, event.isKorean)
    }
}
```

#### 해결 방법

TransactionEventListener를 제거하고 `@EventListener`로 변경하였다.

```kotlin
@Component
class NewWordEventListener(
    private val wordTransactionalService: WordTransactionalService
) {
    private val log = logger()

    @Async
    @EventListener
    fun processNewWordEvent(event: NewWordEvent) {
        wordTransactionalService.save(event.word, event.jamo, event.isKorean)
    }
}
```
<br>
<br>

## 🤔 Event를 발행해서 접근해도 외부에서 접근한 것으로 판단될까?

그렇다면 궁금해지는 것이다.
내부 로직이 Transactional일 경우에 이벤트를 publish 했다고 **외부에서 접근한 것으로 판단**할까? 이에 대해 다른 이들과 의논하다가 테스트를 해보기로 하였다.

### 💻 테스트

Listener에 Transaction을 걸지 않고 다른 클래스에 Transaction이 걸린 메서드를 호출하는 로직이다.

```java

//controller
@RestController
@RequiredArgsConstructor
public class TransactionTestController {
    private final UserService userService;
    private final UserDataRepository repository;

    /**
     * 실패 테스트
     */
    @GetMapping("/test/with-failure")
    public ResponseEntity<String> testWithFailure() {
        repository.deleteAll(); // 초기화

        userService.processUserDataWithoutTransaction("TEST_FAIL", true);

        // 잠시 대기 후 결과 확인
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        List<UserData> results = repository.findAll();
        return ResponseEntity.ok("실패 테스트 - 저장된 데이터 개수: " + results.size() +
            "\n데이터: " + results.toString());
    }

}

//service(publisher)
@Service
@RequiredArgsConstructor
public class UserService {
    private final ApplicationEventPublisher eventPublisher;
    private final UserDataRepository repository;

    // 트랜잭션 없이 이벤트 발행
    public void processUserDataWithoutTransaction(String userData, boolean shouldFail) {
        System.out.println("=== 트랜잭션 없이 이벤트 발행 ===");

        // DB에 직접 저장 (비교용)
        repository.save(new UserData(userData, "MAIN_SERVICE"));

        // 이벤트 발행
        eventPublisher.publishEvent(new UserDataEvent(userData, shouldFail));

        System.out.println("이벤트 발행 완료");
    }
}

//listener
@Component
@RequiredArgsConstructor
public class UserDataEventListener {
    private final DataStorageService dataStorageService;

    @EventListener
    @Async
    public void handleUserDataEvent(UserDataEvent event) {
        System.out.println("=== 이벤트 리스너 시작 (트랜잭션 없음) ===");
        System.out.println("현재 스레드: " + Thread.currentThread().getName());

        try {
            // 다른 클래스의 @Transactional 메소드 호출
            dataStorageService.saveDataWithTransaction(
                event.getUserData() + "_FROM_LISTENER",
                event.shouldFail()
            );
            System.out.println("이벤트 처리 완료");
        } catch (Exception e) {
            System.out.println("이벤트 처리 중 예외 발생: " + e.getMessage());
        }
    }
}

// 리스너에서 호출하는 트랜잭션 객체(실패 로직)
@Service
@RequiredArgsConstructor
public class DataStorageService {
    private final UserDataRepository repository;

    @Transactional
    public void saveDataWithTransaction(String data, boolean shouldFail) {
        System.out.println("DataStorageService.saveDataWithTransaction() 시작");

        // 데이터 저장
        UserData userData = new UserData(data, "EVENT_LISTENER");
        repository.save(userData);
        System.out.println("데이터 저장 완료: " + data);

        // 실패 테스트
        if (shouldFail) {
            System.out.println("의도적으로 예외 발생!");
            throw new RuntimeException("Transaction rollback test");
        }
    }
}
```

**결과**

```bash
2025-09-20T00:23:11.650+09:00 DEBUG 6526 --- [nio-8080-exec-2] org.hibernate.SQL                        : 
    select
        ud1_0.id,
        ud1_0.data,
        ud1_0.source 
    from
        user_data ud1_0
Hibernate: 
    select
        ud1_0.id,
        ud1_0.data,
        ud1_0.source 
    from
        user_data ud1_0
=== 트랜잭션 없이 이벤트 발행 ===
2025-09-20T00:23:11.704+09:00 DEBUG 6526 --- [nio-8080-exec-2] org.hibernate.SQL                        : 
    insert 
    into
        user_data
        (data, source, id) 
    values
        (?, ?, default)
Hibernate: 
    insert 
    into
        user_data
        (data, source, id) 
    values
        (?, ?, default)
이벤트 발행 완료
=== 이벤트 리스너 시작 (트랜잭션 없음) ===
현재 스레드: AsyncEvent-1
DataStorageService.saveDataWithTransaction() 시작
2025-09-20T00:23:11.722+09:00 DEBUG 6526 --- [   AsyncEvent-1] org.hibernate.SQL                        : 
    insert 
    into
        user_data
        (data, source, id) 
    values
        (?, ?, default)
Hibernate: 
    insert 
    into
        user_data
        (data, source, id) 
    values
        (?, ?, default)
데이터 저장 완료: TEST_FAIL_FROM_LISTENER
의도적으로 예외 발생!
이벤트 처리 중 예외 발생: Transaction rollback test
2025-09-20T00:23:13.731+09:00 DEBUG 6526 --- [nio-8080-exec-2] org.hibernate.SQL                        : 
    select
        ud1_0.id,
        ud1_0.data,
        ud1_0.source 
    from
        user_data ud1_0
Hibernate: 
    select
        ud1_0.id,
        ud1_0.data,
        ud1_0.source 
    from
        user_data ud1_0
```

**Postman에 들어온 데이터**

```
실패 테스트 - 저장된 데이터 개수: 1
데이터: [testaop.aoptest.domain.UserData@64b97dc5]
```

위의 로직을 성공시키면 데이터의 개수가 2가 되어야 한다. 그런데 1이 들어온 것을 보면 성공적으로 트랜잭션이 걸린 것을 알 수 있다.


### ✔️ 클래스간 직접 호출에는 트랜잭션이 걸리는데 이벤트로 호출하는 로직은 걸리지 않는 이유

스프링이 트랜잭션을 적용하는 방식은 프록시이다. 클래스간 직접 호출하는 경우 프록시를 통해 호출하기 때문에 트랜잭션이 정상 작동한다. 프록시 기반 AOP는 **Spring Container를 통한 메소드 호출에만 적용**되고 리플렉션 기반 직접 호출에는 프록시가 개입할 수 없다. 이벤트 리스너는 **Spring이 내부적으로 리플렉션으로 직접 호출**하기 때문에 트랜잭션 프록시가 개입하지 못해 Transaction이 적용되지 않는다.

따라서 리스너 자체에 걸어주거나 리스너에서 Transactional이 걸린 메소드를 호출하도록 한다.

```kotlin
// 방법 1: 리스너 메소드 자체에 @Transactional
@EventListener
@Async
@Transactional  // 이렇게 하면 프록시 생성
fun handleEvent(event: MyEvent) {
    // 이제 이 메소드도 프록시를 통해 호출됨
}

// 방법 2: 리스너에서 별도 서비스 호출
@EventListener
@Async
fun handleEvent(event: MyEvent) {
    eventProcessingService.processEvent(event); // 프록시 통과
}

@Service
class EventProcessingService {
    @Transactional
    fun processEvent(event: MyEvent) {
        // 트랜잭션 적용됨
    }
}
```

#### 간단 비교

| 호출 방식                      | 프록시 여부 | 트랜잭션 적용 |
| ------------------------------ | ----------- | ------------- |
| 클래스간 직접 호출             | 프록시 통과 | 적용          |
| 이벤트 리스너 메소드           | 직접 호출   | 미적용        |
| 리스너 내부의 다른 서비스 호출 | 프록시 통과 | 적용          |


