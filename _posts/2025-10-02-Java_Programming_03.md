---
title: 03. Java Programming_Loops, Arrays, Methods and HW3
description: "자바 프로그래밍 및 실습 3장과 과제 3: for·while·continue·break, 배열 선언과 생성(레퍼런스), 비정방형 2차원 배열, for-each, 배열을 메소드에 넘기면 원본이 바뀌는 이유, main의 원형, 예외와 try-catch-finally. 연습문제 답 중 틀린 두 개를 실행으로 정정"
Author: YoungHoon Ko
date: 2025-10-02 12:00:00 +0900
categories: [University(2025-Second-Semester), Java Programming]
tags: [java]
image: /assets/img/lang/java.png
---

## 학습 목표

- for, while, do-while과 continue, break
- 배열 선언과 생성이 따로인 이유(레퍼런스)
- 2차원 배열과 비정방형 배열, `length`
- for-each 문
- 배열·객체를 메소드에 넘길 때와 기본 타입을 넘길 때의 차이
- `main()`의 원형과 명령행 인자
- 예외와 try-catch-finally
- 과제 3(3장 연습문제)

---

## 1. 반복문

```java
for (int i = 1; i < 10; i += 2)
    System.out.print(i + " ");       // 1 3 5 7 9
```

- `for(초기; 조건; 증감)`의 세 부분은 생략할 수 있고, 쉼표로 여러 문장을 넣을 수 있다
- `i++`와 `++i`는 반복문의 증감 자리에서는 결과가 같다(값을 따로 쓰지 않으므로). 식 안에서 값을 쓸 때만 다르다
- `continue`는 이번 반복의 나머지를 건너뛰고, `break`는 반복문을 빠져나간다

### 연습문제: 3의 배수 합 (continue)

```java
int sum = 0, i = 1;
while (i < 100) {
    if (i % 3 != 0) {
        i++;
        continue;          // 3의 배수가 아니면 건너뜀
    }
    else sum += i;
    i++;
}
```

실행하면 sum = **1683** (3 + 6 + … + 99).

### 연습문제: 1~50의 합 (break)

```java
while (true) {
    if (i > 50) break;
    sum += i;
    i++;
}
```

---

## 2. 배열

```java
int[] n = {1, 2, 3, 4, 5};     // 선언 + 생성 + 초기화
int n2[] = new int[3];         // 선언 + 생성 (0으로 초기화)
int n3[3];                     // 오류: 선언할 때 크기를 쓸 수 없음
```

- 배열 변수는 **배열을 가리키는 레퍼런스**다. 선언만 하면 레퍼런스만 있고 배열은 없다
- `new`로 생성해야 메모리가 잡힌다
- 크기는 `n.length`, 범위를 넘으면 실행 중 `ArrayIndexOutOfBoundsException`

### 비정방형 2차원 배열

```java
int n[][] = { {1}, {1, 2, 3}, {1}, {1, 2, 3, 4}, {1, 2} };
for (int j = 0; j < n.length; j++)
    System.out.print(n[j].length + " ");
```

실행 결과

```text
1 3 1 4 2
```

2차원 배열은 "1차원 배열의 배열"이라 행마다 길이가 다를 수 있다. `n.length`는 행 수, `n[j].length`는 j행의 열 수.

### for-each

```java
char[] alpha = {'a', 'b', 'c', 'd'};
for (char c : alpha)
    System.out.print(c);
```

---

## 3. 메소드에 배열 넘기기

```java
static void replaceSpace(char a[]) {
    for (int i = 0; i < a.length; i++)
        if (a[i] == ' ')
            a[i] = ',';
}

char c[] = {'T','h','i','s',' ','i','s',' ','a',' ','P','e','n','c','i','l','.'};
printCharArray(c);
replaceSpace(c);
printCharArray(c);
```

실행 결과

```text
This is a Pencil.
This,is,a,Pencil.
```

> **(중요!!)** 기본 타입(int 등)을 넘기면 **값이 복사**되어 메소드 안에서 바꿔도 원래 변수는 그대로다. 배열이나 객체를 넘기면 **레퍼런스가 복사**되어 메소드와 호출한 쪽이 같은 배열을 가리킨다. 그래서 `replaceSpace` 안에서 바꾼 내용이 원본 `c`에 남는다 (시험: 인자 값의 변화를 설명하라)
{: .prompt-warning }

---

## 4. main()과 예외

