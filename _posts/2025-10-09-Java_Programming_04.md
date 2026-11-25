---
title: 04. Java Programming_Classes, Objects, Constructors and HW4
description: "자바 프로그래밍 및 실습 4장과 과제 4: 캡슐화·상속·다형성, 클래스와 객체, 생성자와 기본 생성자, this와 this(), 객체 배열, 메소드 오버로딩, 가비지 컬렉션, 접근 지정자 4가지, static과 final. 연습문제 코드를 컴파일해 오류 확인"
Author: YoungHoon Ko
date: 2025-10-09 12:00:00 +0900
categories: [University(2025-Second-Semester), Java Programming]
tags: [java]
image: /assets/img/lang/java.png
---

## 학습 목표

- 객체지향의 세 특징(캡슐화, 상속, 다형성)
- 클래스와 객체(인스턴스), `new`
- 생성자, 기본 생성자가 자동으로 들어가는 조건, `this`와 `this()`
- 객체 배열: 배열 생성과 객체 생성은 별개
- 메소드 오버로딩 조건
- 가비지와 가비지 컬렉션
- 접근 지정자, static, final
- 과제 4(4장 연습문제)

---

## 1. 객체지향의 특징

| 특징 | 뜻 |
| --- | --- |
| 캡슐화 | 필드와 메소드를 클래스로 묶고 내부를 감춘다 → 외부에서 함부로 바꾸지 못함 |
| 상속 | 부모 클래스의 멤버를 물려받아 확장 → 코드 재사용, 중복 제거 |
| 다형성 | 같은 이름의 메소드가 객체에 따라 다르게 동작(오버로딩, 오버라이딩) |

- 절차지향은 작업 순서(함수의 집합)로, 객체지향은 객체들의 상호작용으로 프로그램을 표현한다

---

## 2. 클래스, 객체, 생성자

- 클래스: 객체를 만드는 **설계도(틀)**. 필드(변수)와 메소드(함수)로 구성
- 객체(인스턴스): 클래스로 `new`를 써서 만든 실체. 메모리를 차지한다

```java
public class Rectangle {
    int width;
    int height;
    public int getArea() { return width * height; }

    public static void main(String[] args) {
        Rectangle rect = new Rectangle();
        Scanner scanner = new Scanner(System.in);
        rect.width = scanner.nextInt();
        rect.height = scanner.nextInt();
        System.out.println("Area of Rectangle is " + rect.getArea());
    }
}
```

입력 `4 5` → `Area of Rectangle is 20`

### 생성자

- 이름이 클래스 이름과 같고, 리턴 타입이 없다
- 객체가 생성될 때 **반드시 하나가 호출**된다. 여러 개를 만들 수 있다(오버로딩)
- 생성자를 **하나도 만들지 않으면** 컴파일러가 매개변수 없는 **기본 생성자**를 넣어 준다
- 생성자를 **하나라도 만들면** 기본 생성자는 자동으로 들어가지 않는다. 그때 `new Book()`을 쓰면 컴파일 오류
- 리턴 값은 없지만 `return;`으로 생성자를 중간에 끝낼 수는 있다

### this와 this()

- `this`: 지금 객체 자신. 매개변수와 필드 이름이 같을 때 `this.size = size;`
- `this()`: 같은 클래스의 **다른 생성자**를 호출. 생성자의 **첫 줄**에만 올 수 있다
- static 메소드에서는 객체가 없으니 `this`를 쓸 수 없다

---

## 3. 객체 배열

```java
class Circle {
    int radius;
    public Circle(int radius) { this.radius = radius; }
    public double getArea() { return 3.14 * radius * radius; }
}

Circle[] c = new Circle[5];        // 레퍼런스 5칸만 생김 (모두 null)
for (int i = 0; i < c.length; i++)
    c[i] = new Circle(i);          // 객체는 여기서 생성
for (int i = 0; i < c.length; i++)
    System.out.print((int)(c[i].getArea()) + " ");
```

실행 결과

```text
0 3 12 28 50
```

`new Circle[5]`는 객체 5개가 아니라 **레퍼런스 5개**를 만든다(연습문제 4번).

---

## 4. 가비지 컬렉션

- 가비지: 가리키는 레퍼런스가 하나도 없는 객체
- 자바는 객체를 직접 해제할 수 없고, JVM이 가용 메모리가 부족할 때 가비지를 자동으로 수거한다
- `System.gc()`는 수거를 **요청**할 뿐 즉시 실행을 보장하지 않는다
- 장점: 메모리 해제 실수(누수, 이중 해제)가 줄어듦 / 단점: 언제 실행될지 예측하기 어렵고, 실행 중 잠깐 멈출 수 있음

```java
String a = new String("aa");
String b = a;
String c = b;
a = null;
b = null;     // c가 아직 "aa"를 가리킴 → 가비지 아님
```

---

## 5. 접근 지정자, static, final

| 지정자 | 같은 클래스 | 같은 패키지 | 다른 패키지(상속) | 다른 패키지 |
| --- | --- | --- | --- | --- |
| public | O | O | O | O |
| protected | O | O | O | X |
| default(생략) | O | O | X | X |
| private | O | X | X | X |

```java
class Sample {
    public int a;
    private int b;
    int c;              // default
}
Sample s = new Sample();
s.a = 10;   // O
s.b = 10;   // 컴파일 오류: private
s.c = 10;   // O (같은 패키지)
```

