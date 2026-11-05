---
title: 11. Internet Engineering - Network Layer Control Plane (Final Review)
description: "기말 정리 2: 링크 상태와 거리 벡터 라우팅, AS 내부(OSPF, RIP)와 AS 사이(BGP) 라우팅, BGP 경로 선택과 핫 포테이토, SDN과 OpenFlow, ICMP, 네트워크 관리(SNMP, NETCONF/YANG). 거리 벡터의 무한 계수 문제와 BGP 경로 선택을 Python으로 실행"
Author: YoungHoon Ko
date: 2026-11-05 15:18:00 +0900
categories: [University(2026-First-Semester), Internet Engineering]
tags: [internet, network]
math: true
image: /assets/img/lang/internet.png
---

## 학습 목표

- 라우터별 제어와 SDN(논리적 중앙 집중 제어)의 차이
- 링크 상태(다익스트라)와 거리 벡터(벨만-포드) 알고리즘 비교, 거리 벡터의 무한 계수 문제
- AS 내부 라우팅(RIP, EIGRP, OSPF)과 계층적 OSPF
- BGP의 역할, eBGP와 iBGP, 경로 속성(AS-PATH, NEXT-HOP), 경로 선택 순서
- SDN 구성 요소와 OpenFlow 메시지
- ICMP와 네트워크 관리(SNMP/MIB, NETCONF/YANG)

---

## 1. 제어 평면의 두 가지 방식

| 구분 | 라우터별 제어 | SDN |
| --- | --- | --- |
| 라우팅 알고리즘 위치 | 모든 라우터 | 원격 컨트롤러 |
| 포워딩 테이블 계산 | 각 라우터가 이웃과 메시지를 주고받으며 직접 계산 | 컨트롤러가 계산해서 라우터에 설치 |
| 구조 | 분산형 | 논리적 중앙 집중형 |

라우팅 프로토콜의 목표: 송신 호스트에서 수신 호스트까지 라우터 네트워크를 지나는 **좋은 경로**(비용이 가장 작은 경로)를 찾는 것.

---

## 2. 라우팅 알고리즘

| 구분 | 링크 상태 (Link State) | 거리 벡터 (Distance Vector) |
| --- | --- | --- |
| 가진 정보 | **전체 토폴로지**와 모든 링크 비용 (전역 정보) | 이웃과의 링크 비용과 이웃이 알려 준 거리 벡터 (지역 정보) |
| 알고리즘 | 다익스트라 | 벨만-포드 |
| 정보 교환 | 링크 상태를 모든 라우터에 플러딩 | 이웃끼리만 거리 벡터 교환 |
| 성질 | 반복적, 한 노드에서 계산 | 반복적, 비동기, 분산, 스스로 멈춤 |
| 문제점 | 메시지 양이 많고 $$O(n^2)$$ 계산, 링크 비용이 트래픽에 따라 바뀌면 경로가 진동할 수 있음 | 수렴이 느릴 수 있고 **무한 계수(count-to-infinity)** 문제 |
| 대표 프로토콜 | OSPF, IS-IS | RIP, EIGRP |

### 다익스트라

1. 출발 노드에서 시작해 이웃의 비용을 초기화
2. 아직 확정되지 않은 노드 중 비용이 가장 작은 노드를 확정 (그리디 선택)
3. 그 노드를 거치는 경로로 이웃의 비용을 갱신
4. 모든 노드를 확정할 때까지 반복 → 최단 경로 트리

### 벨만-포드 (거리 벡터)

식으로 쓰면 $$D_x(y) = \min_v \lbrace c(x,v) + D_v(y) \rbrace$$

- 의미: "이웃 v를 거쳐 y까지 가는 비용" 중 가장 작은 것
- 부분 문제(이웃의 최소 비용)를 이용해 큰 문제를 푸는 **동적 프로그래밍**
- 각 노드는 자기 거리 벡터가 바뀔 때만 이웃에게 알림 → 바뀌는 것이 없으면 스스로 멈춤

### 좋은 소식은 빠르게, 나쁜 소식은 느리게 (실행)

