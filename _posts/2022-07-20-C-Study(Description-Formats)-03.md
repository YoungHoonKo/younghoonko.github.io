---
title: 03. C Language(Description Formats)
Author: YoungHoon Ko
date: 2022-07-20 16:45:00 +0900
categories: [C, C-Study]
tags: [c]
image: /assets/img/lang/C.png
---

[toc]

# C Language Description Formats (C 언어의 서술 형식)

이번 학습에서는 C 언어에서 사용되는 다양한 서술문(Statement)의 형태와 각각의 역할에 대해 학습하였다. 단일 서술문, 복합 서술문, 조건문, 반복문, 분기문, 주석 등은 모두 프로그램의 논리 흐름을 구성하는 중요한 요소이다.

---

## 1. 단일 서술문 (Single Statement)

- **논리적인 최소 명령 단위**이며, `;` 세미콜론으로 끝난다.
- 한 줄에 여러 개를 나열할 수도 있고, 여러 줄에 걸쳐 한 문장을 작성할 수도 있다.

~~~c
a = a + 30;
a = a
  + 30;
a = a + 30; b = b + 50;
~~~

---

## 2. 복합 서술문 (Compound Statement)

- 여러 개의 단일 서술문을 `{}` 중괄호로 감싼 문장
- 단일문을 하나의 블록으로 묶어주는 역할
- 복합문 끝에 `;`를 붙여도 오류는 없지만 비권장됨

~~~c
{
  a = a + 30;
  b = b + 50;
}
~~~

---

## 3. 역할에 따른 서술문 (Description Formats by Role)

### 📌 수식 서술문 (Expression Statement)

- 계산 또는 대입을 위한 서술문

~~~c
average = (a + b) / 2;
~~~

---

### 📌 조건 서술문 (Selection Statement)

- 조건을 만족할 경우 특정 명령을 실행

~~~c
if (a > 5) a = 0;
~~~

---

### 📌 반복 서술문 (Iteration Statement)

- 특정 명령을 **반복 실행**할 수 있는 구조

~~~c
int sum = 0, i;
for (i = 0; i < 5; i++) sum = sum + i;
~~~

---

### 📌 라벨 서술문 (Labeled Statement)

- 특정 위치에 이름을 붙여 `goto`로 점프할 수 있도록 한다

~~~c
if (a < 0) goto EXIT;
a = a - 1;
EXIT:
~~~

> ⚠️ 구조화 프로그래밍을 해치므로 가능한 사용하지 않는 것이 좋다.

---

### 📌 분기 서술문 (Jump Statement)

- `break`, `continue`, `return` 등 흐름 제어 용도

~~~c
while (1) {
  if (i > 5) break;
  i++;
}
~~~

---

### 📌 주석문 (Comment)

- 프로그램의 설명, 기록 등을 위해 사용되며 `/* */` 사이에 작성함

~~~c
/* 이 문장은 주석이므로 실행에 영향을 미치지 않는다 */
~~~

#### ❗ 주의사항

- 주석은 **중첩 사용이 불가능**하다
- 아래 예시는 오류를 유발함

~~~c
/* 주석문은 /* 중첩하면 */ 오류 발생 */
~~~

---

### 📌 예약어 (Reserved Words)

- 프로그래밍 언어에서 **기능이 고정된 단어**로, 변수나 함수 이름 등으로 사용할 수 없다

예: `int`, `for`, `return`, `while`, `if`, ...

---

## Quiz

다음 코드에서 누락된 문법 요소를 찾아 보완하시오.

~~~c
/* 다음 코드는 중간에 빠진 문법이 있다. 찾아서 매꾸시오 */
#include <stdio.h>

void main(void) {
  // a 와 b의 값을 더해서 sum에 저장
  int a = 1. b = 2, sum;
  sum = a + b

~~~

정답:  
1. `/*` ← 주석 시작  
2. `;` ← 각 문장 구분  
3. `}` ← 함수 닫기 중괄호

---

> 📌 정리  
C 언어의 서술 형식은 단순한 문법을 넘어서 프로그램 구조화의 핵심이 된다. 각 역할에 맞는 서술문을 올바르게 사용하면 가독성과 유지 보수성이 크게 향상된다.

---

End
