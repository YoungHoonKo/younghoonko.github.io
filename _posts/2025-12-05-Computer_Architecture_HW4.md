---
title: 04. Computer_Architecture_HW4 (Counting 1 Bits with Shift and AND)
description: "컴퓨터구조 과제 4: 정수의 2진수 표현에서 1의 개수를 세는 C 코드를 MIPS로 변환. andi로 최하위 비트 검사, srl(논리)과 sra(산술) 시프트의 차이, 음수 입력에서 32가 나오는 이유를 C와 SPIM 실행으로 비교"
Author: YoungHoon Ko
date: 2025-12-05 20:10:00 +0900
categories: [University(2025-Second-Semester), Computer Architecture]
tags: [assembly, mips_programming]
image: /assets/img/lang/asm.png
---

## 학습 목표

- C의 `while`과 `if`를 MIPS 분기(`beq`, `j`)로 바꾸는 방법
- 비트 AND(`andi`)로 최하위 비트(LSB)를 검사하는 방법
- 논리 시프트(`srl`)와 산술 시프트(`sra`)의 차이와 `unsigned` 변환의 의미
- 음수를 입력했을 때 2의 보수 표현 때문에 결과가 어떻게 되는지
- C 코드와 MIPS 코드를 같은 입력으로 실행해 비교

---

## 1. 과제 내용

> **과제 4**: 아래 C 코드가 무엇을 하는지 이해하고, 입력 문장과 출력 문장이 같은 MIPS 프로그램을 작성하라. (C와 똑같은 구조일 필요는 없음)
{: .prompt-info }

```c
#include <stdio.h>

int main(void) {
    int n, ones_count;
    unsigned unsigned_n;

    printf("Enter an int\n");
    scanf("%d", &n);

    unsigned_n = (unsigned) n;
    ones_count = 0;

    while (unsigned_n != 0) {
        if (unsigned_n & 1) ones_count++;
        unsigned_n = unsigned_n >> 1;
    }

    printf("The results is %d\n", ones_count);
    return 0;
}
```

**이 코드가 하는 일**: n을 2진수로 썼을 때 **1인 비트의 개수**(population count)를 센다. 맨 오른쪽 비트가 1인지 보고(`& 1`), 한 칸씩 오른쪽으로 밀면서(`>> 1`) 0이 될 때까지 반복한다.

| n | 2진수 | 1의 개수 |
| --- | --- | --- |
| 5 | 101 | 2 |
| 7 | 111 | 3 |
| 255 | 1111 1111 | 8 |

---

## 2. 왜 unsigned로 바꾸는가

`int`를 오른쪽으로 밀 때 C 컴파일러는 보통 **산술 시프트**를 쓴다. 산술 시프트는 부호 비트(맨 왼쪽)를 복사해서 채우기 때문에 음수는 아무리 밀어도 0이 되지 않고 −1(모든 비트 1)에 머문다. → **무한 루프**

`unsigned`로 바꾸면 **논리 시프트**가 되어 왼쪽을 0으로 채우므로 최대 32번 안에 반드시 0이 된다.

| 명령어 | 이름 | 빈자리 채움 | C에서 대응 |
| --- | --- | --- | --- |
| `srl` | shift right logical | 0 | `unsigned >> 1` |
| `sra` | shift right arithmetic | 부호 비트 | `int >> 1` (대부분의 컴파일러) |
| `sll` | shift left logical | 0 | `<< 1` |

MIPS 레지스터에는 "부호 있음/없음" 구분이 없고 **비트만** 있다. 그래서 `(unsigned) n`은 MIPS에서 아무 명령어도 필요 없고, 대신 시프트를 `srl`로 고르는 것이 그 역할을 한다.

---

## 3. 코드

```assembly
# 컴퓨터 구조 HW4 Mips 실습
## 작성자: 고영훈
### 날짜: 2025/11/30

.data
prompt:  .asciiz "Enter an int\n"
result:  .asciiz "The result is "
newline: .asciiz "\n"

.text
.globl main

main:
    # printf("Enter an int\n");
    li $v0, 4
    la $a0, prompt
    syscall

    # scanf("%d", &n);
    li $v0, 5        # read int
    syscall
    move $t0, $v0    # n = $t0
    
    # unsigned_n = (unsigned) n;
    move $t1, $t0    # unsigned_n = t1  (정수 그대로 사용)
    
    # ones_count = 0;
    li $t2, 0        # ones_count = t2 = 0

# while (unsigned_n != 0)
WHILE:
    beq $t1, $zero, END_WHILE    # unsigned_n == 0 → 종료

    # if (unsigned_n & 1) ones_count++;
    andi $t3, $t1, 1             # t3 = unsigned_n & 1
    beq  $t3, $zero, SKIP_INC    # LSB == 0 이면 증가 스킵
    addi $t2, $t2, 1             # ones_count++

SKIP_INC:
    # unsigned_n = unsigned_n >> 1;
    srl $t1, $t1, 1              # 논리 쉬프트

    j WHILE                      # 반복

END_WHILE:

    # printf("The results is %d\n", ones_count);
    li $v0, 4
    la $a0, result
    syscall

    li $v0, 1
    move $a0, $t2
    syscall

    li $v0, 4
    la $a0, newline
    syscall

    # return 0;
    li $v0, 10
    syscall

# End.
```

