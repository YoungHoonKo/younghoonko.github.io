---
title: 05. Java Programming_Inheritance, Polymorphism, Interfaces and HW5
description: "자바 프로그래밍 및 실습 5·6장과 과제 5: 상속과 extends, protected, 생성자 호출 순서와 super(), 업캐스팅·다운캐스팅·instanceof, 메소드 오버라이딩과 동적 바인딩, 추상 클래스와 인터페이스, 계산기 과제, 패키지·모듈·Object·Wrapper 클래스"
Author: YoungHoon Ko
date: 2025-10-16 12:00:00 +0900
categories: [University(2025-Second-Semester), Java Programming]
tags: [java]
image: /assets/img/lang/java.png
---

## 학습 목표

- 상속(`extends`)의 목적과 자바 상속의 특징
- 접근 지정자에 따른 상속 멤버 접근(protected)
- 생성자 호출 순서와 `super()`
- 업캐스팅, 다운캐스팅, `instanceof`
- 오버라이딩과 동적 바인딩, 오버로딩과의 차이
- 추상 클래스와 인터페이스
- 과제 5(추상 클래스 계산기)
- 패키지, 모듈, Object, Wrapper 클래스

---

## 1. 상속

```java
class Person {
    private int weight;
    int age;
    protected int Height;
    public String name;
    public void setWeight(int weight) { this.weight = weight; }
    public int getWeight() { return weight; }
}

class Student extends Person {
    public void set() {
        age = 30;           // default: 같은 패키지라 가능
        name = "홍길동";     // public
        Height = 175;       // protected: 자식 클래스라 가능
        // weight = 99;     // private: 불가
        setWeight(99);      // public 메소드로 간접 접근
    }
}
```

실행 결과

```text
몸무게: 99
이름: 홍길동
나이: 30
키: 175
```

- 부모 = 슈퍼 클래스, 자식 = 서브 클래스
- 자바는 클래스의 **다중 상속을 지원하지 않는다**(인터페이스는 여러 개 구현 가능). 상속 단계 수에는 제한이 없다
- **모든 클래스의 최상위 조상은 `java.lang.Object`**. `extends`를 안 쓰면 자동으로 Object를 상속한다

> **(중요!!)** 슈퍼 클래스의 protected 멤버: 같은 패키지의 모든 클래스 + **다른 패키지라도 상속받은 서브 클래스**에서 접근 가능
{: .prompt-warning }

### 생성자 호출 순서

서브 클래스 객체를 만들면 **서브 클래스 생성자가 먼저 호출**되지만, 그 생성자의 본문이 실행되기 전에 슈퍼 클래스 생성자가 먼저 실행된다. 그래서 출력은 위에서 아래로 나온다.

```java
class A0 { A0() { System.out.println("A 생성자"); } }
class B0 extends A0 { B0() { System.out.println("B 생성자"); } }
class C0 extends B0 { C0() { System.out.println("C 생성자"); } }
new C0();
```

```text
A 생성자
B 생성자
C 생성자
```

- 서브 클래스 생성자에서 슈퍼 클래스 생성자를 고르지 않으면 컴파일러가 `super()`(기본 생성자)를 넣는다. 슈퍼 클래스에 기본 생성자가 없으면 오류
- 다른 생성자를 부르려면 `super(x, y)`를 **첫 줄**에 쓴다

```java
class ColorPoint extends Point {
    private String color;
    public ColorPoint(int x, int y, String color) {
        super(x, y);          // Point(int, int) 호출
        this.color = color;
    }
    public void showColorPoint() {
        System.out.print(color);
        showPoint();
    }
}
new ColorPoint(5, 6, "blue").showColorPoint();   // blue(5,6)
```

---

## 2. 업캐스팅과 instanceof

- **업캐스팅**: 서브 클래스 객체를 슈퍼 클래스 레퍼런스로 가리킴(자동). 사람은 생물이다
- 업캐스팅된 레퍼런스로는 슈퍼 클래스의 멤버만 보인다(`p.grade` 불가)
- **다운캐스팅**: 다시 서브 클래스 타입으로 바꿈. `(Student)p`처럼 **명시**해야 한다
- `instanceof`: 레퍼런스가 가리키는 **실제 객체**의 타입을 검사해 true/false

```java
Person p = new Student("이재문");   // 업캐스팅
System.out.println(p.name);         // name: 이재문
```

---

## 3. 오버라이딩과 동적 바인딩

슈퍼 클래스의 메소드를 서브 클래스가 **같은 이름·매개변수·리턴 타입으로 다시 구현**하는 것. 목적은 **다형성의 실현**이다.

```java
class Shape { public void draw() { System.out.println("Shape"); } }
class Line extends Shape { public void draw() { System.out.println("Line"); } }
class Rect extends Shape { public void draw() { System.out.println("Rect"); } }
class Circle extends Shape { public void draw() { System.out.println("Circle"); } }

static void paint(Shape p) { p.draw(); }

paint(new Line());  paint(new Shape());  paint(new Rect());  paint(new Circle());
```

