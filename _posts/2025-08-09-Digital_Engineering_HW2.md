---
title: 02. Digital_Engineering_HW2 Variable Quantization and IEEE 754
description: "디지털공학 과제 2 (C): 양자화 레벨 수를 입력받아 샘플을 양자화하고 필요한 비트 수로 코딩, 2진 실수(-1001010.11101)를 IEEE 754 단정밀도 32비트로 변환. 손 계산으로 부호·지수·가수를 검증"
Author: YoungHoon Ko
date: 2025-08-10 08:25:32 +0900
categories: [University(2025-First-Semester), Digital_Engineering]
tags: [c, digital]
math: true
image: /assets/img/lang/C.png
---

## 학습 목표

- 레벨 수 L에 따라 양자화 간격과 필요한 비트 수($$\lceil \log_2 L \rceil$$) 구하기
- 2진 실수 문자열을 실수 값으로 바꾸기
- IEEE 754 단정밀도(32비트)의 부호, 지수(bias 127), 가수 구조
- 2진 실수를 정규화해 32비트 패턴을 손으로 구하기
- `union`으로 float의 비트 패턴 읽기

---

## 1. 과제

> ```text
> What is the level of Quantization? 16
> Type the values of 5 sampled points: 0 -1.2 1.5 -4.4 2.5
> The level of the input samples are 8 6 10 0 12
> Digitized signal after quantization and coding is 1000 0110 1010 0000 1100
> Type a binary number for float point number: -1001010.11101
> The converted floating number is 11000010100101011101000000000000
> ```
>
> - 입력 전압은 -5V ~ 5V
> - 16레벨이면 -5V는 레벨 0, 0V는 레벨 8
> - 5V는 마지막 양자화 레벨로
> - 단정밀도 부동소수점 사용
{: .prompt-info }

---

## 2. 양자화 (HW1의 일반화)

레벨 수가 L이면

$$\Delta = \frac{10}{L},\qquad \text{level} = \min\left(\left\lfloor \frac{v + 5}{\Delta} \right\rfloor,\ L - 1\right),\qquad \text{비트 수} = \lceil \log_2 L \rceil$$

L = 16이면 $$\Delta = 0.625$$V, 4비트.

| 입력(V) | $$(v+5)/0.625$$ | 레벨 | 코드 |
| --- | --- | --- | --- |
| 0 | 8.00 | 8 | 1000 |
| -1.2 | 6.08 | 6 | 0110 |
| 1.5 | 10.40 | 10 | 1010 |
| -4.4 | 0.96 | 0 | 0000 |
| 2.5 | 12.00 | 12 | 1100 |

```c
int count_bit(int level) {
    return (int)ceil(log2(level));          /* 16 → 4, 5 → 3 */
}

int quantization_lvl(int L, double v) {
    double space = (5.0 - (-5.0)) / L;
    int q = (int)((v - (-5.0)) / space);
    if (q >= L) q = L - 1;                  /* 5V → 마지막 레벨 */
    return q;
}
```

레벨이 2의 거듭제곱이 아니어도 된다. 5레벨을 넣어 보면 3비트로 코딩된다.

```text
What is the level of Quantization? 5
Type the values of 5 sampled points: -5 -0.001 0 4.999 5
The level of the input samples are 0 2 2 4 4
Digitized signal after quantization and coding is 000 010 010 100 100
```

---

## 3. IEEE 754 단정밀도

$$\text{value} = (-1)^S \times (1.F)_2 \times 2^{E - 127}$$

| 필드 | 비트 수 | 내용 |
| --- | --- | --- |
| S (sign) | 1 | 0 양수, 1 음수 |
| E (exponent) | 8 | 실제 지수 + 127 (bias) |
| F (fraction) | 23 | 정규화한 뒤 소수점 아래 부분. 앞의 1은 저장하지 않음(hidden bit) |

### 손 계산: -1001010.11101

