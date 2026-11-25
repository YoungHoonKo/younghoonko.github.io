---
title: 10. C-Programming_Strings and String Functions
description: "C 프로그래밍 7~12장 중 기존 글에 빠져 있던 문자열 정리: 널 문자와 char 배열, sizeof와 strlen의 차이, scanf로 문자열 입력, strlen 직접 구현, strcpy·strcat·strcmp, 배열과 문자열 리터럴 포인터의 차이, ==로 문자열을 비교하면 안 되는 이유, 버퍼 넘침"
Author: YoungHoon Ko
date: 2022-12-03 18:00:00 +0900
categories: [University(2022-Second-Semester), C-Programming]
tags: [c, c_programming, string]
image: /assets/img/lang/C.png
---

## 학습 목표

- C의 문자열이 널 문자(`'\0'`)로 끝나는 `char` 배열이라는 점
- `sizeof`와 `strlen`의 차이
- `scanf("%s")`로 문자열을 받을 때 `&`를 붙이지 않는 이유
- `strlen`을 직접 구현하며 포인터로 문자열 순회하기
- `strcpy`, `strcat`, `strcmp`의 동작과 주의점
- `char a[]`와 `char *p`의 차이, `==` 비교가 틀리는 이유

수업 교재 7~12장(배열, 함수, 포인터, 배열과 포인터, 문자열 함수, 구조체) 중 배열·함수·포인터·구조체는 기존 글 03~09에 정리되어 있다. 이 글은 빠져 있던 **문자열** 부분을 보완한다. 예제는 모두 gcc로 컴파일해 실행 결과를 확인했다.

---

## 1. 문자열 = 널 문자로 끝나는 char 배열

```c
char str[] = "Hello";
```

| 인덱스 | 0 | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- | --- |
| 값 | `'H'` | `'e'` | `'l'` | `'l'` | `'o'` | `'\0'` |

- 문자열 리터럴 끝에는 컴파일러가 `'\0'`을 자동으로 붙인다
- 그래서 5글자를 저장하려면 배열 크기가 **최소 6**이어야 한다
- `printf("%s")`, `strlen` 등 모든 문자열 함수는 `'\0'`을 만날 때까지 읽는다

> **(중요!!)** `sizeof(str)`은 배열 전체 크기(널 문자 포함, 6), `strlen(str)`은 널 문자 앞까지의 글자 수(5)다. 배열이 함수 인자로 넘어가면 포인터가 되므로 함수 안의 `sizeof`는 포인터 크기(보통 8)가 나온다.
{: .prompt-danger }

### 문자열 입력

```c
char name[100];
scanf("%s", name);   /* &name이 아니다 */
```

배열 이름 자체가 첫 원소의 주소이므로 `&`를 붙이지 않는다. `%s`는 공백에서 입력을 끊고, 입력이 배열보다 길면 넘쳐 버린다. 실무에서는 `fgets(name, sizeof(name), stdin)`처럼 크기를 제한하는 함수를 쓴다.

---

## 2. strlen 직접 구현

```c
int my_strlen(const char *s) {
    int n = 0;
    while (s[n] != '\0') {
        n++;
    }
    return n;
}
```

- 매개변수가 `const char *`인 이유: 함수가 문자열을 **바꾸지 않는다**는 약속
- `s[n]`은 `*(s + n)`과 같다 (기존 글 "07. Array & Pointer"의 배열-포인터 관계)
- 문자열 길이를 구하려면 끝까지 읽어야 하므로 시간 복잡도는 O(n). 반복문 조건에 `strlen`을 넣으면 매번 다시 세어 O(n²)이 된다

---

## 3. string.h 함수