실행 결과

```text
Line
Shape
Rect
Circle
```

- `paint`는 Shape 타입으로 받지만 **실제 객체의 draw()** 가 실행된다(동적 바인딩)
- 같은 원리로 `Shape`에 `next` 필드를 두고 Line → Rect → Line → Circle을 연결 리스트로 이어 돌리면 각자의 draw가 차례로 실행된다
- `super.draw()`는 슈퍼 클래스의 메소드를 부르고, 이것은 컴파일 때 결정된다(정적 바인딩)

### 오버로딩 vs 오버라이딩 (객관식 출제 가능)

| 구분 | 오버로딩 | 오버라이딩 |
| --- | --- | --- |
| 위치 | 같은 클래스(또는 상속 관계) | 상속 관계 |
| 이름 | 같음 | 같음 |
| 매개변수 | **달라야 함** | **같아야 함** |
| 리턴 타입 | 상관없음 | 같아야 함 |
| 바인딩 | 정적(컴파일 시) | 동적(실행 시) |
| 목적 | 이름 하나로 여러 입력 처리 | 다형성 |

### 시험 대비 실습: 무한 재귀 함정

```java
class SuperClass {
    public void paint() { draw(); }
    public void draw() { draw(); System.out.println("SuperClass"); }
}
class SubClass extends SuperClass {
    public void paint() { draw(); }
    public void draw() { System.out.println("SubClass"); }
}
SuperClass obj = new SubClass();
obj.paint();       // SubClass
```

- `obj`의 실제 객체가 SubClass라서 오버라이딩된 draw가 실행되고 "SubClass"가 나온다
- 하지만 `new SuperClass().paint()`를 실행하면 `SuperClass.draw()`가 **자기 자신을 끝없이 호출**해서 `StackOverflowError`가 난다(직접 실행 확인). 슈퍼 클래스의 draw를 부르려던 것이라면 서브 클래스에서 `super.draw()`를 써야 한다

---

## 4. 추상 클래스와 인터페이스

| 구분 | 추상 클래스 | 인터페이스 |
| --- | --- | --- |
| 선언 | `abstract class` | `interface` |
| 메소드 | 추상 + 일반 메소드 | 추상 메소드(기본 `public abstract`), default·static·private 메소드 |
| 필드 | 일반 필드 가능 | 상수만 (`public static final`) |
| 객체 생성 | 불가 | 불가 |
| 사용 | `extends` (하나만) | `implements` (여러 개) |
| 목적 | 공통 개념 + 일부 구현 | 규격(약속) |

추상 메소드는 선언만 하고 구현하지 않으며, 서브 클래스가 **반드시 오버라이딩**해야 한다. 슈퍼 클래스는 개념을 정의하고 서브 클래스가 구체적인 행동을 정한다.

```java
interface PhoneInterfaceEx {
    final int TIMEOUT = 10000;
    void sendCall();
    void receiveCall();
    default void printLogo() { System.out.println("** Phone **"); }
}
class SamsungPhone implements PhoneInterfaceEx {
    public void sendCall() { System.out.println("띠리리리링"); }
    public void receiveCall() { System.out.println("전화가 왔습니다."); }
    public void flash() { System.out.println("전화기에 불이 켜졌습니다."); }
}
```

실행 결과

```text
** Phone **
띠리리리링
전화가 왔습니다.
전화기에 불이 켜졌습니다.
TIMEOUT: 10000
```

---

## 5. 과제 5: 추상 클래스로 계산기

> **과제 5**: 추상 클래스 `Calculator`(add, subtract, average)를 상속받아 `GoodCalc`를 구현하라(교재 예제 확장). 소스와 실행 화면 제출
{: .prompt-info }

곱셈(mul), 나눗셈(div)을 추가로 구현했다.

```java
abstract class Calculator {
    public abstract int add(int a, int b);
    public abstract int subtract(int a, int b);
    public abstract int mul(int a, int b);        // 추가
    public abstract int div(int a, int b);        // 추가
    public abstract double average(int[] a);
}

public class GoodCalc extends Calculator {
    @Override public int add(int a, int b) { return a + b; }
    @Override public int subtract(int a, int b) { return a - b; }
    @Override public int mul(int a, int b) { return a * b; }
    @Override public int div(int a, int b) {
        if (b == 0) throw new ArithmeticException("Division by zero is not allowed.");
        return a / b;
    }
    @Override public double average(int[] a) {
        double sum = 0;
        for (int i = 0; i < a.length; i++) sum += a[i];
        return sum / a.length;
    }

    public static void main(String[] args) {
        GoodCalc c = new GoodCalc();
        System.out.println(c.add(2, 3));
        System.out.println(c.subtract(2, 3));
        System.out.println(c.mul(2, 3));
        System.out.println(c.div(6, 3));
        System.out.println(c.average(new int[] {2, 3, 4}));
    }
}
```