노드 x, y, z가 있고 c(x,y) = 4, c(y,z) = 1, c(x,z) = 50인 교재 예제. 목적지 x로 수렴한 상태에서 c(x,y)가 바뀌면 어떻게 되는지 동기식(모든 노드가 라운드마다 동시에 갱신)으로 흉내 냄.

```python
INF = float("inf")

def run_dv(cost, nodes, dest, init=None, max_rounds=60):
    """동기식 거리 벡터: 매 라운드 모든 노드가 이웃의 이전 벡터로 D(dest)를 다시 계산"""
    D = init or {n: (0 if n == dest else cost.get((n, dest), INF)) for n in nodes}
    history = [dict(D)]
    for _ in range(max_rounds):
        new = {}
        for n in nodes:
            if n == dest:
                new[n] = 0
                continue
            new[n] = min(cost[(n, v)] + D[v] for v in nodes if (n, v) in cost)
        if new == D:
            break
        D = new
        history.append(dict(D))
    return history

def links(**kw):
    c = {}
    for k, w in kw.items():
        a, b = k
        c[(a, b)] = c[(b, a)] = w
    return c

nodes = ["x", "y", "z"]
before = links(xy=4, yz=1, xz=50)
start = run_dv(before, nodes, "x")[-1]
print("수렴한 상태 (목적지 x):", start)

for title, new_xy in [("좋은 소식: c(x,y) 4 -> 1", 1), ("나쁜 소식: c(x,y) 4 -> 60", 60)]:
    after = links(xy=new_xy, yz=1, xz=50)
    hist = run_dv(after, nodes, "x", init=dict(start))
    print(f"\n{title}: {len(hist) - 1}라운드 만에 수렴")
    for r, D in enumerate(hist):
        if r < 4 or r >= len(hist) - 2:
            print(f"  round {r:2}: D_y(x)={D['y']:>3}, D_z(x)={D['z']:>3}")
        elif r == 4:
            print("  ...")
```

실행 결과

```text
수렴한 상태 (목적지 x): {'x': 0, 'y': 4, 'z': 5}

좋은 소식: c(x,y) 4 -> 1: 2라운드 만에 수렴
  round  0: D_y(x)=  4, D_z(x)=  5
  round  1: D_y(x)=  1, D_z(x)=  5
  round  2: D_y(x)=  1, D_z(x)=  2

나쁜 소식: c(x,y) 4 -> 60: 47라운드 만에 수렴
  round  0: D_y(x)=  4, D_z(x)=  5
  round  1: D_y(x)=  6, D_z(x)=  5
  round  2: D_y(x)=  6, D_z(x)=  7
  round  3: D_y(x)=  8, D_z(x)=  7
  ...
  round 46: D_y(x)= 50, D_z(x)= 50
  round 47: D_y(x)= 51, D_z(x)= 50
```

- 비용이 **줄면** 2라운드 만에 끝남 (좋은 소식은 빠르게)
- 비용이 **늘면** y는 "z를 거치면 5 + 1 = 6"이라고 계산하는데, z의 5는 사실 **y를 거치는** 경로였음. y와 z가 서로를 거쳐 가는 루프를 만들며 2씩 올려 가다가, 직접 링크 50을 쓰는 것이 더 싸질 때까지 47라운드나 걸림 (나쁜 소식은 느리게, 무한 계수 문제)
- 교재는 비동기 갱신으로 세어 44번이라고 하지만, 현상은 같음
- 해결책 중 하나는 **poisoned reverse**: z가 y를 거쳐 x로 간다면 y에게 "내 x까지 거리는 ∞"라고 알려서 y가 z를 거치는 경로를 쓰지 못하게 함 (루프가 3개 이상 노드로 이루어지면 막지 못함)

---

## 3. 인터넷의 계층적 라우팅

- 모든 라우터가 같은 알고리즘으로 전 세계 경로를 계산하는 것은 **규모**(수십억 목적지)와 **관리 자율성**(각 조직이 자기 네트워크를 직접 제어하고 싶어 함) 때문에 불가능
- 그래서 라우터를 **자율 시스템(AS)** 으로 묶음
  - **AS 내부 라우팅(intra-AS)**: 같은 AS의 라우터는 같은 프로토콜을 씀. AS마다 다른 프로토콜을 써도 됨
  - **AS 사이 라우팅(inter-AS)**: AS 경계의 **게이트웨이 라우터**가 다른 AS와 경로를 주고받음
