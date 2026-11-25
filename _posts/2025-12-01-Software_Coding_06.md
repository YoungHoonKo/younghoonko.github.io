---
title: 06. Software Coding_Lists, Dictionaries, Strings, Functions, HW7 and Project
description: "소프트웨어코딩 및 실습 7~9장과 과제 7, 개별 프로젝트: 리스트 슬라이싱과 in, 딕셔너리 items, 문자열 슬라이싱과 count·find·split, 함수의 매개변수·반환값·기본값, 최솟값 함수(기말 출제), 로또 번호 추첨 프로그램 검토"
Author: YoungHoon Ko
date: 2025-12-01 15:30:00 +0900
categories: [University(2025-Second-Semester), Software Coding]
tags: [python]
image: /assets/img/lang/py.png
---

## 학습 목표

- 리스트 만들기, 인덱싱, 슬라이싱(음수 인덱스 포함)
- 리스트와 `if ... in`, `for`
- 딕셔너리의 키·값과 `items()`
- 문자열 슬라이싱과 `count`, `index`, `find`, `startswith`, `endswith`, `split`
- 함수 정의, 매개변수 기본값, 여러 값 반환
- 과제 7(리스트 슬라이싱), 개별 프로젝트(로또 추첨)

---

## 1. 리스트

여러 데이터를 **순서대로** 한 묶음으로 관리하는 자료형. 대괄호와 쉼표로 만든다.

```python
x = [10, 20, 30]
fruits = ['apple', 'banana', 'kiwi']
if 'apple' in fruits:
    print('apple found')
```

### 슬라이싱 규칙

`리스트[시작:끝]`은 시작부터 **끝 − 1**까지. 비우면 처음(0) 또는 끝까지.

| 인덱스 | 0 | 1 | 2 | 3 | 4 |
| --- | --- | --- | --- | --- | --- |
| 값 | 10 | 20 | 30 | 40 | 50 |
| 음수 인덱스 | −5 | −4 | −3 | −2 | −1 |

### 과제 7: 리스트 슬라이싱

> **과제 7**: (1) 처음 세 개 (2) 인덱스 2부터 4까지 (3) 인덱스 2부터 끝까지 (4) 첫 번째와 마지막을 뺀 나머지 (5) 음수 인덱스로 마지막 두 개를 슬라이싱하라.
{: .prompt-info }

```python
a = [10, 20, 30, 40, 50]
print(a[:3])                       # (1)

a = [1, 2, 3, 4, 5, 6]
print(a[2:5])                      # (2) 끝은 4 + 1

a = [5, 10, 15, 20, 25]
print(a[2:])                       # (3)

nums = [100, 200, 300, 400, 500, 600]
print(nums[1:5])                   # (4)

a = [10, 20, 30, 40, 50]
print(a[-2:])                      # (5)
```

실행 결과

```text
[10, 20, 30]
[3, 4, 5]
[15, 20, 25]
[200, 300, 400, 500]
[40, 50]
```

- (2) "4까지"는 끝 인덱스에 **5**를 써야 한다
- (4)의 `nums[1:5]`는 길이가 6일 때만 맞다. `nums[1:-1]`로 쓰면 길이와 상관없이 첫·마지막을 뺀다(같은 결과)

기말 예제: `a = list(i for i in range(1, 6))` → [1, 2, 3, 4, 5], `a[-4:-1]` = [2, 3, 4]

---

## 2. 딕셔너리

**키(key)와 값(value)의 쌍**. 키는 중복될 수 없고, 키로 값을 찾는다.

```python
students = {"Alice": 20, "Bob": 22, "Charlie": 19}
print(students["Alice"])                # 20

for name in students.keys():
    print(f"{name}'s age is {students[name]}.")

for k, v in students.items():          # 키와 값을 함께
    print(k, v)
```

