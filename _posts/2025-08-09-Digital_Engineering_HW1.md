---
title: 01. Digital_Engineering_HW1
description: "디지털공학 과제 1 (C): 샘플링한 아날로그 값을 8레벨로 양자화하고 2진 코드로 변환하는 프로그램."
Author: YoungHoon Ko
date: 2025-08-09 09:55:32 +0900
categories: [University(2025-First-Semester), Digital_Engineering]
tags: [c, c_programming]
image: /assets/img/lang/C.png
---

# Digital_Engineering_HW1

## 문제

```markdown
1. Program in C language to print as follows:
---
Type the values of 5 sampled points: 0 1.2 1.5 2.3 2.5
Digitized signal after quantization and coding is 100 100 101 101 110
---

2. Cosider followings
- The analog input voltage level is from - 5V to 5V
- The quantization level is 8.
- -5V is mapped to Quantization level 0 and 0V is mapped to Quantization level 4.
```

---

## 정답

```c
#define _CRT_SECUR_NO_WARNINGS
#include <stdio.h>

// 양자화 함수
int q_lvls_f(double x) {
    int q = (x - (-5.0)) / 1.25;
    if (q > 7) {
        q = 7;
    }
    return q;
}

// 양자화 레벨 부여 함수
void d_to_b(int lvl, int count_bit) {
    for (int i = count_bit - 1; i >= 0; i--) {
        if ((lvl >> i) & 1) { // 비트 연산자
            printf("1");
        } else {
            printf("0");
        }
    }
    printf(" ");
}


int main(){
    double s_pnts[5];
    int q_lvls[5];

    printf("Type the values of 5 sampled points: ");
    for (int i = 0; i < 5; i++) {
        scanf("%lf", &s_pnts[i]);
        q_lvls[i] = q_lvls_f(s_pnts[i]);
    }

    printf("Digitized signal after quantization and coding is ");
    for (int i = 0; i < 5; i++) {
        d_to_b(q_lvls[i], 3);
    }
    printf("\n");
    
    return 0;
}    
```

---

## 결과

```markdown
Type the values of 5 sampled points: 0 1.2 1.5 2.3 2.5
Digitized signal after quantization and coding is 100 100 101 101 110
```

---

End.
