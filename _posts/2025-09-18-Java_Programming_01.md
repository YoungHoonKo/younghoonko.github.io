---
title: 01. Java Programming_Java Basics (JVM, Bytecode, JDK) and HW1
description: "자바 프로그래밍 및 실습 1장과 과제 1: 자바의 탄생 목적과 WORA, 바이트코드와 JVM, JDK·JRE·API, javac·java 명령, 파일 이름 규칙과 내부 클래스의 .class 파일, 자바의 특징(가비지 컬렉션, 전역 변수 없음, 패키지)"
Author: YoungHoon Ko
date: 2025-09-18 12:00:00 +0900
categories: [University(2025-Second-Semester), Java Programming]
tags: [java]
image: /assets/img/lang/java.png
---

## 학습 목표

- 자바가 만들어진 목적과 WORA의 의미
- 소스 → 바이트코드 → JVM으로 이어지는 실행 구조
- JDK, JRE, Java API의 차이
- 컴파일·실행·역어셈블 명령어
- 소스 파일 이름 규칙과 컴파일 결과 클래스 파일
- 과제 1(1장 연습문제) 풀이와 검증

---

## 1. 자바의 탄생

- 1991년 썬 마이크로시스템즈의 **제임스 고슬링**이 시작, 처음 이름은 **Oak**
- 목적: 플랫폼(CPU, OS)이 제각각인 **가전제품**에서 똑같이 돌아가고, 메모리가 적은 내장형 시스템에도 맞는 언어
- 1995년 공개. 언어 순서는 C(1972) → C++(1983) → Java(1995) → C#(2000)

### WORA (Write Once, Run Anywhere)

한 번 작성해 컴파일하면 JVM이 있는 어떤 하드웨어·OS에서도 그대로 실행된다.

---

## 2. 실행 구조

```text
Hello.java ──javac──▶ Hello.class (바이트코드) ──JVM──▶ 실행
```

| 구성 요소 | 설명 |
| --- | --- |
| 바이트코드 | 특정 CPU에 종속되지 않는 중간 코드. `.class` 파일에 저장 |
| JVM (Java Virtual Machine) | 바이트코드를 해석·실행하는 가상 기계. **JVM 자체는 플랫폼마다 다르게** 만들어진다 |
| 결과 | 자바 프로그램은 플랫폼 독립, JVM은 플랫폼 종속 |

- C는 컴파일 후 **링크** 과정을 거쳐 실행 파일 하나를 만들지만, 자바는 링크 없이 실행 중에 필요한 클래스를 그때그때 로딩한다. 그래서 적은 메모리로 시작할 수 있다
- 요즘 JVM은 자주 쓰는 코드를 실행 중에 기계어로 바꾸는 **JIT 컴파일러**도 함께 쓴다

### 명령어

| 일 | 명령 |
| --- | --- |
| 컴파일 | `javac Hello.java` |
| 실행 | `java Hello` (`.class`를 붙이지 않음) |
| 역어셈블 | `javap -c Hello` (바이트코드 보기) |

---

## 3. JDK, JRE, API

| 이름 | 풀네임 | 내용 |
| --- | --- | --- |
| JDK | Java Development Kit | 개발 도구(javac, javap 등) + JRE |
| JRE | Java Runtime Environment | JVM + 실행에 필요한 라이브러리 |
| Java API | Application Programming Interface | 미리 만들어 둔 클래스 라이브러리. 패키지 단위로 JDK 안에 있음 |
| IDE | Integrated Development Environment | 이클립스, VS Code 등 |

프로그램을 **개발**하려면 JDK가 필요하다(JRE만으로는 컴파일할 수 없다).

---

## 4. 자바 프로그램의 기본 구조

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

- 실행은 항상 `public static void main(String[] args)`에서 시작
- `println`은 출력 후 줄바꿈, `print`는 줄바꿈 없음
- 문장 끝은 `;`
- **public 클래스는 파일당 하나**이고, 파일 이름은 그 클래스 이름과 같아야 한다(`Hello.java`)