- 추가·수정: `phone["Charlie"] = "..."`, 삭제: `del phone["Bob"]`
- 리스트 안의 리스트로 표를 만들 수도 있다. 10주차 실습은 `[이름, 학년, 학번]` 목록에서 2학년만 골라 이름과 번호를 출력했다(Alice, Eve)

---

## 3. 문자열

문자열도 리스트처럼 인덱싱·슬라이싱이 된다(단, 바꿀 수는 없다).

```python
text = "Hello, world!"
print(text[0:5])           # Hello
print("python"[:4])        # pyth
print("Data Science"[-7:-1])
print("Data Science"[-7:])

text = "banana"
print(text.count("a"))     # 3
print(text.index("n"))     # 2  처음 나오는 위치
print(text.find("x"))      # -1 없으면 -1 (index는 오류)
print(text.startswith("b"))
print("Hello.pdf".endswith(".pdf"))
print("a, b, c".split(", "))
```

실행 결과

```text
Hello
pyth
Scienc
Science
3
2
-1
True
True
['a', 'b', 'c']
```

- `"Data Science"[-7:-1]`은 **"Scienc"** 이다. 끝 인덱스 −1(마지막 글자 e)은 포함되지 않는다. 실습 주석의 "Science"는 `[-7:]`의 결과
- `find`는 못 찾으면 −1, `index`는 `ValueError`

---

## 4. 함수

```python
def 함수이름(매개변수):
    실행할 문장
    return 결과
```

### 11주차·기말 예제

```python
def ab(a):                          # (a + 1)^2
    return a**2 + 2*a + 1

def c_to_f(c):                      # 섭씨 → 화씨
    return c * 9/5 + 32

def sumList(numb):
    total = 0
    for k in numb:
        total += k
    return total

def avg(numb):
    return sumList(numb) / len(numb)   # 함수 안에서 다른 함수 호출

def power(m, exp=2):                # 기본값 매개변수
    return m ** exp

def calc(a, b):                     # 값 두 개 반환
    if a > b:
        return a - b, True
    else:
        return -1, False

print(ab(3), c_to_f(30))
print(sumList([10, 20, 30, 50, 100, 70, 100, 50]), avg([10, 20, 30, 50, 100, 70, 100, 50]))
print(power(2, 4), power(4))
print(calc(5, 3), calc(5, 10))
```

실행 결과

```text
16 86.0
430 53.75
16 16
(2, True) (-1, False)
```

- 기본값이 있는 매개변수는 생략할 수 있다. `power(4)`는 `power(4, 2)`
- `return a, b`는 튜플 하나를 돌려주고, `m, n = calc(5, 3)`처럼 나눠 받는다

### 최솟값 함수 (기말 출제)

> **(중요!!)** "최소값 구하는 함수 - 기말고사 출제" 표시
{: .prompt-warning }

```python
def find_min(val):
    min_val = val[0]            # 첫 원소로 시작
    for k in val:
        if k < min_val:
            min_val = k
    return min_val

print(find_min([65, 85, 50, 95, 80]))
```

실행 결과

```text
50
```

- 초기값을 0으로 두면 모든 값이 양수일 때 답이 0이 되는 오류가 생긴다. 반드시 **첫 원소**로 시작
- 빈 리스트를 넣으면 `val[0]`에서 `IndexError`가 난다. 내장 함수 `min()`은 빈 리스트에 `ValueError`를 낸다
- 최댓값은 부등호만 `>`로 바꾸면 된다

---

## 5. 개별 프로젝트: 로또 번호 추첨

> **개별 프로젝트**: 지금까지 배운 함수, 조건문, 반복문, 리스트로 3가지 기능이 있는 로또 번호 자동 추첨 프로그램을 만들어라.
{: .prompt-info }

구성

| 함수 | 역할 |
| --- | --- |
| `generate_lotto_numbers(count)` | 1~45에서 중복 없이 count개를 뽑아 정렬 |
| `generate_bonus_number(main_numbers)` | 본 번호와 겹치지 않는 보너스 1개 |
| `print_one_game(game_no, numbers, bonus)` | `%2d`로 두 자리 맞춰 출력 |
| `print_history(history)` | 지금까지 뽑은 기록 전체 출력 |
| `auto_one_game(history)` | 1게임 추첨 + 기록 저장 |

