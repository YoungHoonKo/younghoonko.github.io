---
title: 04. Circuit Theory Lecture_Capacitors, Inductors and Transients
description: "회로이론 강의 Ch6~7: 리액턴스와 임피던스, 커패시터와 인덕터의 v-i 관계·에너지·연속성, 직렬·병렬 합성, 1차 RC·RL 회로의 미분방정식과 해, 시정수, 테브난으로 시정수 구하기, 단계별 풀이 전략, 2차 회로의 특성방정식"
Author: YoungHoon Ko
date: 2022-12-07 18:00:00 +0900
categories: [University(2022-Second-Semester), Circuit Theory]
tags: [circuit]
math: true
image: /assets/img/lang/circuit.png
---

## 학습 목표

- 리액턴스, 임피던스, 저항의 차이
- 커패시터와 인덕터의 v-i 관계와 저장 에너지
- 커패시터 전압과 인덕터 전류가 순간적으로 바뀌지 않는 이유
- 커패시터·인덕터의 직렬·병렬 합성
- 1차 회로의 일반해 $$x(t) = K_1 + K_2 e^{-t/\tau}$$와 단계별 풀이
- 2차 회로의 특성방정식과 감쇠의 세 가지 경우

---

## 1. 에너지 저장 소자 (Ch6)

- **리액턴스(X)**: 커패시턴스나 인덕턴스 때문에 교류에 생기는 저항 성분
- **임피던스(Z)**: 저항과 리액턴스를 합친 복소수 $$Z = R + jX$$, 단위 Ω. 저항은 직류, 임피던스는 교류에 대한 반대 작용

### 커패시터와 인덕터 비교

| | 커패시터 (C) | 인덕터 (L) |
| --- | --- | --- |
| 구조 | 절연체를 사이에 둔 두 도체판, $$C = \dfrac{\varepsilon A}{d}$$ | 코일 |
| 단위 | 패럿 (F), 보통 μF, nF, pF | 헨리 (H), 보통 mH, μH |
| v-i 관계 | $$i = C\dfrac{dv}{dt}$$ | $$v = L\dfrac{di}{dt}$$ |
| 적분형 | $$v(t) = \dfrac{1}{C}\displaystyle\int_{t_0}^{t} i\,dx + v(t_0)$$ | $$i(t) = \dfrac{1}{L}\displaystyle\int_{t_0}^{t} v\,dx + i(t_0)$$ |
| 저장 에너지 | $$w = \dfrac{1}{2}Cv^2$$ | $$w = \dfrac{1}{2}Li^2$$ |
| 직류 정상상태 | **개방** ($$dv/dt = 0$$ → $$i = 0$$) | **단락** ($$di/dt = 0$$ → $$v = 0$$) |
| 연속인 양 | 전압 $$v_C(0^-) = v_C(0^+)$$ | 전류 $$i_L(0^-) = i_L(0^+)$$ |

> **(중요!!)** 커패시터 전압과 인덕터 전류는 **순간적으로 바뀔 수 없다.** 바뀌려면 $$i = C\,dv/dt$$나 $$v = L\,di/dt$$가 무한대가 되어야 하기 때문이다. 과도 해석의 초기 조건은 모두 여기서 나온다.
{: .prompt-warning }

### 합성

| | 직렬 | 병렬 |
| --- | --- | --- |
| 커패시터 | $$\dfrac{1}{C_s} = \dfrac{1}{C_1} + \dfrac{1}{C_2} + \cdots$$ | $$C_p = C_1 + C_2 + \cdots$$ |
| 인덕터 | $$L_s = L_1 + L_2 + \cdots$$ | $$\dfrac{1}{L_p} = \dfrac{1}{L_1} + \dfrac{1}{L_2} + \cdots$$ |

커패시터는 저항과 **반대**, 인덕터는 저항과 **같은** 규칙이다.

---

## 2. 1차 과도 회로 (Ch7)

에너지 저장 소자가 **하나**인 회로를 1차 회로라 한다. 스위치가 바뀌면 전압과 전류가 바로 새 값으로 가지 못하고 지수적으로 이동한다.

### 일반해

1차 회로의 미분방정식은 다음 꼴이다.

$$\frac{dx(t)}{dt} + a\,x(t) = A$$

해 = 강제응답(정상상태) + 고유응답(과도):

$$x(t) = K_1 + K_2 e^{-t/\tau}, \qquad \tau = \frac{1}{a}, \qquad K_1 = x(\infty), \qquad K_1 + K_2 = x(0^+)$$

### 시정수

소자 단자에서 본 **테브난 저항** $$R_{Th}$$로 구한다.

$$\tau_{RC} = R_{Th}C, \qquad \tau_{RL} = \frac{L}{R_{Th}}$$

$$t = \tau$$에서 과도 성분은 $$e^{-1} \approx 36.8\%$$가 남고, $$5\tau$$ 뒤에는 0.7% 미만이 되어 사실상 정상상태가 된다.

### 단계별 풀이 전략

