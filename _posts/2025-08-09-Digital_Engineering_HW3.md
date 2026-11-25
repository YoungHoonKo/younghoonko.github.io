---
title: 03. Digital_Engineering_HW3 CRC Generation
description: "디지털공학 과제 3 (C): 4비트 생성 코드와 data.txt의 10비트 데이터로 CRC를 만들어 파일에 저장하는 프로그램. modulo-2 나눗셈 손 계산, 과제 예시(1000)와 표준 CRC(100)의 관계, 수신 측 검증, 하드코딩된 \"0000\"의 일반화"
Author: YoungHoon Ko
date: 2025-08-11 11:00:32 +0900
categories: [University(2025-First-Semester), Digital_Engineering]
tags: [c, digital]
math: true
image: /assets/img/lang/C.png
---

## 학습 목표

- 오류 검출 코드: 패리티와 CRC의 차이
- modulo-2 연산(XOR)으로 하는 나눗셈
- 송신 측 CRC 생성과 수신 측 검증 절차
- 파일 입출력(`fopen`, `fscanf`, `fprintf`)
- 과제 예시 출력과 표준 CRC 결과가 다른 이유

---

## 1. 과제

> - 실행하면 4비트 생성 코드를 입력받는다: `Please, type 4-bit generation code: 1001`
> - `data.txt`에서 10비트 2진수를 읽는다 (예: `1010101010`)
> - 생성 코드로 데이터의 CRC를 만들고, CRC를 붙인 데이터를 출력 파일에 쓴다
> - 예시 출력 파일 내용: `The data with CRC is 10101010101000`
> - 제출한 C 파일을 그대로 실행해서 동작하지 않으면 0점
{: .prompt-info }

---

## 2. CRC 원리

**패리티**는 1비트 오류만 검출하고 위치도 모른다. **CRC(Cyclic Redundancy Check)**는 데이터 블록 뒤에 체크섬을 붙여 여러 비트 오류를 검출한다.

### modulo-2 연산

자리올림·자리내림이 없는 2진 연산으로, 덧셈과 뺄셈이 모두 XOR이다.

| A | B | A ⊕ B |
| --- | --- | --- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

### 송신 측

1. 생성 코드 G(4비트, 최고차 3차)에 맞춰 데이터 뒤에 0을 3개 붙인다
2. modulo-2로 G로 나눈다
3. 나머지 3비트가 CRC. 붙였던 0 자리에 나머지를 넣어 보낸다

### 수신 측

받은 비트열 전체를 같은 G로 나눠 **나머지가 0이면 오류 없음**, 아니면 오류.

### 손 계산: 데이터 1010101010, G = 1001

`1010101010` + `000`을 1001로 나눈다. 맨 앞 비트가 1인 위치마다 G를 XOR한다.

```text
위치  0: 1010 xor 1001 -> 0011
위치  2: 1110 xor 1001 -> 0111
위치  3: 1111 xor 1001 -> 0110
위치  4: 1100 xor 1001 -> 0101
위치  5: 1011 xor 1001 -> 0010
위치  7: 1000 xor 1001 -> 0001
나머지(마지막 3비트): 100
```

표준 CRC는 **100**이고 송신 데이터는 `1010101010 100`이다.

### 과제 예시가 1000인 이유

과제 예시는 `1010101010` 뒤에 **4비트 `1000`**을 붙였다. 이것은 3비트 CRC `100` 뒤에 0을 하나 더 붙인 것과 같다. 다항식으로 보면 표준 코드워드에 $$x$$를 곱한 것이라, 여전히 G로 나누어떨어진다.

| 비트열 | 1001로 나눈 나머지 |
| --- | --- |
| `1010101010` `100` (표준 CRC) | 000 |
| `1010101010` `1000` (과제 예시) | 000 |
| `1010101010` `0001` (강의 슬라이드처럼 0을 4개 붙여 계산) | 000 |

강의 슬라이드는 "생성 코드 비트 수만큼 0을 붙인다"고 해서 4개를 붙이면 나머지가 `001`(4비트로 `0001`)이 된다. 세 방식 모두 수신 측 검사를 통과하지만 **과제 예시와 같은 출력은 "3비트 CRC + 0"**뿐이라, 제출 코드도 이 형태에 맞췄다. 위 나머지는 Python으로 modulo-2 나눗셈을 따로 구현해 확인했다.

---

## 3. 코드

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <string.h>
#define DATA_FILE "data.txt"
#define OUTPUT_FILE "HW3_output.txt"

/* a = a XOR b (문자열 '0'/'1') */
void xor_bits(char *a, const char *b, int len) {
    for (int i = 0; i < len; i++)
        a[i] = (a[i] == b[i]) ? '0' : '1';
}

/* data를 gen으로 modulo-2 나눈 나머지(gen 길이 - 1 비트)를 crc에 */
void compute_crc(const char *data, const char *gen, char *crc) {
    char temp[100] = {0}, curr[10] = {0}, zeros[10] = {0};
    int data_len = (int)strlen(data), gen_len = (int)strlen(gen), crc_len = gen_len - 1;

    memset(zeros, '0', gen_len);                       /* 맨 앞이 0일 때 XOR할 0000 */
    strcpy(temp, data);
    memset(temp + data_len, '0', crc_len);             /* 데이터 뒤에 0을 crc_len개 */
    temp[data_len + crc_len] = '\0';

    strncpy(curr, temp, gen_len);
    int pos = gen_len, total = (int)strlen(temp);
    while (1) {
        xor_bits(curr, curr[0] == '1' ? gen : zeros, gen_len);
        if (pos >= total) break;
        memmove(curr, curr + 1, gen_len - 1);          /* 한 칸 왼쪽으로 */
        curr[gen_len - 1] = temp[pos++];               /* 다음 비트 내려받기 */
    }
    strncpy(crc, curr + 1, crc_len);
    crc[crc_len] = '\0';
}