---

## 5. 과제 1: 1장 연습문제 (명품 자바 프로그래밍 p.53~54)

> **과제 1**: 교재 1장 연습문제 1~11번
{: .prompt-info }

| 번호 | 묻는 것 | 답 |
| --- | --- | --- |
| 1 | 자바 소스·컴파일 파일 확장자 | `.java`, `.class` |
| 2 | 자바를 처음 개발한 사람 | 제임스 고슬링 |
| 3 | Java, C, C++, C# 등장 순서 | C → C++ → Java → C# |
| 4 | 자바를 만든 목적 | 다양한 플랫폼의 가전제품에 쓸 플랫폼 독립적 언어, 내장형 시스템의 적은 메모리 요구 |
| 5 | 자바 프로그램을 직접 실행하는 것 | 자바 가상 기계(JVM) |
| 6 | "한 번 작성하면 어디서나 실행" 줄임말 | WORA |
| 7 | 틀린 설명 | ④ "클래스 파일 하나에 여러 클래스를 저장" → 클래스마다 `.class` 파일이 따로 생김 |
| 8 | 응용 프로그램 개발에 필요한 것 | JDK |
| 9 | 틀린 설명 | ③ "클래스 바깥에 전역 함수·변수를 둘 수 있다" → 자바는 모든 것이 클래스 안에 있음 |
| 10 | `public class Shape`의 파일 이름 | `Shape.java` |

### 11번: 내부 클래스와 .class 파일

`public class A` 안에 클래스 B, 파일에 함께 있는 `class C` 안에 클래스 D가 있을 때

- 파일 이름: `A.java` (public 클래스 이름)
- 생성되는 파일: `A.class`, `A$B.class`, `C.class`, `C$D.class`

직접 컴파일해 확인했다.

```java
public class A { class B { } }
class C { class D { } }
```

```text
$ javac A.java && ls *.class
A$B.class  A.class  C$D.class  C.class
```

- 내부 클래스는 `바깥클래스$내부클래스.class`로 저장된다
- 그래서 "소스 파일 하나당 클래스 파일 하나"는 틀린 말이다. **클래스 하나당** `.class` 파일 하나

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 중간 대비 노트 | 소스(.java) 하나당 1개의 .class 파일 | 클래스 하나당 .class 하나 | 11번처럼 파일 하나에서 4개가 생김 (컴파일 확인) |
| 2주차 필기 | 10진수를 2, 6, 8진수로 변환 | 2, 8, 16진수 | 6진수는 다루지 않음 |
| 2주차 필기 | 대문자로 시작하는 이름은 클래스에만 | 문법 규칙이 아니라 **관례** | 변수를 대문자로 시작해도 컴파일됨 |
| 중간 대비 노트 | JVM은 인터프리트 방식 | 인터프리터 + JIT 컴파일러 | 현재 HotSpot JVM 동작 |

---

## 최종 핵심 정리

1. 자바는 제임스 고슬링이 가전제품용 플랫폼 독립 언어로 만들었고, 처음 이름은 Oak였다
2. WORA: 한 번 컴파일한 바이트코드를 JVM이 있는 어디서나 실행한다
3. 바이트코드는 CPU 중립적인 `.class` 파일이고, JVM은 플랫폼마다 다르게 구현된다
4. 자바는 링크 과정 없이 실행 중 필요한 클래스를 로딩한다
5. JDK = 개발 도구 + JRE, JRE = JVM + 라이브러리. 개발에는 JDK가 필요하다
6. `javac Hello.java`로 컴파일, `java Hello`로 실행, `javap -c`로 바이트코드를 본다
7. public 클래스는 파일당 하나이고 파일 이름과 같아야 한다
8. 클래스마다 `.class`가 하나씩 생기며 내부 클래스는 `A$B.class` 형태다

---

End.
