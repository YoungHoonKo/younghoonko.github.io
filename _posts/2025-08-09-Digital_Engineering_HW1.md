---
title: 01. Digital_Engineering_HW1 Sampling, Quantization and Coding
description: "디지털공학 과제 1 (C): -5V~5V 아날로그 샘플 5개를 8레벨로 양자화하고 3비트 2진 코드로 바꾸는 프로그램. 양자화 간격 1.25V 계산, 비트 연산으로 2진 출력, 경계값 테스트와 제출 규칙 점검"
Author: YoungHoon Ko
date: 2025-08-09 09:55:32 +0900
categories: [University(2025-First-Semester), Digital_Engineering]
tags: [c, digital]
math: true
image: /assets/img/lang/C.png
---

## 학습 목표

- 아날로그 신호를 디지털로 바꾸는 세 단계: 샘플링, 양자화, 코딩
- 전압 범위와 레벨 수로 양자화 간격 구하기
- 전압을 양자화 레벨 번호로 바꾸는 식과 최댓값 처리
- 비트 연산(`>>`, `&`)으로 정수를 n비트 2진수로 출력하기
- 경계값(-5V, 0V, 5V)으로 프로그램 검증하기

---

## 1. 과제

> 1. Program in C language to print as follows:
>
> ```text
> Type the values of 5 sampled points: 0 1.2 1.5 2.3 2.5
> Digitized signal after quantization and coding is 100 100 101 101 110
> ```
>
> 2. Consider followings
> - The analog input voltage level is from -5V to 5V
> - The quantization level is 8
> - -5V is mapped to Quantization level 0 and 0V is mapped to Quantization level 4
>
> 파일 이름은 `Class#_ID_HW1.c`, 코드 첫 줄에 `#define _CRT_SECURE_NO_WARNINGS`, 모든 변수는 사용 전에 초기화, Visual Studio 2022 사용
{: .prompt-info }

---

## 2. 원리: 샘플링, 양자화, 코딩

| 단계 | 하는 일 | 이 과제에서 |
| --- | --- | --- |
| 샘플링 | 연속 신호를 일정 시간 간격으로 측정 | 샘플 5개가 이미 주어짐 |
| 양자화 | 측정값을 정해진 레벨 중 하나로 반올림·내림 | 8레벨 |
| 코딩 | 레벨 번호를 2진 코드로 | 3비트 ($$2^3 = 8$$) |

전압 범위 10V를 8칸으로 나누므로 양자화 간격은

$$\Delta = \frac{5 - (-5)}{8} = 1.25\text{ V}$$

전압 $$v$$의 레벨은 아래에서부터 몇 칸째인지로 정한다.

$$\text{level} = \left\lfloor \frac{v - (-5)}{1.25} \right\rfloor$$

| 레벨 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 전압 구간(V) | -5~-3.75 | ~-2.5 | ~-1.25 | ~0 | 0~1.25 | ~2.5 | ~3.75 | ~5 |
| 코드 | 000 | 001 | 010 | 011 | 100 | 101 | 110 | 111 |

0V는 $$(0 + 5) / 1.25 = 4$$로 레벨 4가 되어 문제 조건과 맞는다. 5V는 식대로면 8이 되지만 레벨은 0~7뿐이므로 **7로 묶어야** 한다.

---

## 3. 코드

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>

/* 전압 → 양자화 레벨 (0~7) */
int q_lvls_f(double x) {
    int q = (int)((x - (-5.0)) / 1.25);
    if (q > 7) q = 7;      /* 5V는 마지막 레벨로 */
    if (q < 0) q = 0;      /* -5V 미만이 들어와도 0 */
    return q;
}

/* 레벨을 count_bit 비트 2진수로 출력 */
void d_to_b(int lvl, int count_bit) {
    for (int i = count_bit - 1; i >= 0; i--)
        printf("%d", (lvl >> i) & 1);   /* i번째 비트를 맨 오른쪽으로 옮겨 1과 AND */
    printf(" ");
}