- 포워딩 테이블은 두 가지를 함께 써서 채움: AS 안의 목적지는 AS 내부 라우팅이, 바깥 목적지는 AS 사이 라우팅과 AS 내부 라우팅이 함께 정함

### AS 내부 라우팅 프로토콜

> **(중요!!)** 세 프로토콜의 방식 구분이 시험에 나옴
{: .prompt-warning }

| 프로토콜 | 방식 | 특징 |
| --- | --- | --- |
| RIP | 거리 벡터 | 30초마다 거리 벡터 교환. 지금은 잘 안 씀 |
| EIGRP | 거리 벡터 기반 | 오랫동안 Cisco 전용이었다가 2013년 공개(RFC 7868) |
| OSPF | 링크 상태 | 다익스트라. IS-IS(ISO 표준)도 거의 같음 |

### OSPF

- "Open": 공개 표준
- 각 라우터가 링크 상태 광고(**LSA**)를 AS 안의 **모든 라우터에 플러딩**함
- OSPF 메시지는 TCP나 UDP가 아니라 **IP 위에 직접**(프로토콜 번호 89) 실림. 신뢰성 있는 전달(확인 응답, 재전송)은 OSPF가 직접 처리
- 링크 비용은 대역폭, 지연 등 관리자가 정함
- 각 라우터가 전체 토폴로지를 가지고 다익스트라로 포워딩 테이블을 계산
- 모든 OSPF 메시지는 **인증**됨 (악의적인 침입 방지)

### 계층적 OSPF

- 큰 AS는 **영역(area)** 과 **백본(backbone)** 의 2단계로 나눔. LSA는 영역 안이나 백본 안에서만 플러딩됨
- 각 라우터는 자기 영역의 토폴로지는 자세히 알고, 다른 영역의 목적지는 방향만 앎

| 라우터 | 역할 |
| --- | --- |
| 내부 라우터 (internal) | 영역 안에서만 LSA를 플러딩하고 영역 안 경로를 계산. 바깥으로는 영역 경계 라우터를 통해 보냄 |
| 영역 경계 라우터 (area border) | 자기 영역 목적지까지의 거리를 요약해서 백본에 광고 |
| 백본 라우터 (backbone) | 백본 안에서 OSPF 실행 |
| 경계 라우터 (boundary) | 다른 AS와 연결 |

---

## 4. BGP: AS 사이 라우팅

- AS 사이 라우팅의 사실상 표준. "인터넷을 하나로 묶는 접착제"
- 서브넷이 "나 여기 있어, 이리로 오면 돼"라고 인터넷 전체에 알릴 수 있게 해 줌
- BGP가 각 AS에 주는 것
  - **eBGP**: 이웃 AS로부터 목적지(프리픽스) 도달 정보를 받음
  - **iBGP**: 그 정보를 AS 안의 모든 라우터에 전파
  - 도달 정보와 **정책**으로 좋은 경로를 고름
- 게이트웨이 라우터는 eBGP와 iBGP를 모두 실행함

### BGP의 기본

- **경로 벡터(path vector)** 프로토콜: 거리뿐 아니라 목적지까지 지나는 **AS 목록 전체**를 광고함
- BGP 라우터(피어)끼리 **반영구적인 TCP 연결**(포트 179)로 메시지를 주고받음

| 메시지 | 역할 |
| --- | --- |
| OPEN | 피어와 TCP 연결을 열고 상대를 인증 |
| UPDATE | 새 경로를 광고하거나 옛 경로를 철회 |
| KEEPALIVE | UPDATE가 없을 때 연결 유지. OPEN에 대한 확인 응답 |
| NOTIFICATION | 오류 보고, 연결 종료 |

### 경로 속성

- 광고되는 경로 = **프리픽스 + 속성**
- **AS-PATH**: 이 광고가 지나온 AS 목록. 자기 AS 번호가 들어 있으면 루프이므로 버림
- **NEXT-HOP**: 다음 AS로 가는 AS 내부의 특정 라우터(게이트웨이)의 주소
- **정책 기반 라우팅**: 게이트웨이는 import 정책으로 경로를 받거나 거부하고(예: AS Y는 절대 거치지 않음), 어떤 경로를 이웃 AS에 광고할지도 정책으로 정함

