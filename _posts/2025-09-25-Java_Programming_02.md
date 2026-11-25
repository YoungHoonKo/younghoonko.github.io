---
title: 02. Java Programming_Types, Operators, Control Statements and HW2
description: "자바 프로그래밍 및 실습 2장과 과제 2: 식별자 규칙, 기본 타입과 문자열, 리터럴(16진수, 2진수, 지수, 밑줄), var와 final, 강제 형 변환, Scanner, 증감·비트·논리 연산자, switch와 break, 초를 시·분·초로 나누기, 연습문제에서 틀린 출력 답 정정"
Author: YoungHoon Ko
date: 2025-09-25 12:00:00 +0900
categories: [University(2025-Second-Semester), Java Programming]
tags: [java]
image: /assets/img/lang/java.png
---

## 학습 목표

- 식별자(이름) 규칙과 관례
- 기본 타입 8개와 문자열(String)의 차이
- 리터럴 표기(16진수, 2진수, 지수, 밑줄)와 `var`, `final`
- 강제 형 변환과 Scanner 입력
- 증감·비트·논리 연산자와 문자 비교
- switch문과 break, 문자열 switch
- 과제 2(2장 연습문제) 풀이와 실행 검증

---

## 1. 식별자

변수, 상수, 클래스, 메소드에 붙이는 이름

- 특수문자는 `_`와 `$`만 가능, 숫자로 시작할 수 없음
- 키워드(`if`, `class` 등)와 `true`, `false`, `null`은 사용 불가
- 대소문자 구분, 한글도 가능(권장하지 않음), 길이 제한 없음
- 관례: 클래스는 대문자로 시작(`StudentInfo`), 변수·메소드는 소문자로 시작하는 카멜 표기(`totalScore`), 상수는 전부 대문자(`MAX_SIZE`)

시험 유형: 잘못된 식별자 고르기 → 숫자로 시작(`1var`), 예약어(`if`, `true`), 허용되지 않는 특수문자(`%j`)

---

## 2. 타입

| 분류 | 타입 | 크기 |
| --- | --- | --- |
| 논리 | boolean | 1바이트 |
| 문자 | char | **2바이트(유니코드)** |
| 정수 | byte, short, int, long | 1, 2, 4, 8바이트 |
| 실수 | float, double | 4, 8바이트 |

- `String`은 기본 타입이 아니라 **클래스**다. `'A'`는 문자(char), `"A"`는 문자열
- `null`은 레퍼런스에만 대입할 수 있다(`int x = null` 불가)

### 리터럴

```java
System.out.println((char)0x41);    // 16진수 0x41 = 65 → 'A'
System.out.println((int)'a');      // 97
System.out.println('a' < 'b');     // 문자 코드 97 < 98
System.out.println((byte)0b0110);  // 2진수 → 6
System.out.println(1234E-4);       // 1234 × 10^-4
System.out.println(1_000_000);     // 밑줄은 무시
```

실행 결과

```text
A
97
true
6
0.1234
1000000
```

숫자 리터럴의 밑줄은 끝, 소수점 앞뒤, `L`·`F` 앞, `0x`의 `x` 앞뒤에는 쓸 수 없다.

### var와 final

- `var`(자바 10): 지역 변수의 타입을 **초기값으로 추론**한다. 컴파일할 때 타입이 정해지므로 동적 타입이 아니고, 초기값이 꼭 있어야 한다(`var v = 10;` → int)
- `final`: 한 번만 대입할 수 있는 상수. `final double PI = 3.14;`

### 형 변환

- 작은 타입 → 큰 타입은 자동(int → double)
- 큰 타입 → 작은 타입은 강제 변환 `(int)3.99` = 3, `(int)-3.99` = −3 (0 쪽으로 버림)
- 문자열 연결은 왼쪽부터: `"1" + 2 + 3` = "123", `1 + 2 + "3"` = "33"

---

## 3. Scanner

```java
Scanner scanner = new Scanner(System.in);
String name = scanner.next();          // 공백 전까지 한 단어
int age = scanner.nextInt();
double weight = scanner.nextDouble();
boolean single = scanner.nextBoolean();
String line = scanner.nextLine();      // 줄 끝까지
```

입력 `홍길동 서울 20 65.1 true`

```text
이름은 홍길동, 도시는 서울, 나이는 20살, 체중은 65.1kg, 독신여부는 true입니다.
```

- Scanner는 **공백 단위**로 토큰을 나눈다
- 문자 하나는 `scanner.next().charAt(0)`으로 받는다

---

## 4. 연산자

### 증감 연산자

```java
int a, d = 3;
a = d++;   // a = 3, d = 4  (쓰고 나서 증가)
a = ++d;   // d = 5, a = 5  (증가하고 나서 쓰기)
```

### 나머지와 몫: 초를 시·분·초로

> 시험 가능 표시: 정수 하나를 시·분·초로 나누는 코드
{: .prompt-info }

```java
int time = 5000;
System.out.println((time / 60) / 60 + "시간 " + (time / 60) % 60 + "분 " + time % 60 + "초");
```

실행 결과

```text
1시간 23분 20초
```

### 비트·논리 연산