메뉴: 1. 자동 1게임 추첨 / 2. 추첨 기록 보기 / 3. 종료

```python
def generate_lotto_numbers(count):
    numbers = []
    while len(numbers) < count:
        num = random.randrange(1, 46)    # 1 ~ 45
        if num not in numbers:           # 중복이면 버림
            numbers.append(num)
    numbers.sort()
    return numbers
```

실행 결과 (시드 고정, 입력 2 → 1 → 1 → 2 → 9 → 3)

```text
번호 입력 >> 아직 추첨한 기록이 없습니다.

[자동 1게임 추첨 결과]
1게임:  6 12 31 34 36 42 +  [보너스  1]

[자동 1게임 추첨 결과]
1게임:  5 15 24 25 26 37 +  [보너스  7]

====== 지금까지 추첨된 전체 기록 ======
1게임:  6 12 31 34 36 42 +  [보너스  1]
2게임:  5 15 24 25 26 37 +  [보너스  7]
=======================================

번호 입력 >> 잘못된 입력입니다. 1~3 중에서 선택해 주세요.

프로그램을 종료합니다. 이용해 주셔서 감사합니다.
```

검토하면서 확인한 점

- 기록이 없을 때, 잘못된 메뉴 입력, 종료가 모두 의도대로 동작한다
- 두 번째 추첨 결과도 "1게임"으로 표시된다. `auto_one_game`이 항상 `print_one_game(1, ...)`을 부르기 때문이라 `len(history) + 1`을 넘기면 기록 번호와 맞는다
- 파일 맨 위 주석에는 "기능 2: 자동 N게임 추첨"을 포함한 4가지 기능이 적혀 있지만, 실제 메뉴에는 N게임 추첨이 없다. 주석을 실제 기능(1게임, 기록, 종료)에 맞추거나, `auto_one_game`을 N번 부르는 메뉴를 추가하면 된다
- 중복을 피하는 `while` + `not in`은 배운 문법만으로 잘 해결한 방법이다. 표준 라이브러리로는 `random.sample(range(1, 46), 6)` 한 줄로 같은 일을 한다

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 10주차 실습 주석 | `"Data Science"[-7:-1]` → Science | Scienc | 끝 인덱스는 포함 안 됨 (실행 확인) |
| 10주차 실습 주석 | `endswith` 결과 "Tru" | True | 오타 |
| 개별 프로젝트 주석 | 기능 4가지(N게임 포함) | 실제는 3가지 메뉴 | 코드와 불일치 |
| 개별 프로젝트 출력 | 매번 "1게임" | 기록 순번 표시 | 게임 번호를 1로 고정해 호출 |

---

## 최종 핵심 정리

1. 리스트 슬라이싱 `[시작:끝]`은 끝 − 1까지이고, 음수 인덱스는 뒤에서 −1부터 센다
2. "인덱스 2부터 4까지"는 `[2:5]`, "첫·마지막 제외"는 `[1:-1]`이 길이에 상관없이 맞다
3. `in`으로 포함 여부를 검사하고, `for`로 원소를 하나씩 읽는다
4. 딕셔너리는 중복 없는 키로 값을 찾고, `items()`로 키와 값을 함께 꺼낸다
5. 문자열도 슬라이싱되며 `"Data Science"[-7:-1]`은 "Scienc", `find`는 없으면 −1
6. 함수는 매개변수 기본값(`exp=2`)과 여러 값 반환(`return a, b`)을 지원한다
7. 최솟값 함수는 첫 원소로 초기화해야 하고, 기말에 출제됐다
8. 로또 프로젝트는 중복 없는 추첨과 기록 기능이 잘 동작하며, 게임 번호 표시와 주석 불일치가 개선점이다

---

End.
