---
title: 01. Circuit Theory Lecture_Basic Concepts and Resistive Circuits
description: "회로이론 강의 Ch1~2 (Irwin, Basic Engineering Circuit Analysis 11판): SI 단위와 전하·전류·전압·전력, 수동 부호 규약, 독립·종속 전원, 옴의 법칙과 컨덕턴스, KCL과 KVL, 직렬·병렬 합성, 전압·전류 분배, 종속 전원이 있는 회로"
Author: YoungHoon Ko
date: 2022-09-21 18:00:00 +0900
categories: [University(2022-Second-Semester), Circuit Theory]
tags: [circuit]
math: true
image: /assets/img/lang/circuit.png
---

## 학습 목표

- 전하, 전류, 전압, 전력, 에너지의 정의와 단위
- 수동 부호 규약으로 소비·공급 전력을 판단하는 방법
- 독립 전원과 네 종류의 종속 전원
- 옴의 법칙, 컨덕턴스, 저항이 소비하는 전력
- 키르히호프의 전류 법칙(KCL)과 전압 법칙(KVL)
- 직렬·병렬 합성과 전압·전류 분배 공식

과제(파이썬으로 P = VI, 합성 저항 계산)는 기존 글 "02. Circuit Theory(Ohm's Law, P = VI)", "03. Circuit Theory(Sum of the series and parallel resistors)"에 있다. 이 글은 강의 내용을 정리한다.

---

## 1. 기본 물리량 (Ch1)

| 양 | 기호 | 정의 | 단위 |
| --- | --- | --- | --- |
| 전하 | $$q$$ | 전자 1개 = $$-1.602 \times 10^{-19}$$ C | 쿨롱 (C) |
| 전류 | $$i$$ | $$i(t) = \dfrac{dq(t)}{dt}$$ | 암페어 (A = C/s) |
| 전압 | $$v$$ | 단위 전하를 옮기는 데 드는 에너지 $$v = \dfrac{dw}{dq}$$ | 볼트 (V = J/C) |
| 전력 | $$p$$ | $$p = \dfrac{dw}{dt} = \dfrac{dw}{dq}\dfrac{dq}{dt} = vi$$ | 와트 (W = J/s) |
| 에너지 | $$w$$ | $$w = \int p\,dt$$ | 줄 (J) |

- 전류의 방향은 **양전하가 움직이는 방향**으로 정의한다 (전자는 반대로 움직인다)
- 전류가 일정하면 DC, 시간에 따라 사인파로 바뀌면 AC
- 전압은 항상 **두 점 사이**의 값이다. 기준점(접지)을 정하지 않은 "어떤 노드의 전압 4V"는 의미가 없다

### 수동 부호 규약 (Passive Sign Convention)

전류가 소자의 **+ 단자로 들어가면** $$p = vi$$는 소자가 **흡수(소비)** 하는 전력이다.

$$p = vi > 0 \;\Rightarrow\; \text{흡수}, \qquad p = vi < 0 \;\Rightarrow\; \text{공급}$$

> **(중요!!)** 회로 전체에서 **흡수 전력의 합 = 0** (공급 전력은 음수로 계산되므로). 문제를 다 풀고 전력 합이 0인지 확인하면 계산 실수를 잡을 수 있다.
{: .prompt-warning }

### 전원

| 종류 | 설명 |
| --- | --- |
| 독립 전압원 | 연결된 회로와 관계없이 단자 전압이 정해짐 |
| 독립 전류원 | 연결된 회로와 관계없이 전류가 정해짐 |
| VCVS | 전압 제어 전압원 $$v = \mu v_x$$ |
| CCVS | 전류 제어 전압원 $$v = r i_x$$ |
| VCCS | 전압 제어 전류원 $$i = g v_x$$ |
| CCCS | 전류 제어 전류원 $$i = \beta i_x$$ |

종속 전원은 마름모 기호로 그리며, 트랜지스터나 op amp 같은 능동 소자를 모델링할 때 쓴다.

---

## 2. 옴의 법칙 (Ch2.1)

$$v(t) = R\,i(t), \qquad i = Gv, \quad G = \frac{1}{R}\ (\text{지멘스, S})$$

$$p = vi = i^2 R = \frac{v^2}{R} \ge 0$$

- 저항은 항상 전력을 **흡수**하고 열로 소모한다
- 옴의 법칙은 실제 소자의 v-i 관계를 **선형 영역**에서 근사한 것
- 단락(short): $$R = 0$$, 전압 0 / 개방(open): $$R = \infty$$, 전류 0
- 실무에서는 V/mA 조합이 자주 나오고 결과가 kΩ이 된다

---

## 3. 키르히호프의 법칙 (Ch2.2)