| 함수 | 동작 | 주의 |
| --- | --- | --- |
| `strlen(s)` | 널 문자 앞까지 길이 | 반환형은 `size_t` (`%zu`로 출력) |
| `strcpy(dst, src)` | src를 dst에 복사 (널 문자 포함) | dst가 충분히 커야 함 |
| `strcat(dst, src)` | dst 끝에 src를 이어 붙임 | dst에 원래 내용 + src + 1칸 필요 |
| `strcmp(a, b)` | 사전순 비교 | 같으면 0, a가 앞이면 음수, 뒤면 양수 |

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int main(void) {
    char str[] = "Hello";
    printf("sizeof = %zu, strlen = %zu\n", sizeof(str), strlen(str));

    char dst[20];
    strcpy(dst, str);
    strcat(dst, ", C");
    printf("dst = %s (%zu)\n", dst, strlen(dst));

    printf("%d %d %d\n", strcmp("apple", "banana") < 0,
                         strcmp("C", "C") == 0,
                         strcmp("b", "a") > 0);

    char a[] = "abc";
    const char *p = "abc";
    printf("a == p: %d, strcmp(a, p): %d\n", a == p, strcmp(a, p));

    for (char *q = str; *q != '\0'; q++) {
        *q = (char)toupper((unsigned char)*q);
    }
    printf("%s\n", str);
    return 0;
}
```

```text
sizeof = 6, strlen = 5
dst = Hello, C (8)
1 1 1
a == p: 0, strcmp(a, p): 0
HELLO
```

> `strcmp`의 반환값은 "-1, 0, 1"이 아니라 **부호**만 정해져 있다. 구현에 따라 -1 대신 두 문자의 차이(예: -1, -25)가 나올 수 있으므로 `== -1`이 아니라 `< 0`으로 비교한다.
{: .prompt-warning }

---

## 4. char 배열과 문자열 포인터

| | `char a[] = "abc";` | `const char *p = "abc";` |
| --- | --- | --- |
| 저장 위치 | 스택의 배열 (리터럴을 **복사**) | 읽기 전용 영역의 리터럴을 **가리킴** |
| 내용 수정 | 가능 (`a[0] = 'A'`) | 불가 (수정하면 정의되지 않은 동작) |
| 다른 문자열 대입 | `a = "xyz"` 불가 (배열은 대입 불가, `strcpy` 사용) | `p = "xyz"` 가능 (가리키는 곳만 바뀜) |
| `sizeof` | 4 | 포인터 크기 |

### == 로 비교하면 안 되는 이유

`a == p`는 두 문자열의 **주소**를 비교한다. 내용이 같아도 저장 위치가 다르므로 위 실행 결과처럼 0(거짓)이 나온다. 내용 비교는 반드시 `strcmp(a, p) == 0`.

### 버퍼 넘침

`strcpy`, `strcat`, `scanf("%s")`는 대상 배열의 크기를 모른다. 넘치면 옆 메모리를 덮어써 프로그램이 죽거나 보안 취약점이 된다. Visual Studio가 `_CRT_SECURE_NO_WARNINGS` 없이는 이 함수들에 경고를 내는 이유이고, 크기를 받는 `snprintf`, `fgets`, `strncpy`(널 문자가 안 붙을 수 있어 직접 붙여야 함)를 쓰는 습관이 필요하다.

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 교재 7~12장 스캔 노트 | - | 정정 없음 | 교재 스캔본이라 내용을 옮기지 않고, 기존 글에 없던 문자열 주제만 직접 작성한 예제로 정리 |

---

## 최종 핵심 정리

1. C의 문자열은 '\0'으로 끝나는 char 배열이므로 배열 크기는 글자 수 + 1 이상이어야 한다
2. sizeof는 배열 전체 크기, strlen은 널 문자 앞까지의 길이다
3. scanf("%s")에는 배열 이름이 이미 주소라서 &를 붙이지 않는다
4. strlen은 끝까지 읽는 O(n) 연산이라 반복문 조건에 반복 호출하지 않는다
5. strcpy와 strcat은 대상 배열이 충분히 커야 하며 크기를 검사하지 않는다
6. strcmp는 같으면 0이고, 음수·양수의 부호로 순서를 판단한다
7. char 배열은 내용 수정이 가능하고, 문자열 리터럴 포인터는 수정하면 안 된다
8. ==는 주소를 비교하므로 문자열 내용은 strcmp로 비교한다

---

End.