- `&&`, `||`, `!` 논리 연산, `^`는 XOR (`5 ^ 3` = 6, `true ^ true` = false)
- 범위 조건은 `80 < score < 90`처럼 쓸 수 없다. `80 < score && score < 90`으로 나눠 쓴다

---

## 5. switch

```java
switch (order) {
    case "에스프레소":
    case "카페라떼":
    case "카푸치노":
        price = 3500; break;
    case "아메리카노":
        price = 2000; break;
    default:
        System.out.println("메뉴에 없습니다.");
}
```

입력 "아메리카노" → `아메리카노는 2000원입니다.`, "카푸치노" → `카푸치노는 3500원입니다.`

- 여러 case를 붙여 쓰면 같은 처리를 공유한다
- **break가 없으면 아래 case까지 계속 실행된다(fall-through)**

---

## 6. 과제 2: 2장 연습문제 (p.107~109)

> **과제 2**: 교재 2장 연습문제(이론) 1~14번
{: .prompt-info }

| 번호 | 내용 | 답 |
| --- | --- | --- |
| 1 | 클래스 선언 키워드 | `class` |
| 2 | 10 + 20 출력 프로그램 오류 | `main` 메소드가 없음, 대입문은 메소드 안에 있어야 함 → 파일 `SampleProgram.java`, `javac SampleProgram.java`, `java SampleProgram` |
| 3 | 잘못된 식별자 | `%j`, `1var` |
| 5 | 수식의 값과 타입 | 67 + 12.8 = 79.8(double), 10 / 3 = 3(int), 10.0 / 3 = 3.333…(double), 10 == 9 → false(boolean) |
| 6 | 36.5로 초기화된 double 상수 | `final double bodyTemp = 36.5;` |
| 7 | 오류 수정 | `while(1)` → `while(true)`, `int n = 3.5` → `double n = 3.5`, `80 < score < 90` → `80 < score && score < 90` |
| 9, 10 | 조건 연산자로 바꾸기 | `sum = (sum > 100) ? 100 : 0;`, `System.out.println((n > 5) ? n : 5);` |

### 11, 12번: 이스케이프 문자 (실행 확인)

```java
String text = "\"을 출력하려면 \\ 다음에 \"를 붙여 \\\"과 같이 하면 됩니다.";
System.out.println(text);
System.out.println("나는 \"Java를 " + 100 + "%\"" + "사랑해");
```

실행 결과

```text
"을 출력하려면 \ 다음에 "를 붙여 \"과 같이 하면 됩니다.
나는 "Java를 100%"사랑해
```

제출 답안은 12번 결과를 `나는 "Java를 100%" 사랑해`처럼 **띄어 썼지만**, 실제로는 `"%\""` 다음에 붙는 문자열이 `"사랑해"`라서 공백이 없다.

### 13번: break 없는 switch

```java
int sum = 0;
switch (grade) {
    case 'A': sum += 100;
    case 'B': sum += 50;
    case 'C': sum += 30;
    case 'D': sum += 10;
}
```

| grade | 실행되는 case | sum |
| --- | --- | --- |
| A | A, B, C, D | 190 |
| B | B, C, D | 90 |
| C | C, D | 40 |
| F | 없음 | 0 |

직접 실행해 A에서 190을 확인했다. 실습 코드(9/18)는 같은 switch에 break를 넣어서 A → 100, C → 30이 나왔다.

### 14번: if-else를 문자열 switch로

```java
switch (in) {
    case "가위": System.out.println(1); break;
    case "바위": System.out.println(2); break;
    case "보":   System.out.println(3); break;
    default:     System.out.println(0);
}
```

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 과제 2의 12번 답 | `나는 "Java를 100%" 사랑해` | `나는 "Java를 100%"사랑해` | 실행 결과에 공백 없음 |
| 3주차 필기 | var는 동적 할당 | 컴파일 시점에 초기값으로 타입을 추론(정적 타입) | var 변수에 다른 타입을 다시 넣으면 컴파일 오류 |
| 2주차 필기 | char에 크기를 지정하면 문장도 들어간다 | char는 문자 하나, 문장은 `String` 또는 `char[]` 배열 | 타입 정의 |
| 9/18 실습 주석 | "break 문을 사용하지 않아서 끝까지 수행" | 실습 코드에는 break가 있어 해당 case만 실행 | 주석과 코드 불일치 |

---

## 최종 핵심 정리

1. 식별자는 `_`, `$` 외 특수문자 불가, 숫자 시작 불가, 키워드·true·false·null 불가
2. char는 2바이트 유니코드이고, String은 기본 타입이 아닌 클래스다
3. 리터럴: `0x41` = 'A', `0b0110` = 6, `1234E-4` = 0.1234, 밑줄은 무시
4. `var`는 초기값으로 타입을 추론하는 정적 타입이고, `final`은 한 번만 대입한다
5. `(int)`는 0 쪽으로 버리고, 문자열 연결은 왼쪽부터 계산한다
6. 5000초 = 1시간 23분 20초: `/60/60`, `/60%60`, `%60`
7. switch에서 break가 없으면 아래 case까지 실행된다(A → 190)
8. 연습문제 12번의 실제 출력은 `나는 "Java를 100%"사랑해`로 공백이 없다

---

End.