- 원형: `public static void main(String[] args)`. 명령행 `java Hello abc 5`면 `args = {"abc", "5"}`(모두 문자열)
- 컴파일 오류는 컴파일러가, **예외**는 실행 중에 생기는 오류다. 처리하지 않으면 프로그램이 바로 종료된다
- 예외가 날 수 있는 코드는 `try`, 처리 코드는 `catch`, 항상 실행할 코드는 `finally`(생략 가능)

---

## 5. 과제 3: 3장 연습문제 (p.163~165)

> **과제 3**: 교재 3장 연습문제(이론) 1~14번
{: .prompt-info }

| 번호 | 답 | 확인 |
| --- | --- | --- |
| 1 | 출력 `1 3 5 7 9`, 1~9의 홀수 출력 | |
| 2 | ③ `for(int i=0; i>10; i++) sum++;` | 실행: ①·②·④는 45, ③은 0 |
| 3 | `continue` | 실행: 1683 |
| 4 | `i > 50` | |
| 5 | ④ `int n[3] = new int[3];` | 선언에 크기 불가 |
| 6 | ④ `array[array.length] = 100;` | 인덱스 5는 범위 밖(0~4) |
| 7 | `char c[] = new char[10];`, `int n[] = {0,1,2,3,4,5};`, `char day[] = {'일','월','화','수','목','금','토'};` | |
| 10 | `int i = 0; i < alpha.length; i++` / `char c : alpha` | |
| 11 | `1 3 1 4 2` | 실행 확인 |
| 13 | ① 원형은 `public static void main` | |
| 14 | 컴파일러, 예외, 바로 종료, try, catch, finally | |

### 검토하면서 고친 답 3개

**2번의 이유**: 제출 답은 "sum++는 1씩만 증가시켜서"였지만, 실제 이유는 조건 `i > 10`이 처음부터 거짓이라 **반복이 한 번도 실행되지 않아** sum이 0이기 때문이다. `i < 10`이었다면 sum++로도 10이 되어 역시 45와 달랐을 것이다.

**8번 (3)**: "1~12로 초기화되는 **4행 3열**" 배열인데 제출 답 `{ {1,2,3,4},{5,6,7,8},{9,10,11,12} }`는 3행 4열이다.

```java
int val[][] = { {1, 2, 3}, {4, 5, 6}, {7, 8, 9}, {10, 11, 12} };   // 4행 3열
```

`val.length` = 4, `val[0].length` = 3으로 확인했다.

**9번**: 제출 답은 "첫 번째 줄 `int myArray[];`에서 오류"였지만, 선언 자체는 문법상 맞다. 컴파일해 보면

```text
Q9.java:2: error: variable myArray might not have been initialized
myArray[0] = 1;
^
```

**두 번째 줄**, 즉 생성하지 않은 배열을 **사용하는 곳**에서 오류가 난다. 수정은 `int myArray[] = new int[2];`로 같다.

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 과제 3의 2번 해설 | sum++는 1씩 증가해서 다름 | 조건이 처음부터 거짓이라 반복이 0번 | 실행 결과 sum = 0 |
| 과제 3의 8번 (3) | 3행 4열 배열 | 4행 3열 `{ {1,2,3},{4,5,6},{7,8,9},{10,11,12} }` | 문제 조건 |
| 과제 3의 9번 | 첫 번째 줄에서 오류 | 두 번째 줄(초기화되지 않은 배열 사용) | javac 오류 메시지 |
| 9/25 실습 메소드 이름 | replacSpace | replaceSpace | 오타 |

---

## 최종 핵심 정리

1. for의 세 부분은 생략·쉼표 사용이 가능하고, continue는 건너뛰기, break는 탈출
2. 1~99 중 3의 배수 합은 1683(continue 예제)
3. 배열 변수는 레퍼런스이고, 선언과 생성(new)은 별개이며 선언에 크기를 쓸 수 없다
4. 2차원 배열은 배열의 배열이라 행마다 길이가 다를 수 있다(`1 3 1 4 2`)
5. 기본 타입은 값이 복사되고, 배열·객체는 레퍼런스가 복사되어 메소드 안의 변경이 원본에 남는다
6. `main`의 원형은 `public static void main(String[] args)`이고 명령행 인자는 문자열 배열로 들어온다
7. 예외는 실행 중 오류이며 try-catch로 처리하고 finally는 생략할 수 있다
8. 연습문제 2번의 이유, 8번(4행 3열), 9번(오류 위치)을 실행으로 다시 확인해 정정했다

---

End.
