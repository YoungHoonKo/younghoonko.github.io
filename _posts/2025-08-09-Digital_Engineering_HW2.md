---
title: 02. Digital_Engineering_HW2
Author: YoungHoon Ko
date: 2025-08-10 08:25:32 +0900
categories: [University(2025-First-Semester), Digital_Engineering]
tags: [c, c_programming]
image: /assets/img/lang/C.png
---

[toc]

# Digital_Engineering_HW2

## 문제

```markdown
1. Program in C language to print as follows:
---
What is the level of Quantization? 16
Type the values of 5 sampled points: 0 -1.2 1.5 -4.4 2.5
The level of the input samples are 8 6 10 0 12
Digitized signal after quantization and coding is 1000 0110 1010 0000 1100
Type a binary number for float point number: -1001010.11101
The converted floating number is 11000010100101011101000000000000
---

2. Cosider followings
- The analog input voltage level is from - 5V to 5V
- With 16 level, -5V is mapped to Quantization level 0 and 0V is mapped to Quantization level 8.
- Map 5V to the last quantization level.
- Use Single Precision floating point number
```

---

## 정답

```c
// Homework #2 - 디지털 신호 양자화와 부동소주점 표현

#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <string.h>
#include <stdint.h>
#include <math.h>

// 필요한 비트 수 계산
int count_bit(int level) {
    return (int)ceil(log2(level)); // 밑이 2인 로그를 활용하여 2^n에서 n을 찾아냄
}

// 양자화 함수
int quantization_lvl(int x, double y) {
    double min = -5.0;
    double max = 5.0;
    double space = (max - min) / x;
    int quantization = (y - min) / space;

    if (quantization >= x) {
        quantization = x - 1;
    }

    return quantization;
}

// 양자화된 값을 지정된 비트 수로 출력
void print_bits(int value, int bit_count) {
    for (int i = bit_count - 1; i >= 0; i--) {
        if ((value >> i) & 1) { // ex) 1 = 0001(2) => (0001 >> 3) = 0(0000), (0001 >> 2) = 0(0000), (0001 >> 1) = 0(0000), (0001 >> 1) = 1(0001)
            printf("1");
        } else {
            printf("0");
        };
    }
    printf(" ");
}

// S-E-F
void print_float_bits(float x) {
    // 공용체 선언 - 과제하면서 알게된 기능
    union {
        float f;
        uint32_t i; // unint(부호없는) == unsigned int, 32(32비트), t(type의 약어) : 부호없는 32비트 정수형 => Use Single Precision floating point number
    } u;

    u.f = x;

    for (int i = 31; i >= 0; i--) {
        if ((u.i >> i) & 1) { // 실수값을 부호없는 정수형으로 접근하여 오른쪽으로 i 만큼 옮기는 비트연산자. 0인지 또는 1을 출력
            printf("1");
        } else {
            printf("0");
        }
    }
    printf("\n");
}

// 2진수 -> 10진수
double binary_to_double(char * x) {
    int negative = 0;
    if (x[0] == '-') {
        negative = 1;
        x++;
    }

    char * ptr = strchr(x, '.'); 
    double rst = 0;

    if (ptr) *ptr = '\0';
    for (int i = 0; x[i]; i++) {
        if (x[i] == '1') {
            rst += pow(2, strlen(x) - 1 - i);
        }
    }

    if (ptr) {
        ptr++;
        for (int i = 0; ptr[i]; i++) {
            if (ptr[i] == '1') {
                rst += pow(2, -(i+1));
            }
        }
    }

    if (negative) {
        return -rst;
    } else {
        return rst;
    }
}

// 메인 함수
int main() {
    double sampled_points[5];
    int quantization_lvl_list[5];
    int qtz_lvl = 0;

    // What is the level of Quantization? - 입력
    printf("What is the level of Quantization? ");
    scanf("%d", &qtz_lvl);
    if (qtz_lvl <= 1) {
        printf("양자화 레벨은 2 이상이어야 합니다.\n");
        return 1;
    }

    // Type the values of 5 sampled points: - 출력
    printf("Type the values of 5 sampled points: ");
    for (int i = 0; i < 5; i++) {
        scanf("%lf", &sampled_points[i]);
    }

    // The level of the input samples are - 출력
    printf("The level of the input samples are ");
    for (int i = 0; i < 5; i++) {
        int q = quantization_lvl(qtz_lvl, sampled_points[i]);
        printf("%d ", q);
        quantization_lvl_list[i] = q;
    }
    printf("\n");

    // Digitized signal after quantization and coding is - 출력
    int bit_count = count_bit(qtz_lvl);
    printf("Digitized signal after quantization and coding is ");
    for (int i = 0; i < 5; i++) {
        print_bits(quantization_lvl_list[i], bit_count);
    }
    printf("\n");

    // Type a binary number for float point number - 입력
    char binary_num[100];
    printf("Type a binary number for float point number: ");
    scanf("%s", binary_num);

    // The converted floating number is - 출력
    float a = (float)binary_to_double(binary_num);
    printf("The converted floating number is ");
    print_float_bits(a);

    return 0;
}
```

---

## 결과

```markdown
What is the level of Quantization? 16
Type the values of 5 sampled points: 0 -1.2 1.5 -4.4 2.5
The level of the input samples are 8 6 10 0 12
Digitized signal after quantization and coding is 1000 0110 1010 0000 1100
Type a binary number for float point number: -1001010.11101
The converted floating number is 11000010100101011101000000000000
```

---

End.