### 핫 포테이토 라우팅

- AS 안의 라우터가 같은 목적지로 가는 게이트웨이를 여러 개 알 때, **AS 내부 비용이 가장 작은 게이트웨이**를 고르는 방식
- AS 바깥 비용(AS 홉 수)은 고려하지 않음. 뜨거운 감자를 쥐고 있지 않고 최대한 빨리 넘기는 것처럼, 자기 네트워크에서 패킷을 빨리 내보냄

### BGP 경로 선택 순서

> **(중요!!)** 순서대로 외우기
{: .prompt-warning }

1. **local preference**가 가장 큰 경로 (정책. 가장 우선)
2. **AS-PATH가 가장 짧은** 경로
3. **NEXT-HOP 라우터가 가장 가까운** 경로 (핫 포테이토)
4. 그 밖의 기준 (라우터 ID 등)

### BGP 경로 선택 (실행)

목적지 하나로 가는 경로 세 개를 받은 라우터가 위 순서대로 고르는 과정.

```python
# BGP 경로 선택: 1) local preference 큰 것 2) AS-PATH 짧은 것 3) NEXT-HOP까지 AS 내부 비용 작은 것(핫 포테이토) 4) 기타(여기서는 라우터 ID)
routes = [
    {"via": "2a", "local_pref": 100, "as_path": ["AS3", "AS1"],        "igp_cost": 2, "router_id": 1},
    {"via": "2c", "local_pref": 100, "as_path": ["AS3"],               "igp_cost": 5, "router_id": 2},
    {"via": "2b", "local_pref": 100, "as_path": ["AS4", "AS5", "AS3"], "igp_cost": 1, "router_id": 3},
]

def best(routes):
    return min(routes, key=lambda r: (-r["local_pref"], len(r["as_path"]), r["igp_cost"], r["router_id"]))

def hot_potato(routes):
    return min(routes, key=lambda r: r["igp_cost"])          # AS 내부 비용만 봄

print("BGP 선택 규칙 :", best(routes)["via"])
print("핫 포테이토만 :", hot_potato(routes)["via"])
routes[0]["local_pref"] = 200                                 # 정책: 2a 쪽 이웃을 선호
print("local_pref 변경 후:", best(routes)["via"])
```

실행 결과

```text
BGP 선택 규칙 : 2c
핫 포테이토만 : 2b
local_pref 변경 후: 2a
```

- local preference가 모두 같으므로 AS-PATH가 가장 짧은 2c(AS 1개)를 고름. 2c는 AS 내부 비용이 가장 크지만 2순위 기준에서 이미 결정됨
- 핫 포테이토만 쓰면 AS 내부 비용이 가장 작은 2b를 고름 (AS를 3개나 거치는데도)
- 정책으로 local preference를 바꾸면 AS-PATH가 더 길어도 2a가 선택됨 → **정책이 성능보다 우선**

### AS 내부와 AS 사이 라우팅이 다른 이유

| 기준 | AS 사이 | AS 내부 |
| --- | --- | --- |
| 정책 | 각 AS가 자기 트래픽이 어디로 가는지, 누구의 트래픽을 실어 줄지 통제하고 싶어 함 → 정책이 핵심 | 관리자가 한 명이라 정책 문제가 거의 없음 |
| 규모 | 계층적 라우팅으로 테이블 크기와 갱신 트래픽을 줄여야 함 | 상대적으로 작음 (큰 AS는 계층적 OSPF로 해결) |
| 성능 | 정책이 성능보다 우선 | 성능(최소 비용)에 집중 |

---

## 5. SDN과 OpenFlow

### SDN의 특징 4가지

1. 일반화된 **플로우 기반 포워딩** (예: OpenFlow)
2. **제어 평면과 데이터 평면의 분리**
3. 제어 평면 기능이 데이터 평면 스위치 **바깥**(컨트롤러)에 있음
4. **프로그래밍 가능한 제어 응용**

### 구성 요소

