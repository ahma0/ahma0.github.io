---
title: 무중단 배포 알아보기
author: ahma0
date:   2024-04-11 11:25:00 +0900
categories: [Study, Infra]
tag: [Study, Infra, AWS, BlueGreen]
---

## 🥝 무중단 배포

무중단 배포는 말 그대로 **서비스가 중단되지 않은 상태**<sub>zero-downtime</sub>로, 새로운 버전을 사용자들에게 배포하는 것을 의미한다. 무중단 배포를 하기 위해서는 최소 서버가 2대 이상을 확보해야한다.

![image](https://github.com/ahma0/ahma0.github.io/assets/84761609/34a1a2b7-4fa2-4c63-890c-ec1c6aed8fa2)

v1 서비스를 종료 시키는 시점부터 v2를 시작하기 전까지 애플리케이션은 중단된다. 이렇게 서비스가 중단되는 시간을 **다운타임**<sub>downtime</sub>이라고 한다. v1 서비스를 꼭 중단시켜야 v2를 실행시킬 수 있는가? 동일한 포트를 사용한다고 하면 당연한 이야기이다. 8080포트를 특정 서버가 사용중일때 다른 서버를 8080 포트로 띄울수는 없다.

이 문제를 해결하기 위해 무중단 배포라는 개념이 나왔다.

- Rolling Update
- Blue/Green Deployment
- Canary Release

<br>

## 🎈 Rolling Update

롤링 배포는 사용 중인 인스턴스 내에서 새 버전을 **점진적으로** 교체하는 것으로 무중단 배포의 가장 기본적인 방식이다. 관리가 편하지만, 배포 중 한쪽 인스턴스의 수가 감소되므로 서버 처리 용량을 미리 고려해야 한다.

크게 2가지 방식이 있다.

<br>

### 📌 방식 1

![rolling deployment 1](/assets/img/posts-image/rolling-deployment-1.gif)

인스턴스를 하나 추가하고, 새로운 버전을 실행한다. 로드 밸런서에 이 인스턴스를 연결하고, 기존 구버전 어플리케이션이 실행되는 인스턴스 하나를 줄인다.

서버 개수를 유연하게 조절할 수 있는 AWS와 같은 클라우드를 기반으로 서비스를 운영할 때 적합한 방식일 것 같다.

<br>

### 📌 방식 2

![rolling deployment 2](/assets/img/posts-image/rolling-deployment-2.gif)

V1이 실행되고 있는 서버 하나를 로드밸런서에서 라우팅하지 않도록 한다. 이렇게 되면, 해당 서버에는 트래픽이 도달하지 않게 된다. 이 상태에서 해당 서버의 어플리케이션을 V2로 적용한다. 이를 반복해서 모든 서버를 새로운 버전으로 교체한다.

이런 방식은 클라우드 환경이 아닌, 물리적인 서버로 서비스를 운영하는 상황에서도 사용할 수 있을 것이다.

<br>

### 📌 장단점

#### 장점

롤링 배포 방식은 k8s, elastic beanstalk과 같은 많은 오케스트레이션 도구에서 지원하여 간편하다고 한다. 또한, 많은 서버 자원을 확보하지 않아도 무중단 배포가 가능하다.

- 점진적으로 새로운 버전이 사용자에게 출시되므로, 배포로 인한 위험성이 다소 줄어들 수 있다.
- 인스턴스마다 차례로 배포를 진행하기에 상황에 따라 손쉽게 롤백이 가능하다.
- 추가적인 인스턴스를 늘리지 않아도 된다.
- 간편한 관리

<br>

#### 단점

- 방식2와 같은 경우 배포 도중 서비스 중인 인스턴스의 수가 줄어들게 되어 각각의 서버가 부담하는 트래픽의 양이 늘어날 수 있다. 따라서 전체 트래픽의 양과 단일 서버가 처리할 수 있는 트래픽의 양을 잘 파악하여 배포를 진행해야한다.
- 구버전과 신버전의 어플리케이션이 동시에 서비스되기 때문에 호환성 문제가 발생할 수 있다.

<br>

## 🥑 Blue/Green이란?

![Blue/Green Deployment](/assets/img/posts-image/blue-green-deployment.gif)

트래픽을 한번에 구버전에서 신버전으로 옮기는 방법이다. Blue/Green 배포 전략에서는 현재 운영중인 서비스의 환경을 Blue라고 부르고, 새롭게 배포할 환경을 Green이라고 부른다.

Blue와 Green의 서버를 동시에 나란히 구성해둔 상태로 배포 시점에 로드 밸런서가 트래픽을 Blue에서 Green으로 일제히 전환시킨다. Green 버전 배포가 성공적으로 완료 되었고, 문제가 없다고 판단했을 때에는 Blue 서버를 제거할 수 있다. 혹은 다음 배포를 위해 유지해둘수 있다.

<br>

### 📌 장단점

blue/green 배포는 무중단 시스템 배포 방법중 가장 많이 사용하는 방법이다. 원리도, 만들기도 간단하다. 단점이 있다면, 배포할 어플리케이션과 동일한 인스턴스 그룹을 만들어야 하므로 비용 효율적이지는 않다. AWS를 활용하면 이를 간단히 구현 가능하다.

<br>

#### 장점

- 롤링 배포와 달리 한번에 트래픽을 모두 새로운 버전으로 옮기기 때문에 **호환성 문제가 발생하지 않는다.**
- 구버전의 인스턴스가 그대로 남아있어서 손쉬운 롤백이 가능하다.
- 구버전의 환경을 다음 배포에 재사용할 수 있다.
- 운영환경에 영향을 주지 않고 새 버전 테스트 가능.

<br>

#### 단점

- 실제 운영에 필요한 서버 리소스 대비 **2배의 리소스**를 확보해야한다. 클라우드 환경에서 운영한다면 필요없는 인스턴스를 제거하면 그만이지만, 온프레미스 방식으로 서비스를 운영했다면 비용 부담이 클것이다.
- 새로운 환경에 대한 테스트가 전제되어야 한다.

<br>

## 🥨 Canary Deployment

옛날 광부들이 유독 가스에 민감한 카나리아 새를 이용해 가스 누출 위험을 감지했던 것에서 유래한 것으로 잠재적 문제 상황을 미리 발견하기 위한 방식이다. 점진적으로 구버전에 대한 트래픽을 신버전으로 옮기는 것은 롤링 배포 방식과 비슷하다. 다만 카나리<sub>Canary</sub> 배포의 핵심은 새로운 버전에 대한 오류를 조기에 감지하는 것이다.

![Canary Deployment](/assets/img/posts-image/canary-deployment.gif)

소수 인원에 대해서만 트래픽을 새로운 버전에 옮겨둔 상태에서 서비스를 운영한다. 새로운 버전에 이상이 없다고 판단하였을 경우에 모든 트래픽을 신규 버전으로 옮긴다. 이때, 트래픽을 새로운 버전으로 옮기는 기준은 정해진 규칙(특정 유저 등) 혹은 랜덤이다.

이러한 특징으로 인해 A/B 테스트를 진행하기에도 적합한 방식이다.

<br>

### 📌 장단점

#### 장점

- **새로운 버전으로 인한 위험을 최소화** 할 수 있다.
- 문제 상황을 빠르게 감지
- A/B 테스트로 활용 가능

#### 단점

- 롤링 배포와 마찬가지로 **신/구 버전의 애플리케이션이 동시에 존재하므로 호환성 문제**가 발생할 수 있다.
- 네트워크 트래픽 제어 부담.

<br>

> A/B 테스트란? <br>대조군과 실험군으로 나누어서 특정한 UI나 알고리즘의 효과를 비교하는 방법론

<br>

## 참고

> [무중단 배포 아키텍처와 배포 전략 (Rolling, Blue/Green, Canary)](https://hudi.blog/zero-downtime-deployment/)<br>[[Infra] 무중단 배포 방식(Rolling / BlueGreen / Canary)](https://llshl.tistory.com/47)<br>