1. 부호: 음수 → **S = 1**
2. 값 확인: $$1001010_2 = 74$$, $$0.11101_2 = 0.5 + 0.25 + 0.125 + 0.03125 = 0.90625$$ → $$-74.90625$$
3. 정규화: $$1001010.11101 = 1.00101011101 \times 2^{6}$$
4. 지수: $$6 + 127 = 133 = 10000101_2$$ → **E = 10000101**
5. 가수: 소수점 아래 `00101011101`에 0을 채워 23비트 → **F = 00101011101000000000000**

```text
1 10000101 00101011101000000000000
= 11000010100101011101000000000000
```

과제 예시와 같다.

### 코드: 문자열 → 실수 → 비트 패턴

```c
/* "-1001010.11101" → -74.90625 */
double binary_to_double(char *x) {
    int negative = 0;
    if (x[0] == '-') { negative = 1; x++; }

    char *ptr = strchr(x, '.');
    double rst = 0;
    if (ptr) *ptr = '\0';                     /* 정수부와 소수부 분리 */
    int n = (int)strlen(x);
    for (int i = 0; i < n; i++)
        if (x[i] == '1') rst += pow(2, n - 1 - i);
    if (ptr) {
        ptr++;
        for (int i = 0; ptr[i]; i++)
            if (ptr[i] == '1') rst += pow(2, -(i + 1));
    }
    return negative ? -rst : rst;
}

/* float의 32비트를 그대로 출력 */
void print_float_bits(float x) {
    union { float f; uint32_t i; } u;         /* 같은 4바이트를 두 가지 타입으로 봄 */
    u.f = x;
    for (int i = 31; i >= 0; i--)
        printf("%u", (u.i >> i) & 1u);
    printf("\n");
}
```

`union`은 멤버들이 **같은 메모리**를 공유하므로, float로 쓰고 `uint32_t`로 읽으면 비트 패턴이 그대로 보인다. 포인터 캐스팅(`*(uint32_t *)&x`)보다 C에서 안전한 방법이다.

### 실행 결과

```text
What is the level of Quantization? 16
Type the values of 5 sampled points: 0 -1.2 1.5 -4.4 2.5
The level of the input samples are 8 6 10 0 12
Digitized signal after quantization and coding is 1000 0110 1010 0000 1100
Type a binary number for float point number: -1001010.11101
The converted floating number is 11000010100101011101000000000000
```

Python의 `struct.pack('>f', -74.90625)`로도 같은 32비트가 나오는 것을 확인했다.

> 이 방식은 2진 문자열을 먼저 `double`로 바꾼 뒤 `float`로 줄인다. 입력의 유효 비트가 24비트(hidden bit 포함)를 넘으면 float로 바꿀 때 **반올림**이 일어나므로, 손으로 23비트를 잘라 붙인 결과와 마지막 비트가 다를 수 있다.
{: .prompt-danger }

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 제출 코드 첫 줄 주석 | 부동소주점 | 부동소수점 | 오타 |
| 제출 코드 주석 | `unint(부호없는)` | `uint32_t`: unsigned 32-bit integer type | 철자 |
| 제출 코드 | 첫 줄 주석에 이름·학번 | 블로그에는 생략 | 개인정보 |
| 과제 원문 | Cosider | Consider | 철자 |

---

## 최종 핵심 정리

1. L레벨 양자화의 간격은 10V/L, 필요한 비트 수는 $$\lceil \log_2 L \rceil$$이다
2. 16레벨이면 간격 0.625V, 4비트이며 0V는 레벨 8이다
3. 최댓값은 계산상 L이 되므로 L-1로 묶는다
4. IEEE 754 단정밀도는 부호 1비트, 지수 8비트(bias 127), 가수 23비트다
5. 정규화한 $$1.F$$의 앞 1은 저장하지 않는다(hidden bit)
6. -1001010.11101은 $$-1.00101011101 \times 2^6$$이고 지수 필드는 133 = 10000101이다
7. `union`으로 float를 쓰고 정수로 읽으면 비트 패턴을 볼 수 있다
8. 유효 비트가 24비트를 넘는 입력은 float 변환 때 반올림된다

---

End.
