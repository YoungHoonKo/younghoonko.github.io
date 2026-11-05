---
title: 13. Internet Engineering - Final Exam Review Questions
description: "인터넷공학 기말고사 대비 예상문제 64개를 단답형, 설명→용어, 비교, DHCP/NAT/BGP/OSPF/SDN/SNMP, O/X, 계산 문제로 나누어 정답과 짧은 이유로 정리"
Author: YoungHoon Ko
date: 2026-11-05 15:20:00 +0900
categories: [University(2026-First-Semester), Internet Engineering]
tags: [internet, network]
math: true
image: /assets/img/lang/internet.png
---

## 학습 목표

- 헷갈리기 쉬운 용어 쌍(포워딩/라우팅, 데이터/제어 평면, eBGP/iBGP 등) 구분
- 4장(데이터 평면)과 5장(제어 평면)의 핵심 용어를 설명만 보고 떠올리기
- 비교표로 링크 상태와 거리 벡터, OSPF와 BGP, 전통적 라우팅과 SDN 정리
- DHCP, NAT, BGP, OSPF, SDN, SNMP의 단골 문제 확인
- O/X 문제로 자주 틀리는 문장 점검
- 다익스트라와 벨만-포드 계산 절차 복습

> 개념 설명은 [10. 데이터 평면](/posts/Internet_Engineering_10/), [11. 제어 평면](/posts/Internet_Engineering_11/), [12. 링크 계층과 무선](/posts/Internet_Engineering_12/)에 있음. 이 글은 시험 직전에 훑어보기 위한 문제 모음
{: .prompt-info }

---

## 헷갈리는 개념 구분

| 구분 | 의미 |
| --- | --- |
| Forwarding | 입력 포트 → 출력 포트로 패킷 전달 (라우터 하나, 지역적) |
| Routing | 출발지 → 목적지까지 전체 경로 계산 (네트워크 전체, 전역적) |
| Data Plane | 포워딩 수행 |
| Control Plane | 라우팅 수행 |

---

## A. 단답형

| 번호 | 문제 | 정답 |
| --- | --- | --- |
| 1 | 패킷을 입력 포트에서 출력 포트로 옮기는 로컬 기능 | Forwarding |
| 2 | 출발지부터 목적지까지 전체 경로를 결정하는 기능 | Routing |
| 3 | 포워딩 테이블을 바탕으로 실제 패킷을 전달하는 평면 | Data Plane |
| 4 | 네트워크 전체 경로를 계산하는 평면 | Control Plane |
| 5 | 어떤 보장도 제공하지 않는 인터넷의 서비스 모델 | Best Effort |
| 6 | 헤더 값을 검사해서 동작을 정하는 개념 | Match + Action |
| 7 | 목적지 주소와 가장 길게 일치하는 프리픽스를 고르는 기법 | Longest Prefix Matching |
| 8 | 최장 프리픽스 매치를 빠르게 하는 메모리 | TCAM |
| 9 | 라우터 안에서 입력 링크와 출력 링크를 잇는 구조 | Switching Fabric |
| 10 | 큐 맨 앞 패킷이 뒤 패킷들을 막는 현상 | HOL Blocking |
| 11 | 버퍼가 가득 찼을 때 새로 온 패킷을 버리는 정책 | Tail Drop |
| 12 | IP 주소를 동적으로 할당하는 프로토콜 | DHCP |
| 13 | 사설 IP를 공인 IP로 변환하는 기술 | NAT |
| 14 | 오류 보고와 ping에 쓰이는 프로토콜 | ICMP |
| 15 | AS 내부 라우팅에 쓰이는 **링크 상태** 프로토콜 | OSPF |
| 16 | AS 사이 라우팅 프로토콜 | BGP |
| 17 | BGP의 라우팅 방식 | Path Vector |
| 18 | 다른 AS와 연결된 AS 경계 라우터 | Gateway Router (OSPF 용어로는 Boundary Router) |
| 19 | 같은 AS 안에서 외부 경로 정보를 전파하는 BGP | iBGP |
| 20 | 다른 AS와 직접 경로 정보를 주고받는 BGP | eBGP |

---

## B. 설명 → 용어

| 번호 | 설명 | 정답 |
| --- | --- | --- |
| 21 | 모든 라우터가 전체 토폴로지를 공유하고 최단 경로를 계산 | Link State |
| 22 | 이웃과 거리 정보를 주고받으며 반복해서 최적 경로를 찾음 | Distance Vector |
| 23 | 중앙 컨트롤러가 포워딩 테이블을 계산해서 설치 | SDN |
| 24 | AS들이 서로 목적지 도달 가능성을 광고 | BGP |
| 25 | AS 내부 비용만 보고 가장 가까운 게이트웨이를 고름 | Hot Potato Routing |
| 26 | BGP 피어가 TCP 연결로 주고받는 메시지 | OPEN, UPDATE, KEEPALIVE, NOTIFICATION |
| 27 | OSPF에서 AS 전체(또는 영역)에 플러딩되는 정보 | LSA (Link State Advertisement) |
| 28 | 링크 상태 광고를 영역 안에서만 전파하는 OSPF 구조 | Hierarchical OSPF |

