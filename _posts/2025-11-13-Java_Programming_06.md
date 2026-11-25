---
title: 06. Java Programming_Collections, Generics, I/O Streams, Threads, HW6 and HW7
description: "자바 프로그래밍 및 실습 7·8·13장과 과제 6, 7: Vector의 용량 증가, ArrayList, HashMap, Iterator, 자동 박싱, 제네릭 클래스·메소드와 직접 만든 스택, 해시맵 인구 검색, 바이트·문자 스트림과 파일 경로 문제, 스레드, 단어 검색 과제"
Author: YoungHoon Ko
date: 2025-11-13 12:00:00 +0900
categories: [University(2025-Second-Semester), Java Programming]
tags: [java]
image: /assets/img/lang/java.png
---

## 학습 목표

- 컬렉션과 제네릭의 개념, 컬렉션에 기본 타입을 넣는 방법(자동 박싱)
- `Vector`, `ArrayList`, `LinkedList`, `HashMap`, `Iterator` 사용법
- 제네릭 클래스와 제네릭 메소드 작성
- 과제 6(7장 연습문제, 나라별 인구 해시맵)
- 바이트 스트림과 문자 스트림, 파일 입출력
- 스레드 만들기
- 과제 7(파일에서 읽은 단어 검색)

---

## 1. 컬렉션과 제네릭

- **컬렉션**: 객체들을 담는 가변 크기 저장소(자료구조). 크기를 신경 쓰지 않고 삽입·삭제할 수 있다
- 컬렉션에는 **객체만** 들어간다. `int` 같은 기본 타입은 `Integer` 같은 Wrapper 객체로 **자동 박싱**되어 들어간다
- 컬렉션은 **제네릭**으로 만들어져 있다: `Vector<E>`의 `E`는 타입 매개변수, `Vector<Integer>`처럼 실제 타입을 정하는 것을 구체화라고 한다. C++에서는 템플릿이라 부른다
- 제네릭 클래스의 배열(`new Vector<Integer>[10]`)은 만들 수 없다

### Vector

```java
Vector<Integer> v = new Vector<>();
v.add(5);
v.add(4);
v.add(-1);
v.add(2, 100);           // 인덱스 2에 삽입 → 뒤 원소가 밀림
System.out.println("벡터 내 요소 개수: " + v.size());
System.out.println("벡터의 현재 용량: " + v.capacity());
```

```text
벡터 내 요소 개수: 4
벡터의 현재 용량: 10
0 : 5
1 : 4
2 : 100
3 : -1
```

- `size()`는 원소 수, `capacity()`는 확보된 공간. 기본 용량은 10
- 연습문제 7번: `new Vector<Integer>(3)`에 10개를 넣으면 용량이 **3 → 6 → 12**로 두 배씩 늘어난다(직접 실행해 원소를 넣을 때마다 `3 3 3 6 6 6 12 12 12 12` 확인). 그래서 연습문제 3번의 "30개만 저장할 수 있다"는 틀린 설명

### Iterator

```java
Iterator<Integer> it = v.iterator();
while (it.hasNext())
    System.out.println(it.next());
```

인덱스가 없는 컬렉션(Set 등)도 같은 방식으로 순회할 수 있다.

### HashMap

```java
HashMap<String, String> dic = new HashMap<>();
dic.put("baby", "아기");
dic.put("love", "사랑");
dic.put("apple", "사과");
String kor = dic.get(eng);       // 없으면 null
```

입력 love → 사랑, baby → 아기, hello → `hello는 없는 단어입니다.`

- 키로 값을 찾으므로 **순서가 없다**. 점수 예제를 출력하면 넣은 순서(김은비, 하여린, 전아린, …)가 아니라 해시 순서(김은비, 하여린, 양승연, 전아린, 이동건)로 나왔다

### LinkedList

이중 연결 리스트로, 넣은 **순서를 그대로 유지**한다. "c", "a", "b"를 넣고 출력하면 `[c, a, b]`

---

## 2. 제네릭 클래스와 메소드

### 직접 만든 제네릭 스택

```java
class GStack<T> {
    int tos;
    Object[] stck;
    public GStack() { tos = 0; stck = new Object[10]; }
    public void push(T item) {
        if (tos == 10) return;           // 가득 참
        stck[tos] = item;
        tos++;
    }
    public T pop() {
        if (tos == 0) return null;       // 비었음
        tos--;
        return (T) stck[tos];
    }
}
```

"Seoul", "Busan", "LA"를 넣고 꺼내면 `LA, Busan, Seoul`(후입선출), 1, 3, 5는 `5, 3, 1`

- `T[]` 배열은 직접 만들 수 없어서 `Object[]`에 저장하고 꺼낼 때 `(T)`로 바꾼다. 컴파일하면 unchecked 경고가 나는 이유
- 가득 찼을 때 조용히 무시하므로 11번째 push는 사라진다. 예외를 던지는 편이 실수를 찾기 쉽다