int main(void) {
    double s_pnts[5] = {0};
    int q_lvls[5] = {0};

    printf("Type the values of 5 sampled points: ");
    for (int i = 0; i < 5; i++) {
        scanf("%lf", &s_pnts[i]);
        q_lvls[i] = q_lvls_f(s_pnts[i]);
    }

    printf("Digitized signal after quantization and coding is ");
    for (int i = 0; i < 5; i++)
        d_to_b(q_lvls[i], 3);
    printf("\n");
    return 0;
}
```

`(lvl >> i) & 1`은 레벨 5(`101`)에서 i = 2, 1, 0일 때 각각 1, 0, 1을 꺼낸다. 높은 비트부터 꺼내야 MSB가 왼쪽에 온다.

---

## 4. 실행 결과

과제 예시 입력

```text
Type the values of 5 sampled points: 0 1.2 1.5 2.3 2.5
Digitized signal after quantization and coding is 100 100 101 101 110
```

| 입력(V) | $$(v+5)/1.25$$ | 레벨 | 코드 |
| --- | --- | --- | --- |
| 0 | 4.00 | 4 | 100 |
| 1.2 | 4.96 | 4 | 100 |
| 1.5 | 5.20 | 5 | 101 |
| 2.3 | 5.84 | 5 | 101 |
| 2.5 | 6.00 | 6 | 110 |

경계값 테스트

```text
Type the values of 5 sampled points: -5 -0.01 4.99 5 -7
Digitized signal after quantization and coding is 000 011 111 111 000
```

- 1.2V와 0V가 같은 코드가 되는 것처럼, 한 구간 안의 값은 모두 같은 코드가 된다. 이때 생기는 오차가 **양자화 오차**이고 최대 $$\Delta$$(내림 방식)다
- 레벨 수를 늘리면 오차가 줄지만 샘플당 비트 수가 늘어난다(HW2)

> 제출한 코드는 `q < 0` 처리가 없어서 -7V를 넣으면 `(int)(-1.6) = -1`이 되어 `111`처럼 엉뚱한 코드가 나온다(음수의 하위 3비트). 입력 범위가 -5~5V로 정해져 있어 채점에는 문제가 없었지만, 아래쪽도 0으로 묶는 편이 안전하다.
{: .prompt-danger }

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 제출 코드 첫 줄 | `#define _CRT_SECUR_NO_WARNINGS` | `_CRT_SECURE_NO_WARNINGS` | 철자가 틀리면 매크로가 무시되어 Visual Studio에서 `scanf` 경고(C4996)가 그대로 남음. 과제 조건 위반 |
| 제출 코드 | `double s_pnts[5]; int q_lvls[5];` | `= {0}`으로 초기화 | "모든 변수를 사용 전에 초기화" 조건 |
| 제출 코드 | 하한 처리 없음 | `if (q < 0) q = 0;` | -5V 미만 입력 시 음수 레벨 |
| 과제 원문 | Cosider | Consider | 철자 |

---

## 최종 핵심 정리

1. 아날로그를 디지털로 바꾸는 과정은 샘플링, 양자화, 코딩 순서다
2. 양자화 간격은 (최대 전압 - 최소 전압) / 레벨 수이고, 이 과제는 10V / 8 = 1.25V다
3. 레벨 번호는 $$\lfloor (v - V_{min}) / \Delta \rfloor$$로 구하고 0V는 레벨 4가 된다
4. 최댓값 5V는 계산상 8이 되므로 마지막 레벨 7로 묶어야 한다
5. 8레벨은 $$\log_2 8 = 3$$비트로 코딩한다
6. `(x >> i) & 1`로 i번째 비트를 꺼내며, MSB부터 출력해야 순서가 맞다
7. 같은 구간의 값은 같은 코드가 되며 그 차이가 양자화 오차다
8. 과제 조건의 `#define` 철자와 변수 초기화도 채점 대상이므로 확인한다

---

End.
