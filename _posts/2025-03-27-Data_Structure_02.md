---
title: 02. Data Structure_Pointer, Array, Structure and Matrix
description: "자료구조 3~4주차: 포인터 연산자 *와 &, base type과 포인터 연산, 배열과 포인터의 관계, 문자열 역순 복사, 다중 간접 지시, 구조체와 자기 참조 구조체, 성적 처리 프로그램의 오류 찾기, 배열 덧셈·곱셈과 행렬곱 실습"
Author: YoungHoon Ko
date: 2025-03-27 18:00:00 +0900
categories: [University(2025-First-Semester), Data Structure]
tags: [c, data-structure, pointer]
math: true
image: /assets/img/lang/C.png
---

## 학습 목표

- 포인터의 정의와 두 연산자 `*`(간접 참조), `&`(주소)
- 포인터의 base type이 중요한 이유와 포인터 연산(`p++`)
- 배열 이름과 포인터의 관계, 포인터로 문자열 다루기
- 포인터의 포인터(다중 간접 지시)
- 구조체, 구조체 포인터(`->`), 자기 참조 구조체(연결 리스트의 노드)
- 배열 덧셈·곱셈 4가지 경우와 5×4 · 4×3 행렬곱

---

## 1. 포인터의 기본

**포인터 = 다른 변수(object)의 주소를 저장하는 변수.** 변수 p가 q의 주소를 갖고 있으면 "p가 q를 가리킨다"고 한다.

```c
type *var_name;   /* type: 포인터의 base type (가리키는 대상의 자료형) */
int *p;
```

| 연산자 | 이름 | 의미 |
| --- | --- | --- |
| `&q` | address of | q의 주소를 돌려줌 |
| `*p` | at address (간접 참조) | p가 가리키는 곳의 값을 돌려줌 |

```c
#include <stdio.h>
int main(void) {
    int *p, q;
    q = 199;           /* q에 199 저장 */
    p = &q;            /* p에 q의 주소 저장 */
    printf("%d", *p);  /* p가 가리키는 q의 값 */
    return 0;
}
```

실행 결과

```text
199
```

`p = &q; *p = 199;`처럼 포인터를 통해 값을 **써 넣을** 수도 있다. 이것이 **간접 지시(indirection)**이며, 연결 리스트와 이진 트리를 구현하는 바탕이다.

### 잘못된 사용

```c
int *p;
*p = 10;      /* 잘못: p가 어떤 변수도 가리키지 않음(쓰레기 주소) */
```

- 초기화하지 않은 포인터나 NULL 포인터에 `*`를 쓰면 정의되지 않은 동작(대부분 Segmentation fault)
- base type이 다른 주소를 넣으면 문제가 생긴다

```c
int q;
double *fp;
fp = &q;        /* int(4바이트) 주소를 double 포인터에 */
*fp = 100.23;   /* 8바이트를 써서 q 옆의 메모리까지 덮어씀 */
```

### base type이 중요한 이유

포인터 연산은 **base type의 크기 단위**로 움직인다.

```c
char *cp, ch;  int *ip, i;  double *dp, d;
cp = &ch;  ip = &i;  dp = &d;
cp++;  ip++;  dp++;    /* 각각 1, 4, 8바이트 이동 */
```

실행 결과 (64비트 Linux, gcc)

```text
cp+1 - cp = 1 byte, ip+1 - ip = 4 byte, dp+1 - dp = 8 byte
```

> **(중요!!)** `p + 1`은 주소에 1을 더하는 것이 아니라 **`sizeof(*p)`만큼** 더한다. 그래서 `*(p + i)`가 `p[i]`와 같다.
{: .prompt-warning }

---

## 2. 배열과 포인터

배열 이름은 **첫 원소의 주소**다. 그래서 `p = str;`처럼 바로 포인터에 넣을 수 있고, `p[i]`, `*(p + i)`, `str[i]`, `*(str + i)`가 모두 같은 원소다.

```c
char str[80];
*(str + 3) = 'c';
char *p = str;
printf("%c", p[3]);   /* c */
```

- 단, 배열 이름은 **상수 주소**라서 `str++`는 불가능하고, 포인터 변수 `p++`만 가능하다

### 포인터로 문자열 대문자·소문자 변환

```c
p = str;
while (*p) {            /* '\0'을 만나면 종료 */
    *p = toupper(*p);
    p++;
}
```

### 문자열 역순 복사

```c
char str1[] = "Pointers";
char str2[9], *p1, *p2;
p1 = str1 + strlen(str1) - 1;   /* str1의 마지막 문자 */
p2 = str2;                      /* str2의 시작 */
while (p1 >= str1)
    *p2++ = *p1--;              /* *p2 = *p1; p2++; p1--; */
*p2 = '\0';
printf("%s %s", str1, str2);
```