### 제네릭 메소드: 스택 뒤집기

```java
public static <T> GStack<T> reverse(GStack<T> a) {
    GStack<T> s = new GStack<T>();
    while (true) {
        T tmp = a.pop();
        if (tmp == null) break;
        else s.push(tmp);
    }
    return s;
}
```

0.0~4.0을 넣고 뒤집은 뒤 꺼내면 `0.0, 1.0, 2.0, 3.0, 4.0`(넣은 순서대로). 원래 스택은 텅 빈다. `pop()`이 null로 "비었음"을 알리므로 null을 원소로 넣으면 중간에 멈춘다는 한계가 있다.

---

## 3. 과제 6: 7장 연습문제 + 실습

> **과제 6**: 교재 7장 연습문제(p.445~448)와 실습 문제(나라 5개의 인구를 해시맵에 저장하고 인구가 가장 많은 나라 찾기)
{: .prompt-info }

### 연습문제 요약

| 번호 | 답 |
| --- | --- |
| 1 | ③ 기본 타입도 Wrapper로 자동 박싱되어 저장 가능 |
| 2 | 일반화, 제네릭, 타입 매개변수, 구체화, 템플릿 |
| 3 | ② 30은 초기 용량일 뿐 |
| 5 | ① `Stack<String> ss;` (`HashMap`은 타입 2개, `Set`은 인터페이스라 new 불가) |
| 6 | `v.add(3.14)` 박싱, `double d = v.get(0)` 언박싱 |
| 7 | 용량 12 (두 배씩 증가) |
| 8 | `size()` = 2, 마지막 원소 삭제 후 `[C++]` (실행 확인) |
| 10 | `return h.size();` |

### 11번: 제네릭 클래스 JGeneric

```java
class JGeneric<W> {
    private W x, y;
    public JGeneric(W x, W y) { this.x = x; this.y = y; }
    public W first() { return x; }
    public W second() { return y; }
    public boolean equal() { return x.equals(y); }
}

JGeneric<String> jg = new JGeneric<>("Hello", "Helliii");
System.out.println(jg.first() + " " + jg.second() + " " + jg.equal());
```

```text
Hello Helliii false
```

- 문자열 비교는 `==`(같은 객체인지)가 아니라 `equals()`(내용이 같은지)로 한다

### 12번: 제네릭 메소드로 바꾸기

원래 코드의 범위 검사는 `if (index > s.length)`였다. 이러면 `index == s.length`일 때 검사를 통과해 `ArrayIndexOutOfBoundsException`이 난다. 제출 답은 `index < 0 || index >= s.length`로 고쳐 두 경우를 모두 막았다.

```java
static <T> T take(T[] s, int index) {
    if (index < 0 || index >= s.length) {
        System.out.println("인덱스가 범위를 벗어났습니다.");
        return null;
    }
    return s[index];
}
```

`take(new Integer[]{1, 2, 3}, 2)` → 3, `take(new String[]{"a"}, 1)` → 메시지 후 null

### 실습: 가장 인구가 많은 나라

```java
HashMap<String, Integer> nations = new HashMap<>();
for (int i = 0; i < 5; i++) {
    String country = sc.next();
    int population = sc.nextInt();
    nations.put(country, population);
}

String maxCountry = null;
int maxPop = -1;
for (Map.Entry<String, Integer> e : nations.entrySet()) {
    if (e.getValue() > maxPop) {
        maxPop = e.getValue();
        maxCountry = e.getKey();
    }
}
System.out.printf("제일 인구가 많은 나라는 (%s, %d)\n", maxCountry, maxPop);
```

입력 `Korea 5100 / USA 33500 / Japan 12400 / China 140900 / India 142800` (단위 만 명)

```text
제일 인구가 많은 나라는 (India, 142800)
```

- `entrySet()`으로 키와 값을 함께 순회했다
- 같은 나라를 두 번 입력하면 `put`이 값을 덮어써서 나라가 4개가 된다. 해시맵의 키는 중복될 수 없기 때문

---

## 4. 입출력 스트림

| 구분 | 단위 | 최상위 클래스 | 예 |
| --- | --- | --- | --- |
| 바이트 스트림 | 바이트 | `InputStream` / `OutputStream` | `FileInputStream`, `FileOutputStream` |
| 문자 스트림 | 문자(유니코드 2바이트) | `Reader` / `Writer` | `FileReader`, `BufferedReader` |

- 스트림 클래스 이름은 모두 Stream, Reader, Writer로 끝난다(시험 문제)

### 파일 경로 함정

```java
FileOutputStream fout = new FileOutputStream("c:\\Temp\\test.out");
```