### C와 MIPS 대응

| C | MIPS |
| --- | --- |
| `while (unsigned_n != 0)` | `WHILE: beq $t1, $zero, END_WHILE` … `j WHILE` |
| `unsigned_n & 1` | `andi $t3, $t1, 1` |
| `if (...) ones_count++` | `beq $t3, $zero, SKIP_INC` / `addi $t2, $t2, 1` |
| `unsigned_n >> 1` | `srl $t1, $t1, 1` |

---

## 4. 실행 결과: C와 MIPS 비교

C 코드는 gcc로, MIPS 코드는 SPIM으로 실행했다.

| 입력 n | 32비트 표현 | C 출력 | MIPS 출력 |
| --- | --- | --- | --- |
| 5 | 0…0101 | 2 | 2 |
| 7 | 0…0111 | 3 | 3 |
| 0 | 0…0000 | 0 | 0 |
| 255 | 0…1111 1111 | 8 | 8 |
| −1 | 1111 … 1111 | 32 | 32 |
| −8 | 1111 … 1000 | 29 | 29 |
| 2147483647 | 0111 … 1111 | 31 | 31 |
| −2147483648 | 1000 … 0000 | 1 | 1 |

```text
Enter an int
5
The result is 2
```

- 음수는 **2의 보수**로 저장된다. −1은 모든 비트가 1이라 32, −8은 하위 3비트만 0이라 29
- `srl` 대신 `sra`를 쓰면 −1에서 끝나지 않는다. 직접 바꿔 실행해 보면 SPIM이 멈추지 않는다

> **주의**: 과제는 출력 문장이 C와 **같아야** 한다고 했는데, 제출 코드의 문자열은 `"The result is "`이고 C는 `"The results is %d\n"`이다. 값은 모두 맞지만 문장이 한 글자 다르므로 `result:  .asciiz "The results is "`로 고쳐야 요구사항과 정확히 같다.
{: .prompt-danger }

---

## 5. 더 빠른 방법

반복 횟수가 비트 길이(최대 32번)에 비례한다. `n & (n - 1)`은 **가장 오른쪽의 1 하나를 지우는** 연산이라, 1의 개수만큼만 반복하면 된다.

```c
while (u != 0) {
    u = u & (u - 1);
    ones_count++;
}
```

- 255(1이 8개)는 8번, 256(1이 1개)은 1번에 끝난다
- 현대 CPU에는 이 계산을 한 번에 하는 `popcnt` 명령어가 있다

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 과제 코드 `result` 문자열 | `"The result is "` | `"The results is "` | 과제 조건: 출력 문장을 C 코드와 같게 |

---

## 최종 핵심 정리

1. 이 C 코드는 정수의 2진수 표현에서 1의 개수를 센다 (5 → 2, 7 → 3, 255 → 8)
2. `andi reg, reg, 1`은 최하위 비트만 남기므로 홀짝 검사, 비트 검사에 쓴다
3. `srl`은 왼쪽을 0으로, `sra`는 부호 비트로 채운다. `unsigned >>`는 `srl`에 해당한다
4. MIPS 레지스터에는 부호 정보가 없고, 어떤 명령어로 다루느냐가 부호 해석을 정한다
5. 음수는 2의 보수라 −1 → 32, −8 → 29처럼 1이 많이 나온다
6. 산술 시프트를 쓰면 음수 입력에서 무한 루프가 생기므로 `(unsigned)` 변환이 필요하다
7. C(gcc)와 MIPS(SPIM)를 같은 8개 입력으로 실행해 결과가 모두 같았다
8. 출력 문장은 요구사항과 글자까지 같아야 하고, `n & (n - 1)`을 쓰면 반복 횟수를 1의 개수로 줄일 수 있다

---

End.
