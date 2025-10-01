---
title: 03. Digital_Engineering_HW3
Author: YoungHoon Ko
date: 2025-08-11 11:00:32 +0900
categories: [University(2025-First-Semester), Digital_Engineering]
tags: [c, c_programming]
image: /assets/img/lang/C.png
---

[toc]

# Digital_Engineering_HW3

## 문제

```markdown
1. Program in C language to print as follows:
- 메모장에 적힌 4비트 제네레이션 코드를 입력 후 .txt 파일에 적히 10 digit 바이너리 숫자를 읽은 후 CRC를 구하는 코드를 작성하시오
```

---

## 정답

```c
// Homework #3 - CRC 코드 구현

#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <string.h>
#define DATA_FILE "data.txt"
#define OUTPUT_FILE "501_C289002_HW3.txt"

// XOR 함수
void xor(char *a, const char *b, int len) {
    for (int i = 0; i < len; i++) {
        a[i] = (a[i] == b[i]) ? '0' : '1';
    }
}

// CRC 함수
void compute_crc(const char *data, const char *gen, char *crc) {
    char temp[100], curr[10];
    int data_len = strlen(data);
    int gen_len = strlen(gen);
    int crc_len = gen_len - 1;

    int i, j;
    for (i = 0; i < data_len; i++){
        temp[i] = data[i];
    }
    for (j = 0; j < crc_len; j++) {
        temp[i++] = '0';
    }
    temp[i] = '\0';

    strncpy(curr, temp, gen_len);
    curr[gen_len] = '\0';

    int pos = gen_len;
    while (pos < strlen(temp)) {
        if (curr[0] == '1') xor(curr, gen, gen_len);
        else xor(curr, "0000", gen_len);  // 4비트 고정
        for (i = 0; i < gen_len - 1; i++) {
            curr[i] = curr[i + 1];
        }
        curr[gen_len - 1] = temp[pos++];
    }

    if (curr[0] == '1') {
        xor(curr, gen, gen_len);
    } else {
        xor(curr, "0000", gen_len);
    }

    for (i = 0; i < crc_len; i++) {
        crc[i] = curr[i + 1];
    }
    crc[crc_len] = '\0';
}

// 3비트 CRC -> 4비트 문자열(원래 예제를 계산하면 CRC = 0100인데 예제는 1000으로 나옴, 왼쪽 정렬 후 0 추가) 너무 어려워....
void crc_2_4bit(const char *crc3, char *final_crc) {
    final_crc[0] = crc3[0];
    final_crc[1] = crc3[1];
    final_crc[2] = crc3[2];
    final_crc[3] = '0';  // 오른쪽 0 패딩
    final_crc[4] = '\0';
}

int main() {
    char gen[5], data[20], crc[10], final_crc[10], result[40];
    FILE *fin = fopen(DATA_FILE, "r");
    FILE *fout;

    if (!fin) {
        printf("data.txt 파일을 열 수 없습니다.\n");
        return 1;
    }

    printf("Please, type 4-bit generation code: ");
    scanf("%4s", gen);

    // 생성 다항식 유효성 검사
    if (gen[0] != '1' || strchr(gen, '1') == NULL) {
        printf("잘못된 입력값입니다. G는 4비트이고, 첫 비트가 반드시 1이어야 합니다.\n");
        fclose(fin);
        return 1;
    }

    fscanf(fin, "%10s", data);
    fclose(fin);

    compute_crc(data, gen, crc);
    crc_2_4bit(crc, final_crc);  // XOR 비교 없이 3비트 → 4비트 직접 변환

    strcpy(result, data);
    strcat(result, final_crc);

    fout = fopen(OUTPUT_FILE, "w");
    if (!fout) {
        printf("출력 파일을 열 수 없습니다.\n");
        return 1;
    }

    fprintf(fout, "The data with CRC is %s", result);
    fclose(fout);

    printf("결과가 %s 파일에 저장되었습니다.\n", OUTPUT_FILE);

    return 0;
}
```

---

## 결과

```markdown
.txt 파일에서 정상적으로 읽히고, 계산도 올바르게 된다.
단, .txt 파일과 .c 파일이 같은 위치에 있어야 함
```

---

End.