| 구성 요소 | 역할 |
| --- | --- |
| 데이터 평면 스위치 | 빠르고 단순한 화이트박스 스위치. 컨트롤러가 계산한 플로우 테이블대로 동작하고, OpenFlow 같은 프로토콜로 컨트롤러와 통신 |
| SDN 컨트롤러 (네트워크 OS) | 네트워크 상태(링크, 스위치, 호스트, 통계, 플로우 테이블)를 유지. 위로는 **northbound API**로 제어 응용과, 아래로는 **southbound API**로 스위치와 통신. 성능과 장애 대응을 위해 분산 시스템으로 구현 |
| 네트워크 제어 응용 | 제어의 "두뇌". 컨트롤러의 API로 라우팅, 접근 제어, 부하 분산 등을 구현. 컨트롤러 제조사와 별개로 제3자가 만들 수 있음(unbundled) |

### OpenFlow 프로토콜

- **컨트롤러와 스위치 사이**에서 동작하고, **TCP**로 메시지를 주고받음 (암호화 선택)
- OpenFlow **프로토콜**은 둘 사이의 대화 규칙이고, OpenFlow **API**는 스위치에 시킬 수 있는 동작의 목록이라 서로 다름

| 방향 | 메시지 | 역할 |
| --- | --- | --- |
| 컨트롤러 → 스위치 | features | 스위치의 기능을 물어봄 |
| | configure | 스위치 설정을 조회하거나 바꿈 |
| | modify-state | 플로우 테이블 엔트리를 추가, 삭제, 수정 |
| | packet-out | 특정 포트로 이 패킷을 내보내라고 지시 |
| 스위치 → 컨트롤러 | packet-in | 매치되는 규칙이 없는 패킷 등을 컨트롤러로 보냄 |
| | flow-removed | 플로우 엔트리가 삭제되었음을 알림 |
| | port status | 포트 상태 변화를 알림 |

- packet-in을 받은 컨트롤러는 규칙을 만들어 modify-state로 설치하거나, packet-out으로 그 패킷만 처리하게 함
- 운영자가 스위치를 직접 프로그래밍하지 않고, 컨트롤러 위의 응용이 대신함
- 대표적인 오픈 소스 컨트롤러: **ONOS**(제어 응용 분리, "무엇을" 원하는지 선언하는 intent 프레임워크, 분산 코어), **OpenDaylight(ODL)**

---

## 6. ICMP와 네트워크 관리

### ICMP

- 호스트와 라우터가 네트워크 계층 정보를 주고받는 프로토콜
  - **오류 보고**: 호스트, 네트워크, 포트, 프로토콜에 도달할 수 없음
  - **에코 요청/응답**: `ping`
- IP 위에서 동작 (ICMP 메시지는 IP 데이터그램에 실림)
- 메시지 = Type + Code + **오류를 일으킨 IP 데이터그램의 앞 8 bytes** (원래 헤더 포함)
- `traceroute`는 TTL을 늘려 가며 받은 ICMP Time Exceeded(Type 11)로 경로를 알아냄 ([Wireshark 실습](/posts/Internet_Engineering_4/))

### 네트워크 관리

구성 요소: **관리 서버**(사람이 쓰는 응용), **관리되는 장치**(설정 가능한 하드웨어/소프트웨어), **데이터**(설정, 운영 상태, 통계), **관리 프로토콜**

운영자가 네트워크를 관리하는 방법 세 가지

| 방법 | 내용 |
| --- | --- |
| CLI | 장치마다 직접 명령을 입력하거나 스크립트 실행 (SSH 등) |
| SNMP/MIB | SNMP로 장치의 MIB 값을 조회하거나 설정 |
| NETCONF/YANG | 여러 장치의 설정을 네트워크 전체 관점에서 한꺼번에 관리. YANG은 데이터 모델링 언어, NETCONF는 YANG 형식의 동작과 데이터를 장치와 주고받는 프로토콜 |

### SNMP

- 두 가지 통신 방식
  - **요청/응답**: 관리 서버(manager)가 장치(agent)에 묻고 agent가 답함
  - **트랩(trap)**: 링크 다운 같은 **예외 상황**이 생기면 agent가 먼저 manager에게 알림

