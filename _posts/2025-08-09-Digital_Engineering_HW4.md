---
title: 04. Digital_Engineering_HW4
description: "디지털공학 과제 4 (C): 두 5비트 이진수를 입력받아 컨볼루션(다항식 곱셈) 결과를 출력하는 프로그램."
Author: YoungHoon Ko
date: 2025-08-15 14:53:32 +0900
categories: [University(2025-First-Semester), Digital_Engineering]
tags: [c, c_programming]
image: /assets/img/lang/C.png
---

# Digital_Engineering_HW4

## 문제

```markdown
1. Program in C language to print as follows:
- When your program is executed,
- it asks a user to input two 5 digit bynary number.
```Please, type the 1st 5-bit Binary number: 10101
```Please, type the 2nd 5-bit Binary number: 10101
- Then do the following process.

The results is : 0 1 0 2 0 3 0 2 0 1 0
```

---

## 정답

```c
// Homework #4 - 두 개의 바이너리 신호에 대한 컨볼루션(Convolution) 을 수행하는 C 프로그램

#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define SIZE_ORIG 5               // 원래 이진 입력의 비트 수 (문제에서 5비트라고 명시됨)
#define SIZE_CAL 6                // 계산용 확장 비트 수 (입력 앞 또는 뒤에 0을 붙여 6비트로 확장)
#define RESULT_SIZE (2 * SIZE_CAL - 1)  // 컨볼루션 결과는 항상 입력+필터-1 크기, 즉 11

int main() {
    char bin1[SIZE_ORIG + 1], bin2[SIZE_ORIG + 1]; // +1은 null 문자 포함을 위한 공간
    int result[RESULT_SIZE] = {0};                 // 컨볼루션 결과 저장용 배열, 전부 0으로 초기화
    unsigned int a = 0, b = 0;                     // 입력된 이진 문자열을 정수로 바꿔서 저장

    // 입력 받기
    printf("Please, type the 1st 5-bit Binary number: ");
    scanf("%5s", bin1);
    printf("Please, type the 2nd 5-bit Binary number: ");
    scanf("%5s", bin2);

    // 첫 번째 입력 변환: 앞에 0을 붙여야 하므로, 입력을 오른쪽으로 shift
    // 예: 입력 "10101" → 0b10101 → shift-left 1 → 0b101010 (앞에 0 붙인 효과)
    for (int i = 0; i < SIZE_ORIG; i++) {
        if (bin1[i] == '1') {
            a |= (1 << (SIZE_ORIG - i - 1));  // 왼쪽 비트부터 채우기 (직접 계산해 봄)
        }
    }
    a = a << 1; // 실제로 앞에 0을 붙이는 건 왼쪽 시프트 1번이면 됨

    // 두 번째 입력 변환: 뒤에 0을 붙이는 건 특별한 처리 없이 바로 정렬하면 됨
    for (int i = 0; i < SIZE_ORIG; i++) {
        if (bin2[i] == '1') {
            b |= (1 << (SIZE_CAL - i - 2));  // SIZE_CAL=6이므로 가장 왼쪽부터 오른쪽으로 정렬
        }
    }

    // 본격적인 컨볼루션 계산
    // CNN에서 필터가 입력 위를 슬라이딩하며 계산하는 것과 동일한 구조
    for (int i = 0; i < SIZE_CAL; i++) {
        int bit_a = (a >> (SIZE_CAL - 1 - i)) & 1;  // a의 i번째 비트 추출 (왼쪽부터)
        for (int j = 0; j < SIZE_CAL; j++) {
            int bit_b = (b >> (SIZE_CAL - 1 - j)) & 1;  // b의 j번째 비트 추출
            result[i + j] += bit_a & bit_b;  // 비트 곱셈 대신 AND 연산 사용
        }
    }

    // 결과 출력
    printf("The results is : ");
    for (int i = 0; i < RESULT_SIZE; i++) {
        printf("%d", result[i]);
        if (i < RESULT_SIZE - 1) printf(" ");
    }
    printf("\n");

    return 0;
}
```

---

## 결과

```markdown
The results is : 0 1 0 2 0 3 0 2 0 1 0
```

---

End.
