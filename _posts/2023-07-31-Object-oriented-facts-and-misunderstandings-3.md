---
title: 객체지향의 사실과 오해 - 03. 타입과 추상화
author: ahma0
date:   2023-07-31 21:08:00 +0900
categories: [Study, 객체지향의 사실과 오해]
tag: [Study, 객체지향의 사실과 오해]
math: true
mermaid: true
image:
  path: https://github.com/ahma0/ahma0/assets/84761609/7c4db4bc-7e95-4acd-9a5f-0b93e6f5afdc
  alt: book image
---

> [객체지향의 사실과 오해](https://product.kyobobook.co.kr/detail/S000001628109)를 읽고 정리한 글입니다.
{: .prompt-info }

<br>

## 추상화를 통한 복잡성 극복

- 추상화: 현실에서 출발하되 불필요한 부분을 도려내가면서 사물의 놀라운 본질을 드러나게 하는 과정
    - 목적: 불필요한 부분을 무시함으로써 현실에 존재하는 복잡성을 극복하는 것
    - 훌륭한 추상화는 목적에 부합하는 것

<br>

### 추상화

어떤 양상, 세부 사항, 구조를 좀 더 명확하게 이해하기 위해 특정 절차나 물체를 의도적으로 생략하거나 감춤으로써 복잡도를 극복하는 방법이다.

복잡성을 다루기 위해 추상화는 두 차원에서 이뤄진다.

- 첫 번째 차원은 구체적인 사물들 간의 공통점은 취하고 차이점은 버리는 일반화를 통해 단순하게 만드는 것이다.
- 두 번째 차원은 중요한 부분을 강조하기 위해 불필요한 세부 사항을 제거함으로써 단순하게 만드는 것이다.

모든 경우에 추상화의 목적은 복잡성을 이해하기 쉬운 수준으로 단순화하는 것이라는 점을 기억하라.

<br>

## 객체지향과 추상화

> 기껏해야 트럼프에 불과해

- 정원에 서 있는 다양한 인물들을 계급, 나이, 성격 등의 차이점은 무시한 채 '트럼프'라는 유사성을 기반으로 추상화함
- 앨리스는 정원에 있는 인물들을 두 개의 그룹으로 나눔
    - 트럼프: 정원사, 병사, 신하, 왕자와 공주, 하객으로 참여한 왕과 왕비들, 하트 잭, 하트 왕과 하트 여왕
    - 토끼
- 다수의 개별적인 인물이 아니라 '트럼프'와 '토끼'라는 두 개의 렌즈를 통해 정원을 바라보는 것은 정원에 내재된 복합성을 효과적으로 감소시킴

<br>

### 개념

- 앨리스가 인물들의 차이점을 무시하고 공통점만을 취해 트럼프라는 개념으로 단순화한 것은 추상화의 일종
- **개념(concept)**: 공통점을 기반으로 객체들을 묶기 위한 그릇
    - 일반적으로 우리가 인식하고 있는 다양한 사물이나 객체에 적용할 수 있는 아이디어나 관념
- 개념을 이용하면 객체를 여러 그룹으로 **분류(classification)**할 수 있음
- 개념은 공통점을 기반으로 객체를 분류할 수 있는 일종의 체
- 객체에 어떤 개념을 적용하는 것이 가능해서 개념 그룹의 일원이 될 때 객체를 그 개념의 **인스턴스(instance)**라고 함

> 객체란 특정한 개념을 적용할 수 있는 구체적인 사물을 의미한다. 개념이 객체에 적용됐을 때 객체를 개념의 인스턴스라고 한다.

<br>

### 개념의 세 가지 관점

어떤 객체에 어떤 개념이 적용됐다고 할 때는 그 개념이 부가하는 의미를 만족시킴으로써 다른 객체와 함께 해당 개념의 일원이 됐다는 것을 의미한다. 일반적으로 객체의 분류 장치로서 개념을 이야기할 때는 아래의 세 가지 관점을 함께 언급한다.

- **심볼(symbol)**: 개념을 가리키는 간략한 이름이나 명칭
- **내연(intension)**: 개념의 완전한 정의를 나타내며 내연의 의미를 이용해 객체가 개념에 속하는지 여부를 확인할 수 있다.
- **외연(extension)**: 개념에 속하는 모든 객체의 집합(set)

'심볼'은 개념을 가리키는 이름, '내연'이란 개념의 의미, 내연은 개념을 객체에 적용할 수 있는지 여부를 판단하기 위한 조건. '외연'은 개념에 속하는 객체들, 즉 개념의 인스턴스들이 모여 이뤄진 집합.

트럼프라는 개념의 심볼, 내연, 외연은 다음과 같다.

- **심볼(symbol)**: 트럼프
- **내연(intension)**: 몸이 납작하고 두 손과 두 발은 네모 귀퉁이에 달려 있는 등장인물
- **외연(extension)**: 정원사, 병사, 신하, 왕자와 공주, 하객으로 참석한 왕과 왕비들, 하트 잭, 하트 왕과 하트 여왕

개념을 구성하는 심볼, 내연, 외연은 객체의 분류 방식에 대한 지침을 제공한다. 개념을 이용해 공통점을 가진 객체들을 분류할 수 있다는 아이디어는 객체지향 패러다임이 복잡성을 극복하는 데 사용하는 가장 기본적인 인지 수단이기 때문이다.

<br>

### 객체를 분류하기 위한 틀

- 외연의 관점에서 어떤 객체에 어떤 개념을 적용할 수 있다는 것 = 동일한 개념으로 구성된 객체 집합에 해당 객체를 포함시킨다는 것
- 객체에게 적용한 개념을 결정하는 것은 결국 해당 객체를 개념이 적용된 객체 집합의 일원으로 맞아들인다는 것을 의미
- 객체에 어떤 개념을 적용할 것인지를 결정하는 것은 결국 객체들을 개념에 따라 분류하는 것과 동일
- 분류란 객체를 특정한 개념의 객체 집합에 포함시키거나 포함시키지 않는 작업을 의미

> 분류란 객체에 특정한 개념을 적용하는 작업이다. 객체에 특정한 개념을 적용하기로 결심했을 때 우리는 그 객체를 특정한 집합의 멤버로 분류하고 있는 것이다.

- 어떤 객체를 어떤 개념으로 분류할지가 객체지향의 품질을 결정
- 객체를 적절한 개념에 따라 분류하지 못한 애플리케이션은 유지보수가 어렵고 변화에 쉽게 대처하기 못함.
- 객체를 적절한 개념에 따라 분류한 애플리케이션은 유지보수가 용이하고 변경에 유연하게 대처

<br>

## 타입

### 타입은 개념이다

- 타입은 공통점을 기반으로 객체들을 묶기 위한 틀

> **타입**은 개념과 동일하다. 따라서 타입이란 우리가 인식하고 있는 다양한 사물이나 객체에 적용할 수 있는 아이디어나 관념을 의미한다. 어떤 객체에 타입을 적용할 수 있을 때 그 객체를 타입의 인스턴스라고 한다. 타입의 인스턴스는 타입을 구성하는 외연인 객체 집합의 일원이 된다.

<br>

### 데이터 타입

- 타입 시스템의 목적
    - 메모리 안의 모든 데이터가 비트열로 보임으로써 야기되는 혼란을 방지하는 것
    - 데이터가 잘못 사용되지 않도록 제약사항을 부과하는 것

- 타입은 데이터가 어떻게 사용되느냐에 관한 것이다.
- 타입에 속한 데이터를 메모리에 어떻게 표현하는지는 외부로부터 철저하게 감춰진다.
    - 데이터 타입의 표현은 연산 작업을 수행하기에 가장 효과적인 형태가 선택됨
    - 해당 데이터 타입의 표현 방식을 몰라도 데이터를 사용하는 데 지장이 없음

> 데이터 타입은 메모리 안에 저장된 데이터의 종류를 분류하는 데 사용하는 메모리 집합에 관한 메타데이터다. 데이터에 대한 분류는 암시적으로 어떤 종류의 연산이 해당 데이터에 대해 수행될 수 있는지를 결정한다.

<br>

### 객체와 타입

- 어떤 객체가 어떤 타입에 속하는 지를 결정하는 것은 객체가 수행하는 행동
- 객체의 내부적인 표현은 외부로부터 철저하게 감춰짐

<br>

### 행동이 우선이다

객체가 어떤 행동을 하느냐에 따라 객체의 타입이 결정된다. 객체의 타입은 객체의 내부 표현과는 아무런 상관이 없다. 객체의 내부 표현 방식이 다르더랃도 동일하게 행동한다면 그 객체들은 동일한 타입에 속한다. 따라서 동일한 책임을 수행하는 일련의 객체는 동일한 타입에 속한다고 말할 수 있다. 객체의 타입을 결정하는 것은 객체의 행동뿐이다.

- 같은 타입에 속한 객체는 행동만 동일하다면 서로 다른 데이터를 가질 수 있음
- 동일한 행동 = 동일한 책임 = 동일한 메시지 수신
- 동일한 타입에 속한 객체는 내부의 데이터 표현 방식이 다르더라도 동일한 메시지를 수신하고 이를 처리
- 내부의 표현 방식이 다르기 때문에 동일한 메시지를 처리하는 방식은 서로 다름
- 다형성
    - 동일한 요청에 대해 서로 다른 방식으로 응답할 수 있는 능력
    - 다형적인 객체들은 동일한 타입(또는 타입 계층)에 속함
- 캡슐화
    - 휼륭한 객체지향 설계는 외부에 행동만을 제공하고 데이터는 행동 뒤로 감춰야 함

행동에 따라 객체를 분류하기 위해서는 객체가 내부적으로 관리해야하는 데이터가 아니라 객체가 외부에 제공해야하는 행동을 먼저 생각해야 한다. 객체가 외부에 제공해야하는 책임을 먼저 결정, 그 책임을 수행하는 데 적합한 데이터를 나중에 결정, 데이터를 책임을 수행하는 데 필요한 외부 인터페이스 뒤로 캡슐화

<br>

## 타입의 계층

### 트럼프 계층

- 모든 트럼프 인간은 동시에 트럼프이기도 함.
- 트럼프는 트럼프 인간을 포괄하는 좀 더 일반적인 개념
- 트럼프 인간은 트럼프보다 좀 더 특화된 행동을 하는 특수한 개념
- 이 두 개념 사이의 관계를 **일반화/특수화(generalization/specialization)** 관계라고 함

<br>

### 일반화/특수화 관계

- 객체지향에서 일반화/특수화 관계를 결정하는 것은 객체의 상태를 표현하는 데이터가 아니라 행동임
- 두 타입 간에 일반화/특수화 관계가 성립하려면 한 타입이 다른 타입보다 더 특수하게 행동헤야 하고 반대로 한 타입은 다른 타입보다 더 일반적으로 행동해야 함.
- 일반적인 타입은 특수한 타입이 가진 모든 행동들 중에서 일부 행동만을 가지는 타입
- 특수한 타입은 일반적인 타입이 가진 모든 타입을 포함하지만 거기에 더해 자신만의 행동을 추가하는 타입
- 타입의 내연을 의미하는 행동의 가짓수와 외연을 의미하는 집합의 크기는 서로 반대임
- 일반적인 타입은 특수한 타입보다 더 적은 수의 행동을 가지지만 더 큰 크기의 외연의 집합을 가짐

<br>

### 슈퍼 타입과 서브 타입

- 슈퍼타입(supertype): 일반적인 타입
- 서브타입(subtype): 특수한 타입
- 어떤 타입이 다른 타입의 서브타입이 되기 위해서는 행위적 호환성을 만족시켜야 함
- 일반적으로 서브타입은 슈퍼타입의 행위와 호환되기 때문에 서브타입은 슈퍼타입을 대체할 수 있어야 함
- 어떤 타입을 다른 타입의 서브타입이라고 말할 수 있으려면 다른 타입을 대체할 수 있어야 함

#### 일반화/특수화 관계 표기 방법

좀 더 일반적인 타입인 슈퍼타입을 상단에, 좀 더 특수한 타입인 서브타입을 하단에 위치시키고 속이 빈 삼각형으로 연결해서 표현한다.

![](https://github.com/ahma0/ahma0/assets/84761609/f510fa14-2313-4f7b-b842-4db1b12001d0)

<br>

### 일반화는 추상화를 위한 도구다

- 정원에 있던 등장인물들의 차이점은 배제하고 공통점만을 강조함으로써 이들을 공통의 타입인 트럼프 인간으로 분류
- 트럼프 인간을 좀 더 단순한 관점에서 바라보기 위해 불필요한 특성을 배제하고 좀 더 포괄적인 의미를 지는 트럼프로 일반화

<br>

## 정적 모델

### 타입의 목적

- 타입을 사용하는 이유는 인간의 인지 능력으로는 시간에 따라 동적으로 변하는 객체의 복잡성을 극복하기가 너무 어렵기 때문
- 앨리스라는 객체의 상태는 변하지만 앨리스를 다른 객체와 구별할 수 있는 식별성은 동일하게 유지
- 타입은 시간에 따라 동적으로 변하는 앨리스의 상태를 시간과 무관한 정적인 모습으로 다룰 수 있게 해줌

<br>

### 그래서 결국 타입은 추상화다

- 타입을 이용하면 객체의 동적인 특성을 추상화할 수 있음
- 타입은 시간에 따른 객체의 상태 변경이라는 복잡성을 단순화할 수 있는 효과적인 방법

<br>

### 동적 모델과 정적 모델

객체를 생각할 때 우리는 두 가지 모델을 동시에 고려한다.

- 객체가 특정 시점에 구체적으로 어떤 상태를 가지느냐
    - 객체의 **스냅샷(snapshot)** = **객체 다이어그램(object diagram)**
    - 동적 모델(dynamic model)
        - 실제로 객체가 살아 움직이는 동안 상태가 어떻게 변하고 행동하는지를 포착하는 것
- 객체가 가질 수 있는 모든 상태와 모든 행동을 시간에 독립적으로 표현
    - **타입 모델(type model)**
    - **정적 모델(static model)**
        - 객체가 속한 타입의 정적인 모습을 표현

<br>

### 클래스

타입을 구현하는 가장 보편적인 방법은 클래스를 이용하는 것이다.

- '타입을 구현한다'
    - 클래스와 타입은 동일한 것이 아님
    - 타입은 객체를 분류하기 위해 사용하는 개념
    - 클래스는 단지 타입을 구현할 수 있는 여러 구현 메커니즘 중 하나
- 클래스와 타입을 구분하는 것은 설계를 유연하게 유지하기 위한 바탕

객체를 분류하는 기준은 타입이며 타입을 나누는 기준은 객체가 수행하는 행동이다.