| 메시지 | 방향 | 기능 |
| --- | --- | --- |
| GetRequest | manager → agent | 데이터 하나 요청 |
| GetNextRequest | manager → agent | 목록의 다음 데이터 요청 |
| GetBulkRequest | manager → agent | 데이터 블록 요청 |
| SetRequest | manager → agent | MIB 값 설정 |
| Response | agent → manager | 요청에 대한 응답 |
| Trap | agent → manager | 예외 이벤트 알림 |

- **MIB**(Management Information Base): 장치의 관리 데이터를 모은 것. 데이터 정의 언어는 **SMI**
- 예: UDP MIB 모듈의 `udpInDatagrams`(받은 데이터그램 수), `udpNoPorts`(포트에 응용이 없어 전달하지 못한 수), `udpInErrors`(그 밖의 이유로 전달하지 못한 수), `udpOutDatagrams`(보낸 수), `udpTable`(사용 중인 포트 목록)

### NETCONF와 YANG

- **NETCONF**: 네트워크 전체의 장치를 능동적으로 관리하고 설정. 여러 장치에 대한 **원자적 커밋**(전부 적용되거나 전부 취소), **RPC** 방식
- **YANG**: NETCONF 관리 데이터의 구조, 문법, 의미를 정하는 데이터 모델링 언어. 데이터 사이의 제약 조건을 표현해 설정이 정확하고 일관되게 함

---

### 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 라우팅 알고리즘 분류 | 거리 벡터는 "동적 프로그래밍"이면서 "(그리디 알고리즘)" | 거리 벡터는 동적 프로그래밍, 다익스트라가 그리디 | 벨만-포드는 부분 문제의 최적값을 이용하는 DP이고, 매 단계 최소 비용 노드를 확정하는 것은 다익스트라 |
| 라우팅 알고리즘 분류 | 링크 상태: "모든 라우터가 포워딩 테이블을 가지고" | 모든 라우터가 **전체 토폴로지(링크 비용)** 를 가지고 | 포워딩 테이블은 두 방식 모두 가지고 있음. 차이는 계산에 쓰는 정보의 범위 |
| OSPF | TCP/UDP 사용 안 함 → 빠름 | IP 위에 직접 실림(프로토콜 89) | "빠르다"는 근거가 없음. 신뢰성은 OSPF가 직접 처리 |
| SNMP MIB | `UDInErrors` | `udpInErrors` | 오타 |
| 링크 상태 vs 거리 벡터 비교표 | 링크 상태는 빠른 수렴 | 링크 상태는 메시지 $$O(n^2)$$와 진동 가능성, 거리 벡터는 느린 수렴과 무한 계수 | 일방적으로 어느 쪽이 낫다고 할 수 없음 (교재 5.2절 비교) |

---

## 최종 핵심 정리

1. 제어 평면은 라우터별 분산 제어 또는 SDN의 논리적 중앙 집중 제어로 구현됨
2. 링크 상태는 전체 토폴로지로 다익스트라(그리디)를 돌리고, 거리 벡터는 이웃의 벡터로 벨만-포드(DP)를 반복함
3. 거리 벡터는 비용이 줄면 빠르게, 늘면 무한 계수 문제로 느리게 수렴하며 poisoned reverse로 일부 완화함
4. AS 내부 라우팅은 RIP(DV), EIGRP(DV), OSPF(LS)이고, OSPF는 LSA를 플러딩하며 IP 위에 직접 실림
5. 계층적 OSPF는 영역과 백본으로 나누고, 내부/영역 경계/백본/경계 라우터가 있음
6. BGP는 TCP 위의 경로 벡터 프로토콜이고, eBGP로 이웃 AS의 경로를 받아 iBGP로 AS 안에 퍼뜨림
7. BGP 경로 선택은 local preference → 짧은 AS-PATH → 가까운 NEXT-HOP(핫 포테이토) → 기타 순서이며, 정책이 성능보다 우선
8. SDN은 컨트롤러와 OpenFlow(packet-in, modify-state, packet-out 등)로 스위치를 제어하고, 네트워크 관리는 SNMP(요청/응답, 트랩)와 NETCONF/YANG으로 함

---

End.