실행 결과

```text
Pointers sretnioP
```

`*p2++ = *p1--`는 **값을 먼저 쓰고 나서** 포인터를 움직인다(후위 증감이 `*`보다 우선순위가 높지만, 증감 효과는 식이 끝난 뒤 반영).

### 포인터 배열과 다중 간접 지시

```c
int *pa[20];       /* 정수 포인터 20개짜리 배열 */
pa[8] = &myvar;    /* 9번째 칸에 myvar의 주소 */

char **mp, *p, ch;
p = &ch;           /* p는 ch를 가리킴 */
mp = &p;           /* mp는 p를 가리킴 (포인터의 포인터) */
**mp = 'A';        /* ch = 'A' */
```

실행해 보면 `ch`에 `A`가 들어간다. 연결 리스트에서 `head` 자체를 바꾸는 함수가 `node **head`를 받는 이유가 이것이다.

---

## 3. 구조체와 구조체 포인터

```c
struct date { int day; int month; int year; } diary = {8, 5, 2001}, *Date;
Date = &diary;
printf("%d, %d, %d", Date->day, Date->month, Date->year);   /* 8, 5, 2001 */
```

- 구조체 변수는 `.`로, 구조체 포인터는 `->`로 멤버에 접근한다. `Date->day`는 `(*Date).day`와 같다

### 자기 참조 구조체

자신과 같은 구조체형을 가리키는 포인터를 멤버로 가진 구조체. **연결 리스트의 노드**가 바로 이것이다.

```c
struct student {
    int year; char name[8]; int age;
    struct student *child;    /* 같은 구조체를 가리킴 */
};
s1.child = &s2;               /* s1 → s2 연결 */
```

### 구조체의 크기

강의 자료에서는 `struct partner { char name; int age; }`의 크기를 1 + 4 = 5바이트라고 설명했지만, 실제로 출력해 보면 다르다.

```text
sizeof(int) = 4, sizeof(struct partner) = 8
```

컴파일러가 `int`를 4의 배수 주소에 놓기 위해 `char` 뒤에 3바이트를 채우기 때문이다(**패딩, 정렬**). 그래서 구조체 크기는 항상 `sizeof`로 구해야 한다.

### 실습: 성적 처리 프로그램의 오류 찾기

> 프로그램을 수행해 보고 잘못된 부분을 찾으시오. (과목명, 학생 수, 가중치를 입력받아 연결 리스트로 학생 성적을 저장하고 총점을 출력)
{: .prompt-info }

| 원래 코드 | 문제 | 수정 |
| --- | --- | --- |
| `#include <stdlib.h>` 없음 | `malloc` 선언이 없음 | 헤더 추가 |
| `scanf("%s\n", name)` | 형식 끝의 `\n`은 공백이 아닌 문자가 올 때까지 입력을 기다림 | `scanf("%49s", name)` |
| `scanf("%d\n", &mid)` | `mid`는 `double`인데 `%d` | `%lf` |
| `scanf("%d", temp->midterm)` | 주소가 아니라 값을 넘김 | `&temp->midterm` |
| `scanf("%s", &(temp->homework))` | 정수에 문자열 형식 | `%d` |
| 마지막 노드의 `link` | 초기화하지 않음 | `temp->link = NULL` |
| `main()` | 반환형 없음 | `int main(void)` |

고친 코드의 핵심 부분

```c
for (int i = 0; i < number; i++) {
    temp = (students *)malloc(sizeof(students));
    temp->link = NULL;
    if (tail != NULL) tail->link = temp;   /* 뒤에 붙이기 */
    else Iclass = temp;                    /* 첫 노드 */
    tail = temp;
    scanf("%19s", temp->name);
    scanf("%d", &temp->midterm);
    scanf("%d", &temp->final);
    scanf("%d", &temp->homework);
    temp->total = temp->midterm * mid + temp->final * final + temp->homework * homework;
}
```

실행 결과 (가중치 0.3, 0.4, 0.3, 학생 3명)

```text
DataStructure
========================================================
 name           midterm     final  homework         total
hong              80        90       100         90.00
lee               70        60        90         72.00
kim               95        85        80         86.50
========================================================
```

---

## 4. 4주차 실습: 배열과 행렬 연산

> 배열 더하기 1·2, 배열 곱하기 1·2 (코드와 결과만), 행렬곱 연산 (문제 정의부터 테스트까지)
{: .prompt-info }

두 배열 `array_1 = {1, 2, 3, 4, 5}`, `array_2 = {6, 7, 8, 9, 10}`에 대해