int main(void) {
    char gen[5] = {0}, data[20] = {0}, crc[10] = {0}, result[40] = {0};
    FILE *fin = fopen(DATA_FILE, "r");
    if (!fin) { printf("data.txt 파일을 열 수 없습니다.\n"); return 1; }

    printf("Please, type 4-bit generation code: ");
    scanf("%4s", gen);
    if (strlen(gen) != 4 || gen[0] != '1' || strspn(gen, "01") != 4) {
        printf("생성 코드는 1로 시작하는 4비트 2진수여야 합니다.\n");
        fclose(fin);
        return 1;
    }
    fscanf(fin, "%10s", data);
    fclose(fin);

    compute_crc(data, gen, crc);                       /* 표준 3비트 CRC */
    strcpy(result, data);
    strcat(result, crc);
    strcat(result, "0");                               /* 과제 예시 형식: 4비트로 맞춤 */

    FILE *fout = fopen(OUTPUT_FILE, "w");
    if (!fout) { printf("출력 파일을 열 수 없습니다.\n"); return 1; }
    fprintf(fout, "The data with CRC is %s", result);
    fclose(fout);
    printf("결과가 %s 파일에 저장되었습니다.\n", OUTPUT_FILE);
    return 0;
}
```

제출한 코드와 달라진 점

| 제출 코드 | 수정 | 이유 |
| --- | --- | --- |
| 첫 비트가 0일 때 `xor(curr, "0000", gen_len)` | `zeros`를 gen 길이만큼 만듦 | 4비트에만 맞춰 고정됨 |
| `crc_2_4bit()`로 3비트 뒤에 0 추가 | `strcat(result, "0")` | 동작은 같고 이름이 의도를 잘못 전달 |
| `gen[0] != '1' \|\| strchr(gen, '1') == NULL` | 길이 4, 첫 비트 1, 0/1만 허용 | 뒤 조건은 앞 조건이 거짓이면 항상 거짓이라 의미 없음 |
| 출력 파일 이름에 학번 | `HW3_output.txt` | 블로그 공개용 |

---

## 4. 실행 결과

과제 예시 (`data.txt` = `1010101010`, G = `1001`)

```text
Please, type 4-bit generation code: 1001
결과가 HW3_output.txt 파일에 저장되었습니다.
```

```text
The data with CRC is 10101010101000
```

과제 예시와 같다. 제출 당시 `data.txt`(`1001001001`)로 실행하면

```text
The data with CRC is 10010010010000
```

$$1001001001_2$$는 $$(x^3+1)(x^6+1)$$이라 1001로 나누어떨어져 CRC가 `000`이다.

> 과제 폴더에 남아 있던 출력 파일은 `10010010011010`이었다. 현재 코드에 생성 코드 `1001`을 넣으면 나올 수 없는 값(나머지 `011`)이라, 다른 생성 코드로 시험한 결과가 남은 것으로 보인다. 같은 데이터를 `1011`로 나누면 나머지가 `101`이 되어 일치한다.
{: .prompt-danger }

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 제출 코드 주석 | "원래 예제를 계산하면 CRC = 0100인데 예제는 1000으로 나옴" | 표준 CRC는 `100`, 예시는 `100` 뒤에 0을 붙인 것 | `0100`은 4비트 작업 창에 남은 나머지로, 맨 앞 0을 빼면 `100`. 예시 `1000`과 다른 것은 0을 앞이 아니라 뒤에 붙였기 때문 (계산으로 확인) |
| Chapter 2 슬라이드 | 생성 코드 비트 수만큼 0을 붙임 | 표준 CRC는 생성 다항식 차수(비트 수 - 1)만큼 | 비트 수만큼 붙여도 수신 검사는 통과하지만 과제 예시와 다름 |
| 제출 코드 | `xor` 함수 이름 | `xor_bits` | `xor`는 C++의 대체 연산자 키워드라 C++ 컴파일러에서 오류 |

---

## 최종 핵심 정리

1. CRC는 데이터 뒤에 체크섬을 붙여 여러 비트 오류를 검출한다
2. modulo-2 연산은 자리올림이 없고 덧셈·뺄셈이 모두 XOR이다
3. 송신 측은 데이터 뒤에 0을 붙여 G로 나누고, 그 나머지를 CRC로 붙인다
4. 수신 측은 받은 비트열을 G로 나눠 나머지가 0이면 오류가 없다고 판단한다
5. 1010101010을 1001로 나눈 표준 CRC는 100이다
6. 과제 예시 1000은 표준 CRC 뒤에 0을 붙인 형태이며 여전히 G로 나누어떨어진다
7. 생성 코드 길이에 의존하는 값("0000")은 하드코딩하지 말고 길이로 만든다
8. 입력 검사는 길이, 첫 비트, 문자 종류를 모두 확인해야 의미가 있다

---

End.
