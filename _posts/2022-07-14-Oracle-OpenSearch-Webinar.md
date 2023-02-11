---
title: 21th Developer Meetup - 오픈서치 기반 오라클 클라우드의 Search Service 알아보기 웨비나 후기
author: ahma0
date:   2022-07-14 02:16:00 +0900
categories: [Webinar, Oracle]
tag: [Webinar, Seminar, Oracle, OpenSearch]
---

깃허브 Dev-Event repo를 보던 중 오라클에서 웨비나를 개최한다는 것을 보아 신청하게 되었다. 신청할 때만 해도 강연일까지 꽤 시간이 남아있었던 걸로 기억하는데 코앞으로 다가와 좀 망설여졌다. 아직 나는 개발지식이 부족하다고 느꼈기때문에 내가 이걸 들어서 이해를 할 수 있을까 고민이 많았다. 그 덕에 밤을 설친듯 싶다.

웨비나는 오라클 클라우드의 Search Service를 소개하고 설명해주는 방식으로 진행되었다. 강의 시간은 17:00~18:00으로 1시간 강의였다. 아래는 ppt에 적혀있는 내용을 조금 적어놓은 것이다.

![강의 ppt 사진](https://user-images.githubusercontent.com/84761609/178792017-2ce4d395-5f3b-420e-ae3e-402e56cc71e4.png)

<hr>

### OCI 관리형 검색 엔진 서비스

OCI + OpenSearch

OpenSearch 기반 Oracle Cloud Infrastructure(OCI) 검색 서비스는 완전 관리형 서비스로 정형/비정형 데이터 검색 기능을 제공합니다. 서비스 패치, 업데이트, 업그레이드, 백업 및 크기 조정을 자동화 지원하며, 가동 중단이 발생하지 않습니다. 서비스 사용자는 대용량 데이터의 빠른 저장, 검색 및 분석하고 거의 실시간으로 결과를 확인할 수 있습니다.

<br>

### OpenSearch 프로젝트

OpenSearch는 Apache 2.0 라이선스 Elasticsearch 7.10.2 및 Kibana 7.10.2에서 파생된 커뮤니티 중심의 오픈 소스 검색 및 분석 프로젝트입니다.

OCI Search 서비스는 완전 관리형 클라우드 네이티브 서비스입니다.

완전 관리형 제공 기능: 자동화 패치 및 보안 업데이트, 업그레이드, 클러스터 크기 변경, 스케쥴 백업, HA

데이터 안전성: FIPS 준수 서비스

<br>

### 오픈서치 기반 오라클 클라우드의 Search Service 주요 특징과 차별성

- 완전 관리형
    - 자동화된 클러스터 생성
    - 오브젝트 스토리지에 예약 백업
    - 운영체제 레벨 패치
    - 인프라 문제 발생에 대한 모니터링과 재배포
- 비교 불가 유연성
    - OCI Flex shape을 통해서 높은 CPU/메모리 설정 자유도 지원
    - 다운 타임 없이 클러스터 크기 조정
- 보안 & 규제 준수
    - 세분화된 엑세스 제어 및 권한 부여를 위해 OCI IAM과 통합
    - 모든 저장 데이터와 이동 데이터 암호화
    - FIPS 보안 준수
- 업계 최고 가격 경쟁력 / 성능
    - AWS, Azure, GCP 대비 ~35-50% 저렴
    - 첫 2개 노드는 서비스 비용에서 제외
- 강력한 인사이트 지원
    - 대시보드는 데이터를 시각화하는 수십가지 방법을 제공
    - OpenSearch Dashboard API를 활용하여 타사 앱에서 시각화 및 분석 확인
    - 온프레미스, 클라우드 또는 둘 다에 있는 데이터 수집
- 자동화된 HA
    - 노드를 3개 이상 배포하면 OCI는 여러 AD(Availability Domain) 또는 폴트 도메인(Fault Domain)에 걸쳐 노드를 자동 프로비져닝.


<hr>

<h2>FAQ</h2>

- 데이터 규모에 대한 제약 : 0.3PB까지 제약 - > 추가 확장 SR 

- Document / Index 크기 제약 : 서비스에 대한 제약은 없고 OS와 HW에 따른 제약은 잇음

- OpenSearch Intefration : 가능 근데 LogStash, FluentD, Beats 사용할 경우 따로 설정해줘야? 향후에 OCI Service Connect Hub 이용하기 때문에 별도 소프트웨어 필요 x

- OpenSearch 백업/ Snapshot 저장 위치 : OCI Object Storage

- 마이크레이션 방법을 제공하나요?
    - 옵션 1 : 시계열 데이터 이관
        - 현재 하고 있는 것과 똑같은 방식으로 데이터를 수집
        - 수집이 정상 수행 확인
        - 이전 수집을 중단하고 관리형 서비스 유지

    - 옵션 2
        - ELK와 OpenSearch 클러스터의 스냅샷 로딩
            - CSP 호스팅: 스냅샷을 오브젝트 스토리지에 저장
            - 온프레미스 호스팅: 스냅샷의 크기를 처리할 수 있는 위치에 배치
        - 스냅샷을 OCI Object Storage 이전
        - My Oracle Support(MOS)에 스냅샷을 OCI OpenSearch 서비스에 로딩 요청

<hr>

<h2>Q&A</h2>

- 타 클라우드 서비스에서 오픈서치 대시보드에 오류로 접근이 안됐는데 OCI는 따로 설정해야하는지?
	
	- 기본적으로 접근 안됨. 지금은 보안적인 측면에 따라 안됨 private 존임.

- OCI에 카프카 서비스로 들어오나요?
	
	- 이미 OCI 스트리밍 서비스로 들어와있음 오픈서치는 관련 서비스로 생각하면 됨.

- 오픈서치와 OCI 검색할 때 고려해야할 사항
	
	- 모델을 만드는 것에 대해서 인덱스를 어떻게 해야할지 고려해야함
	- 미리 제약사항을 얘기하자면 한글은 아직 안됨 지금 시점으로 2주안에 배포될 것. 