1. $$x(t) = K_1 + K_2 e^{-t/\tau}$$ 꼴을 가정
2. $$t < 0$$ 정상상태 회로(C 개방, L 단락)에서 $$v_C(0^-)$$ 또는 $$i_L(0^-)$$ 구하기
3. 연속성으로 $$x(0^+)$$ 결정
4. $$t \to \infty$$ 정상상태 회로에서 $$K_1 = x(\infty)$$
5. 전원을 끄고 소자에서 본 $$R_{Th}$$로 $$\tau$$ 계산
6. $$K_2 = x(0^+) - K_1$$로 완성하고, 원하는 다른 변수는 옴의 법칙이나 분배로 구한다

### 예제 (강의 Ex. 7.3)

4A 전류원, 20Ω 저항, 5μF 커패시터가 모두 병렬이고, $$t = 0$$에 전류원이 0이 된다.

1. $$t < 0$$: 커패시터는 개방이므로 4A가 모두 20Ω으로 → $$v_C(0^-) = 80\text{V}$$
2. $$t > 0$$: 20Ω을 통해 방전, $$v_C(\infty) = 0$$, $$\tau = RC = 20 \times 5 \times 10^{-6} = 100\mu\text{s}$$
3. 따라서 $$v_C(t) = 80e^{-t/100\mu\text{s}}\ \text{V}$$

(a) 전압이 절반: $$e^{-t/\tau} = 1/2 \Rightarrow t = \tau\ln 2 \approx 69.3\mu\text{s}$$

(b) 에너지가 절반: $$w \propto v^2$$이므로 $$e^{-2t/\tau} = 1/2 \Rightarrow t = \dfrac{\tau}{2}\ln 2 \approx 34.7\mu\text{s}$$

```python
import math
tau = 20 * 5e-6
print(tau * math.log(2) * 1e6)       # 69.3 us
print(tau / 2 * math.log(2) * 1e6)   # 34.7 us
```

강의의 다른 예제도 같은 방식이다. E7.1은 $$v_C(0^-) = 8\text{V}$$, $$\tau = (R_1 + R_2)C = 6\text{k}\Omega \times 100\mu\text{F} = 0.6\text{s}$$로 $$v_C(t) = 8e^{-t/0.6}\ \text{V}$$, E7.2는 $$v_o(0) = 7\text{V}$$, $$v_o(\infty) = 12\text{V}$$, $$\tau = 6\text{k}\Omega \times 2.5\mu\text{F} = 15\text{ms}$$로 $$v_o(t) = 12 - 5e^{-t/0.015}\ \text{V}$$다.

---

## 3. 2차 회로

에너지 저장 소자가 **두 개**면 2차 미분방정식이 된다. 직렬 RLC의 무전원 응답은

$$\frac{d^2 i}{dt^2} + \frac{R}{L}\frac{di}{dt} + \frac{1}{LC}i = 0 \;\Rightarrow\; s^2 + 2\alpha s + \omega_0^2 = 0, \qquad \alpha = \frac{R}{2L},\; \omega_0 = \frac{1}{\sqrt{LC}}$$

| 조건 | 이름 | 응답 |
| --- | --- | --- |
| $$\alpha > \omega_0$$ | 과감쇠 | 서로 다른 두 실근, 진동 없이 천천히 감소 |
| $$\alpha = \omega_0$$ | 임계감쇠 | 중근, 진동 없이 가장 빠르게 감소 |
| $$\alpha < \omega_0$$ | 부족감쇠 | 복소근, $$e^{-\alpha t}$$로 줄어드는 진동 |

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| Ch7 Ex. 7.3 (b) | The energy stored in the capacitor dropped to tits initial value | dropped to half its initial value | 분수 기호가 깨진 것으로 보임. (a)와 짝을 이루는 "절반"이 문제의 의도 |
| Ch7 풀이 전략 슬라이드 | Theveinin Equivalent Circuit | Thevenin | 철자 오류 |
| Ch6 리액턴스 슬라이드 | 2.1 Ohm's Law 머리글 | 6장 내용 | Ch2 슬라이드 머리글이 그대로 남음 |

---

## 최종 핵심 정리

1. 커패시터는 i = C dv/dt, 인덕터는 v = L di/dt이고 저장 에너지는 각각 ½Cv², ½Li²다
2. 직류 정상상태에서 커패시터는 개방, 인덕터는 단락으로 본다
3. 커패시터 전압과 인덕터 전류는 순간적으로 바뀌지 않아 과도 해석의 초기 조건이 된다
4. 커패시터의 직병렬 합성은 저항과 반대, 인덕터는 저항과 같다
5. 1차 회로의 해는 x(t) = x(∞) + [x(0⁺) - x(∞)]e^(-t/τ)다
6. 시정수는 소자에서 본 테브난 저항으로 RC는 RthC, RL은 L/Rth다
7. 에너지는 전압의 제곱에 비례하므로 에너지가 절반이 되는 시간은 전압이 절반이 되는 시간의 절반이다
8. 2차 회로는 α와 ω0의 대소에 따라 과감쇠, 임계감쇠, 부족감쇠로 나뉜다

---

End.