---

## C. 비교 문제

**29. Link State vs Distance Vector**

| Link State | Distance Vector |
| --- | --- |
| 전체 토폴로지를 앎 | 이웃 정보만 앎 |
| 다익스트라 | 벨만-포드 |
| OSPF, IS-IS | RIP, EIGRP |
| 모든 라우터에 플러딩, $$O(n^2)$$ 계산 | 이웃끼리 교환, 수렴이 느릴 수 있고 무한 계수 문제 |

**30. Data Plane vs Control Plane**

| Data Plane | Control Plane |
| --- | --- |
| Forwarding | Routing |
| 지역적 기능 | 전역적 기능 |
| 실제 전달 (ns 단위, 하드웨어) | 경로 계산 (ms 단위, 소프트웨어) |

**31. eBGP vs iBGP**

| eBGP | iBGP |
| --- | --- |
| 다른 AS의 라우터와 연결 | 같은 AS의 라우터와 연결 |
| 이웃 AS로부터 도달 정보를 받음 | 받은 정보를 AS 안에 전파 |

**32. OSPF vs BGP**

| OSPF | BGP |
| --- | --- |
| Intra-AS | Inter-AS |
| Link State | Path Vector |
| 다익스트라, 성능(최소 비용) 중심 | 정책 중심 |
| IP 위에 직접 | TCP 위에서 |

**33. 전통적 라우팅 vs SDN**

| 전통적 라우팅 | SDN |
| --- | --- |
| 분산형 | 논리적 중앙 집중형 |
| 라우터마다 제어 | 컨트롤러가 제어 |

---

## D~I. 프로토콜별 단골 문제

| 번호 | 문제 | 정답 |
| --- | --- | --- |
| 34 | DHCP 과정 | Discover → Offer → Request → ACK |
| 35 | DHCP Discover의 출발지 IP | 0.0.0.0 (아직 주소가 없음) |
| 36 | DHCP Discover의 목적지 IP | 255.255.255.255 (서버 주소를 모름) |
| 37 | NAT의 장점 | 공인 IP 절약, ISP를 바꿔도 내부 주소 유지, 내부 장치가 외부에 노출되지 않음 |
| 38 | NAT의 단점 | 종단 간 원칙 위반, 계층 구조 위반(라우터가 포트 번호를 바꿈), 외부에서 내부 서버 접속이 어려움 |
| 39 | BGP 경로 선택 순서 | Local Preference → 짧은 AS-PATH → 가까운 NEXT-HOP(Hot Potato) → 기타 |
| 40 | 가장 중요한 BGP 선택 기준 | Local Preference (정책) |
| 41 | AS-PATH | 광고가 지나온(목적지까지 거쳐야 하는) AS 목록 |
| 42 | NEXT-HOP | 다음 AS로 가는 AS 내부 게이트웨이 라우터의 주소 |
| 43 | OSPF가 쓰는 알고리즘 | 다익스트라 |
| 44 | OSPF 방식 | Link State |
| 45 | OSPF는 TCP/UDP를 쓰는가 | 쓰지 않음 (IP 위에 직접, 프로토콜 89) |
| 46 | LSA의 뜻 | Link State Advertisement |
| 47 | SDN의 특징 4가지 | 플로우 기반 포워딩, 데이터/제어 평면 분리, 제어 기능이 스위치 바깥(컨트롤러)에 있음, 프로그래밍 가능한 제어 응용 |
| 48 | SDN에서 제어 기능을 수행하는 것 | SDN Controller (와 그 위의 제어 응용) |
| 49 | OpenFlow가 동작하는 위치 | Controller ↔ Switch |
| 50 | 플로우 엔트리를 추가, 삭제, 수정하는 메시지 | modify-state |
| 51 | SNMP 통신 방식 | Request/Response, Trap |
| 52 | 예외 이벤트가 생기면 장치가 먼저 보내는 것 | Trap |
| 53 | MIB의 뜻 | Management Information Base |

---

## J. O/X