| 법칙 | 내용 | 근거 |
| --- | --- | --- |
| **KCL** | 한 노드로 들어오는 전류의 대수합 = 0 | 전하 보존 |
| **KVL** | 닫힌 경로를 한 바퀴 도는 전압의 대수합 = 0 | 에너지 보존 |

$$\sum_{k} i_k = 0 \;(\text{노드}), \qquad \sum_{k} v_k = 0 \;(\text{루프})$$

- **노드**: 두 개 이상의 소자가 만나는 점. 도선으로만 이어진 점들은 같은 노드
- **루프**: 같은 노드를 두 번 지나지 않는 닫힌 경로
- 노드가 $$N$$개면 독립인 KCL 식은 $$N-1$$개

---

## 4. 직렬·병렬과 분배 공식 (Ch2.3~2.6)

| | 직렬 | 병렬 |
| --- | --- | --- |
| 공통인 것 | 전류 | 전압 |
| 합성 | $$R_s = R_1 + R_2 + \cdots$$ | $$\dfrac{1}{R_p} = \dfrac{1}{R_1} + \dfrac{1}{R_2} + \cdots$$ |
| 두 개일 때 | | $$R_1 \parallel R_2 = \dfrac{R_1 R_2}{R_1 + R_2}$$ |
| 분배 | **전압 분배** $$v_1 = \dfrac{R_1}{R_1 + R_2}v$$ | **전류 분배** $$i_1 = \dfrac{R_2}{R_1 + R_2}i$$ |

> 전류 분배에서는 분자에 **반대편 저항**이 온다. 저항이 작은 쪽으로 전류가 더 많이 흐른다.
{: .prompt-danger }

### 예제: 직병렬 회로

12V 전원에 $$R_1 = 2\text{k}\Omega$$이 직렬로 있고, 그 뒤에 $$6\text{k}\Omega \parallel 3\text{k}\Omega$$가 있는 회로.

1. 합성: $$6\text{k} \parallel 3\text{k} = 2\text{k}\Omega$$, 전체 $$R = 4\text{k}\Omega$$
2. 전원 전류: $$I = 12/4\text{k} = 3\text{mA}$$
3. 전압 분배: 병렬부 전압 $$= 12 \times \dfrac{2\text{k}}{4\text{k}} = 6\text{V}$$
4. 전류 분배: $$I_{6k} = 3\text{mA} \times \dfrac{3}{9} = 1\text{mA}$$, $$I_{3k} = 2\text{mA}$$
5. 전력 확인: 저항 흡수 $$18 + 6 + 12 = 36\text{mW}$$ = 전원 공급 $$12 \times 3 = 36\text{mW}$$

```python
R1, Ra, Rb, Vs = 2e3, 6e3, 3e3, 12
Rp = Ra * Rb / (Ra + Rb)
I = Vs / (R1 + Rp)
Vp = I * Rp
P = [I**2 * R1, Vp**2 / Ra, Vp**2 / Rb]
print(I * 1e3, Vp, [p * 1e3 for p in P], Vs * I * 1e3)
# 3.0 mA, 6.0 V, [18, 6, 12] mW, 36 mW
```

### 회로를 줄였다가 되돌아가기

복잡한 회로는 (1) 직병렬 합성으로 **단일 루프**까지 줄이고, (2) 전원 전류를 구한 뒤, (3) 옴의 법칙, KCL, KVL로 **거꾸로 되짚어** 각 소자의 전압·전류를 구한다. Ch3의 노드 해석법은 이 과정을 체계화한 것이다.

### 종속 전원이 있는 회로

종속 전원은 합성으로 없앨 수 없다. 제어 변수($$v_x$$ 또는 $$i_x$$)를 회로 변수로 표현하는 식을 하나 더 세워 KCL·KVL과 함께 푼다.

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| Ch2 2.1 슬라이드 | A common occurance is Volt/mA | occurrence | 철자 오류 |
| Ch1 강의계획서 | 1st year-Feshman | Freshman | 철자 오류 |

---

## 최종 핵심 정리

1. 전류는 전하의 시간 변화율, 전압은 단위 전하당 에너지, 전력은 p = vi다
2. 수동 부호 규약에서 전류가 + 단자로 들어가면 vi는 흡수 전력이다
3. 회로 전체의 흡수 전력 합은 0이므로 풀이 검산에 쓸 수 있다
4. 종속 전원은 VCVS, CCVS, VCCS, CCCS 네 종류이며 합성으로 없앨 수 없다
5. 저항은 v = Ri를 따르고 항상 i²R의 전력을 소비한다
6. KCL은 전하 보존, KVL은 에너지 보존에서 나온다
7. 직렬은 전류가 같아 전압을 분배하고, 병렬은 전압이 같아 전류를 분배한다
8. 전류 분배 공식의 분자에는 반대편 저항이 들어간다

---

End.