실행 결과

```text
5
-1
6
2
3.0
```

- `@Override`를 붙이면 이름이나 매개변수를 잘못 써서 오버라이딩이 안 된 경우 컴파일러가 잡아 준다
- `sum`을 double로 둬서 평균이 정수 나눗셈으로 잘리지 않는다
- `div`는 정수 몫이라 `div(7, 2)`는 3이다. 0으로 나누면 직접 만든 메시지로 예외를 던진다(`int`를 0으로 나누면 어차피 ArithmeticException이 나지만 메시지가 더 분명해짐)
- 빈 배열을 `average`에 넣으면 0.0 / 0 = NaN이 된다

---

## 6. 패키지, 모듈, Object, Wrapper (6장)

- **패키지**: 관련된 클래스·인터페이스의 컴파일된 파일을 한 디렉터리로 묶은 것. 선언은 소스 첫 줄 `package 이름;`
- `java.lang`은 **import 없이** 쓸 수 있다(String, Math, Integer, System 등)
- import 없이 다른 패키지 클래스를 쓰려면 전체 이름을 쓴다: `java.util.Calendar c = java.util.Calendar.getInstance();`
- **모듈**(자바 9): 자바 API를 여러 모듈로 나눠 필요한 모듈만으로 실행 환경을 만든다. 메모리가 적은 소형 기기에 맞는 작은 실행 이미지를 만들기 위함 (교수님이 매우 중요하다고 한 부분)

### Wrapper 클래스

기본 타입을 객체로 다루기 위한 **클래스**: Byte, Short, Integer, Long, Float, Double, Character, Boolean

```java
System.out.println(Character.toLowerCase('A'));
System.out.println(Integer.parseInt("-123"));
System.out.println(Integer.toBinaryString(28));
System.out.println(Integer.toHexString(28));
System.out.println(Integer.bitCount(28));
System.out.println(Double.parseDouble("3.14"));
System.out.println(Boolean.parseBoolean("false"));
```

```text
a
-123
11100
1c
3
3.14
false
```

| 하고 싶은 일 | 메소드 |
| --- | --- |
| 문자열 → 기본 타입 | `Integer.parseInt("123")` |
| 기본 타입·문자열 → 객체 (박싱) | `Integer.valueOf(123)` |
| 객체 → 기본 타입 (언박싱) | `i.intValue()` |
| 자동 박싱·언박싱 (자바 5+) | `Integer i = 10; int j = i;` |

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 5주차 필기 | 최상위 조상 클래스 = java.lang.class | `java.lang.Object` | 6주차 필기에서도 Object로 정리 |
| 6주차 필기 | Wrapper는 클래스가 아님 | Wrapper는 **클래스** (Integer 등) | `Integer.valueOf(5).getClass()`는 `java.lang.Integer` |
| 중간 시험 메모 | 기본 타입을 객체로 바꾸는 메소드 = parseInt? | `valueOf()`(박싱). `parseInt`는 문자열 → int | 반환 타입이 다름 |
| 중간 대비 노트 | final: 상속 불가·값 변경 불가·오버라이딩 불가를 상수의 성질로 정리 | 변수(값 변경 불가), 메소드(오버라이딩 불가), 클래스(상속 불가)로 대상별 구분 | 적용 대상이 다름 |
| 시험 대비 실습 코드 | `SuperClass.draw()` 안에서 `draw()` 호출 | `super.draw()`를 의도했다면 서브 클래스에 작성 | 슈퍼 클래스 객체에서 실행하면 StackOverflowError |

---

## 최종 핵심 정리

1. 상속은 `extends`로 하며 클래스 다중 상속은 불가, 최상위 조상은 `java.lang.Object`
2. protected는 같은 패키지 전체와 다른 패키지의 자식 클래스에서 접근 가능하다
3. 서브 클래스 객체를 만들면 슈퍼 클래스 생성자가 먼저 실행된다(A → B → C), `super()`는 첫 줄
4. 업캐스팅은 자동, 다운캐스팅은 명시, `instanceof`는 실제 객체 타입 검사
5. 오버라이딩은 매개변수까지 같은 재정의로 동적 바인딩되어 다형성을 만든다. 오버로딩은 매개변수가 달라야 한다
6. 추상 클래스는 일부 구현 + 추상 메소드, 인터페이스는 규격이며 여러 개 구현할 수 있다
7. 계산기 과제는 추상 메소드 5개를 구현해 5, −1, 6, 2, 3.0을 출력했다
8. `java.lang`은 import 없이 쓰고, Wrapper는 기본 타입을 감싸는 클래스이며 박싱은 `valueOf`, 파싱은 `parseInt`

---

End.