| 번호 | 문장 | 정답 | 이유 |
| --- | --- | --- | --- |
| 54 | 인터넷은 순서 전달을 보장한다 | X | best effort는 아무것도 보장하지 않음 |
| 55 | OSPF는 Distance Vector 프로토콜이다 | X | Link State |
| 56 | BGP는 TCP를 사용한다 | O | 포트 179의 반영구 TCP 연결 |
| 57 | NAT는 IPv4 주소 부족 문제를 완화한다 | O | 공인 주소 하나를 여러 장치가 공유 |
| 58 | SDN은 논리적으로 중앙 집중식 구조이다 | O | 물리적으로는 분산 시스템으로 구현될 수 있음 |
| 59 | HOL Blocking은 출력 포트 큐에서만 발생한다 | X | **입력** 포트 큐에서 발생 |
| 60 | TCAM은 최장 프리픽스 매칭에 사용된다 | O | 내용으로 한 클록에 병렬 검색 |

---

## K. 계산 문제 절차

### 다익스트라 (61, 62번 대비)

- 사용처: Link State, OSPF
1. 출발 노드를 N'에 넣고, 이웃의 비용 D(v)와 직전 노드 p(v)를 적음 (이웃이 아니면 ∞)
2. N'에 없는 노드 중 D가 가장 작은 노드 w를 N'에 추가
3. w의 이웃 v마다 D(v)를 $$\min(D(v),\ D(w) + c(w,v))$$로 갱신하고, 줄었으면 p(v) = w
4. 모든 노드가 N'에 들어갈 때까지 2~3 반복

풀이 예시는 [Chapter 5 문제풀이 P3](/posts/Internet_Engineering_9/)

### 벨만-포드 (63, 64번 대비)

- 사용처: Distance Vector, RIP
- 식으로 쓰면 $$D_x(y) = \min_v \lbrace c(x,v) + D_v(y) \rbrace$$
- 의미: 이웃 v를 거쳐 y까지 가는 비용 중 최솟값. 이웃의 $$D_v(y)$$는 이웃까지의 링크 비용이 아니라 **이웃이 알려 준 최소 비용**

풀이 예시는 [Chapter 5 문제풀이 P5, P7](/posts/Internet_Engineering_9/)

### 링크 계층 (6장)

| 문제 | 정답 |
| --- | --- |
| CRC의 목적은? | 전송 중 생긴 오류를 **검출**하는 것 (정정은 못함) |
| ALOHA의 특징은? | 채널을 확인하지 않고 바로 보내고, 충돌하면 확률 p로 재전송 |
| CSMA의 핵심 아이디어는? | 보내기 전에 채널 상태를 먼저 확인 |
| CSMA/CD의 목적은? | 충돌을 감지하면 즉시 전송을 멈춰 채널 낭비를 줄임 |
| CSMA/CD가 해결하는 문제는? | 충돌이 나도 프레임을 끝까지 보내 시간이 낭비되는 문제 |

---

### 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 3번 | 라우팅 테이블을 기반으로 실제 패킷을 전달하는 플레인 | 포워딩 테이블을 기반으로 | 데이터 평면이 보는 것은 포워딩 테이블 |
| 29번 | Link State는 빠른 수렴, Distance Vector는 느린 수렴 | LS는 메시지와 계산 부담($$O(n^2)$$), DV는 느린 수렴과 무한 계수 | 교재는 어느 한쪽이 항상 낫다고 하지 않음 |
| 42번 | NEXT-HOP: 다음 홉 라우터 | 다음 AS로 가는 AS 내부 게이트웨이 라우터의 IP 주소 | 핫 포테이토에서 이 라우터까지의 AS 내부 비용을 비교함 |
| CSMA/CD가 해결하는 문제 | Collision(충돌) 문제를 해결 | 충돌 자체를 없애지는 못하고, 충돌로 인한 낭비를 줄임 | CSMA/CD에서도 충돌은 생김 |

---

## 최종 핵심 정리

1. 포워딩은 데이터 평면의 지역적 기능, 라우팅은 제어 평면의 전역적 기능
2. 인터넷은 best effort이고, HOL 차단은 입력 포트 큐, tail drop은 출력 버퍼의 드롭 정책
3. 최장 프리픽스 매치는 TCAM으로 빠르게 하고, match + action은 SDN과 OpenFlow의 바탕
4. DHCP는 Discover(0.0.0.0 → 255.255.255.255), Offer, Request, ACK의 4단계
5. Link State(OSPF, 다익스트라)와 Distance Vector(RIP, 벨만-포드)를 정보 범위와 알고리즘으로 구분
6. BGP는 TCP 위의 Path Vector이고 경로 선택은 Local Preference → AS-PATH → NEXT-HOP → 기타
7. OSPF는 IP 위에 직접 실려 LSA를 플러딩하고, 계층적 OSPF는 영역과 백본으로 나눔
8. SDN은 컨트롤러와 OpenFlow로, 네트워크 관리는 SNMP(요청/응답, Trap)와 MIB로 함

---

End.