이 실습을 macOS에서 실행했더니 "저장했습니다."가 나왔지만, 원하는 `C:\Temp` 폴더가 아니라 **실행 폴더에 `c:\Temp\test.out`이라는 이름의 파일**이 하나 생겼다(실제로 과제 폴더 `src` 안에 그 이름의 파일이 남아 있었다). macOS와 리눅스는 경로 구분자가 `/`이고 `\`를 파일 이름의 일부로 보기 때문이다. 같은 이유로 `FileReader("c:\\windows\\system.ini")`는 "입출력 오류"가 났다.

운영체제와 상관없이 쓰려면

```java
File f = new File(System.getProperty("java.io.tmpdir"), "test.out");
// 또는 java.nio.file.Paths.get("Temp", "test.out")
```

처럼 구분자를 직접 쓰지 않는다.

---

## 5. 스레드

```java
class TimerThread extends Thread {
    int n = 0;
    public void run() {
        while (true) {
            System.out.println(n++);
            try { sleep(1000); }
            catch (InterruptedException e) { return; }
        }
    }
}
new TimerThread().start();     // run()이 아니라 start()
```

- `start()`를 불러야 새 스레드에서 `run()`이 실행된다. `run()`을 직접 부르면 그냥 메소드 호출이다
- 하나의 자바 프로그램은 **여러 스레드**로 구성될 수 있다(main 스레드 + 가비지 컬렉터 등). "하나의 스레드로만 구성된다"는 틀린 말(시험)
- main이 끝나도 이 타이머 스레드가 살아 있어서 프로그램이 종료되지 않는다

---

## 6. 과제 7: 파일 단어 검색 (13장 실습 6)

> **과제 7**: `words.txt`의 단어를 `Vector<String>`에 읽어 두고, 사용자가 입력한 문자열로 **시작하는** 단어를 모두 출력하라. exit이면 종료
{: .prompt-info }

```java
Vector<String> words = new Vector<>();
try (BufferedReader br = new BufferedReader(new FileReader("words.txt"))) {
    String line;
    while ((line = br.readLine()) != null)
        words.add(line.trim());
} catch (IOException e) {
    System.out.println("words.txt 파일을 읽는 중 오류가 발생했습니다.");
    return;
}

while (true) {
    System.out.print("단어>>");
    String input = scanner.nextLine().trim();
    if (input.equalsIgnoreCase("exit")) { System.out.println("종료합니다..."); break; }
    boolean found = false;
    for (String word : words)
        if (word.startsWith(input)) { System.out.println(word); found = true; }
    if (!found) System.out.println("발견할 수 없음");
}
```

실행 결과 (단어 18개짜리 words.txt)

```text
단어>>blueb
blueback
blueberry
bluebill
bluebird
bluebonnet
bluebook
bluebush
단어>>moonl
moonlight
moonlit
단어>>xyz
발견할 수 없음
단어>>Blue
발견할 수 없음
단어>>exit
종료합니다...
```

- try-with-resources로 파일을 자동으로 닫는다
- `exit`는 대소문자를 무시하지만 검색은 `startsWith`라 **대소문자를 구분**한다. "Blue"는 찾지 못했다. 검색도 무시하려면 `word.toLowerCase().startsWith(input.toLowerCase())`
- 빈 줄(Enter만)을 입력하면 빈 문자열로 모든 단어가 시작하므로 18개가 전부 출력된다

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 8주차 필기 | LinkedList 정렬 순서는 오름차순 | 넣은 순서 유지 | `[c, a, b]` 실행 확인 |
| 8주차 필기 | "이터레이터 객체를 만들 때만 이터레이터 사용" | `iterator()`로 얻은 Iterator로 순회 | 표현 정리 |
| 11/4 실습 | `"c:\\Temp\\test.out"` | OS 독립 경로 사용 | macOS에서 파일 이름이 `c:\Temp\test.out`인 파일이 생김 |
| 연습문제 12번 원래 코드 | `index > s.length` | `index < 0 \|\| index >= s.length` | `index == length`에서 예외 |

---

## 최종 핵심 정리

1. 컬렉션은 객체만 담는 가변 저장소이고 기본 타입은 자동 박싱으로 들어간다
2. Vector의 기본 용량은 10, 모자라면 두 배로 늘어난다(3 → 6 → 12)
3. HashMap은 키로 값을 찾아 순서가 없고, LinkedList는 넣은 순서를 유지한다
4. 제네릭은 타입을 매개변수로 일반화하는 기술이고, `T[]` 대신 `Object[]`에 저장하고 꺼낼 때 형 변환한다
5. 문자열 내용 비교는 `equals()`, 배열 범위 검사는 `index < 0 || index >= length`
6. 바이트 스트림의 최상위는 InputStream/OutputStream, 문자 스트림은 Reader/Writer
7. `\`가 들어간 윈도우 경로는 macOS에서 파일 이름이 되므로 OS 독립 경로를 써야 한다
8. 스레드는 `start()`로 실행하고, 단어 검색 과제는 접두어 검색이 대소문자를 구분한다는 점이 개선점

---

End.