| Case | 연산 | 결과 |
| --- | --- | --- |
| 1 | `a1[i] + a2[i]` | 7 9 11 13 15 |
| 2 | `a1[i] + a2[4-i]` (거꾸로 더하기) | 11 11 11 11 11 |
| 3 | `a1[i] * a2[i]` | 6 14 24 36 50 |
| 4 | `a1[i] * a2[4-i]` 후 결과를 뒤집기 | 30 28 24 18 10 |

Case_4의 뒤집기는 앞뒤를 바꾸는 반복을 `n/2`번만 하면 된다.

```c
for (int j = 0; j < 5 / 2; j++) {
    temp = rst[j];
    rst[j] = rst[4 - j];
    rst[4 - j] = temp;
}
```

### 행렬곱 (Case_5)

$$(5 \times 4)$$ 행렬 A와 $$(4 \times 3)$$ 행렬 B의 곱 C는 $$(5 \times 3)$$이고

$$C_{ij} = \sum_{k=0}^{3} A_{ik} B_{kj}$$

```c
#include <stdio.h>

int main(void) {
    int array_1[5][4] = { {1, 5, 3, 6}, {2, 4, 6, 2}, {6, 2, 4, 3}, {7, 1, 7, 5}, {8, 3, 5, 1} };
    int array_2[4][3] = { {7, 4, 4}, {6, 3, 2}, {4, 5, 4}, {1, 3, 2} };
    int rst[5][3] = {0};

    for (int i = 0; i < 5; i++)
        for (int j = 0; j < 3; j++)
            for (int k = 0; k < 4; k++)
                rst[i][j] += array_1[i][k] * array_2[k][j];

    for (int t = 0; t < 5; t++) {
        for (int h = 0; h < 3; h++)
            printf("%4d", rst[t][h]);
        printf("\n");
    }
    return 0;
}
```

실행 결과

```text
  55  52  38
  64  56  44
  73  59  50
  88  81  68
  95  69  60
```

손 검산: $$C_{00} = 1 \cdot 7 + 5 \cdot 6 + 3 \cdot 4 + 6 \cdot 1 = 7 + 30 + 12 + 6 = 55$$

- `rst`를 `{0}`으로 초기화하지 않으면 `+=`가 쓰레기 값에 더해진다
- 삼중 반복의 횟수는 $$5 \times 3 \times 4 = 60$$번 곱셈. 일반적으로 $$(m \times n)(n \times p)$$ 곱은 $$O(mnp)$$

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| Pointer-1 | `int q; // 정수 2bytes` | 현재 대부분의 환경에서 4바이트 | 16비트 시절 기준. `sizeof(int)`로 확인 |
| struc-pointer | `sizeof(partner)` = 1 + 4 = 5바이트 | 8바이트 | 정렬을 위한 패딩 (실행해서 확인) |
| pointer-2 | `printf("%p", p[i])`로 문자 출력 | `%x` 또는 `%d` | `%p`에는 포인터를 넘겨야 함. 문자 코드(0x50 = 'P')가 찍힌 것 |
| pointer-3 | `for (i=0; i<6; i++) temp[i]` | `i < 5` | 원소 5개짜리 배열의 범위 밖 접근 |
| pointer-2 | `gets(str)` | `fgets(str, sizeof str, stdin)` | `gets`는 버퍼 넘침 때문에 C11에서 삭제 |
| pointer-2 | `int main(viod)` | `int main(void)` | 오타 |
| 역순 복사 예제 | `while (p1 >= str1) *p2++ = *p1--;` | 동작은 하지만 마지막에 p1이 배열 앞을 가리킴 | 배열 시작 앞 주소를 만드는 것은 엄밀히 정의되지 않은 동작. 인덱스로 쓰는 편이 안전 |

---

## 최종 핵심 정리

1. 포인터는 주소를 저장하는 변수이고, `&`는 주소를, `*`는 그 주소의 값을 돌려준다
2. 초기화하지 않은 포인터나 NULL 포인터를 역참조하면 안 된다
3. 포인터 연산은 base type 크기 단위로 움직이므로 `*(p + i)`는 `p[i]`와 같다
4. 배열 이름은 첫 원소의 상수 주소라서 `str++`는 안 되고 포인터 변수만 증가시킬 수 있다
5. 포인터의 포인터(`**`)로 다른 함수의 포인터 변수 자체를 바꿀 수 있다
6. 구조체 포인터는 `->`로 멤버에 접근하고, 자기 참조 구조체가 연결 리스트의 노드가 된다
7. 구조체 크기는 패딩 때문에 멤버 크기의 합보다 클 수 있으니 `sizeof`로 구한다
8. 행렬곱은 $$C_{ij} = \sum_k A_{ik}B_{kj}$$를 삼중 반복으로 계산하며 결과 배열은 0으로 초기화한다

---

End.