### static

| 구분 | static 멤버 | 인스턴스 멤버 |
| --- | --- | --- |
| 생성 시점 | 클래스가 로딩될 때 1번 | 객체를 만들 때마다 |
| 공유 | 모든 객체가 공유 | 객체마다 따로 |
| 접근 | `클래스명.멤버` | `참조변수.멤버` |
| 제약 | `this` 불가, 인스턴스 멤버 직접 사용 불가 | 없음 |

언제 static을 쓰나: **모든 객체가 공유하는 값**(예: 개수 카운터), **객체 상태와 무관한 기능**(예: `Math.abs`)

### final

| 대상 | 의미 |
| --- | --- |
| final 변수 | 값을 한 번만 대입 (상수) |
| final 메소드 | 오버라이딩 불가 |
| final 클래스 | 상속 불가 |

---

## 6. 과제 4: 4장 연습문제 (p.240~244)

> **과제 4**: 교재 4장 연습문제(이론) 1~16번
{: .prompt-info }

| 번호 | 답 | 이유 |
| --- | --- | --- |
| 1 | ④ | 필드는 private + getter/setter가 바람직 (정보 은닉) |
| 2 | ④ | 생성자에서도 `return;`은 쓸 수 있음 |
| 3 | ③ `Power p[10] = ...` | 선언에 크기 불가 |
| 4 | ② | 객체 10개가 아니라 레퍼런스 10개 |
| 5 | ① | 리턴 타입만 다른 것은 오버로딩이 아님(매개변수 목록이 달라야 함) |
| 9 | 소멸시킬, 레퍼런스, 가비지, 가비지 컬렉션, `System.gc()` | |
| 10 | (1) 발생 (2) 발생 안 함 | (1) 반복마다 이전 String을 잃음 (2) c가 계속 참조 |
| 11 | ④ `static int g() { return getB(); }` | static 메소드에서 인스턴스 메소드 호출 불가 |
| 12 | ① `StaticSample.x = 5;` | x는 인스턴스 필드 |
| 13 | `f()`를 static으로 하거나 객체를 만들어 호출 | static `main`에서 인스턴스 메소드 직접 호출 불가 |
| 14 | final 필드 `x++`, final 클래스 상속, final 메소드 오버라이딩 | 3가지 모두 컴파일 오류 |

### 6번: this()로 생성자 정리

제출 답안을 그대로 컴파일하니 오류가 났다.

```text
TV.java:6: error: cannot find symbol
  symbol: variable manufacturer
```

필드 이름을 `String manufacture;`(r 빠짐)로 써 놓고 생성자에서는 `this.manufacturer`를 썼기 때문이다. 필드 이름을 맞추면 정상 동작한다.

```java
class TV {
    private int size;
    private String manufacturer;

    public TV() { this(32, "LG"); }
    public TV(String manufacturer) { this(32, manufacturer); }
    public TV(int size, String manufacturer) {
        this.size = size;
        this.manufacturer = manufacturer;
        System.out.println(size + "인치 " + manufacturer);
    }
}
```

`new TV()`, `new TV("삼성")`, `new TV(65, "삼성")` 실행 결과

```text
32인치 LG
32인치 삼성
65인치 삼성
```

`this()`를 쓰면 초기화·출력 코드가 생성자 하나에만 있어서 중복이 없어지고, 나중에 바꿀 곳도 한 군데다.

### 7번: private 필드 접근 오류의 해결 방법

`aPerson.age = 17;`은 age가 private이라 오류다. 해결 방법은 public으로 바꾸기, default로 바꾸기(같은 패키지), 생성자로 초기화, setter 추가가 있다. 객체지향에 가장 맞는 것은 **setter**다. 필드는 숨기고 `setAge()` 안에서 `age > 0` 같은 검사를 할 수 있기 때문이다.

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 과제 4의 6번 코드 | 필드 `manufacture` | `manufacturer` | 컴파일 오류 (javac 확인) |
| 4주차 필기 | "기본 생성자는 자동 삽입되지 않음" | 생성자가 하나도 없을 때만 자동 삽입 | 조건이 빠져 있었음 |
| 6주차 필기 | static 사용: 객체마다 달라야 하는 값 | 그것은 인스턴스 필드. static은 공유 값, 객체와 무관한 기능 | 정의 |
| 4주차 필기 | "상속의 이유는 정보보안" | 상속의 주목적은 **재사용과 확장**, 정보 보호는 캡슐화의 목적 | 개념 구분 |

---

## 최종 핵심 정리

1. 객체지향의 세 특징은 캡슐화(감추기), 상속(재사용), 다형성(같은 이름, 다른 동작)
2. 클래스는 틀, 객체는 `new`로 만든 실체이며, `new Circle[5]`는 레퍼런스 5개만 만든다
3. 생성자가 하나도 없을 때만 기본 생성자가 자동으로 들어간다
4. `this()`는 같은 클래스의 다른 생성자를 부르며 첫 줄에만 쓸 수 있다
5. 오버로딩은 매개변수 목록이 달라야 하고, 리턴 타입만 다르면 오류다
6. 레퍼런스가 하나도 없는 객체가 가비지이고, `System.gc()`는 수거 요청일 뿐이다
7. 접근 범위: public > protected(패키지 + 자식) > default(패키지) > private(클래스)
8. static은 공유·객체 무관 기능, final은 값·오버라이딩·상속을 막는다

---

End.
