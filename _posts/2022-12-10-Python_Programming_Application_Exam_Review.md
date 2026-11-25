---
title: 04. Python Programming(Application)_Exam Review and Regular Expressions
description: "파이썬 입문·응용 중간·기말 샘플 문제 풀이를 직접 실행해 검증: 변수 규칙, input의 자료형, 인덱싱, 리스트 pop, 중첩 반복, return 없는 함수, split·find·re.sub이 원본을 바꾸지 않는 이유, re.search의 span, 정규표현식 메타문자, NumPy shape, 지도·비지도 학습, 과적합. 문제지 자체의 오류도 정리"
Author: YoungHoon Ko
date: 2022-12-10 18:00:00 +0900
categories: [University(2022-Second-Semester), Python-Programming(Application)]
tags: [python, regex, numpy]
image: /assets/img/lang/py.png
---

## 학습 목표

- 중간고사 샘플 문제로 기본 문법(변수, 자료형, 인덱싱, 리스트, 제어문, 함수) 점검
- 문자열 메서드와 `re` 모듈이 **새 값을 반환**하고 원본은 그대로라는 점
- 정규표현식 메타문자와 문자 클래스
- NumPy 배열의 특징과 `shape`
- 머신러닝의 분류(지도·비지도)와 과적합

모든 답은 직접 실행해서 확인했다. 메타문자 표는 기존 글 "Python-Programming(Application)-(Meta characters)"를 보완한다.

---

## 1. 중간고사 샘플 문제 (파이썬 입문 범위)

| 번호 | 문제 | 답 | 이유 |
| --- | --- | --- | --- |
| 1 | 변수 이름 규칙 중 틀린 것 | b) 대소문자는 구별이 없다 | 파이썬은 `a`와 `A`를 다른 변수로 본다. 한글 변수명도 가능 |
| 2 | `a = input(...)`에 123 입력 후 a의 자료형 | c) string | `input()`은 항상 `str`. 숫자로 쓰려면 `int(a)` |
| 3 | `import math` 후 √2 | d) `math.sqrt(2)` | 모듈 이름을 붙여야 함 |
| 4 | `17 % 3` | b) 2 | 나머지 연산 |
| 5 | `I can't stop.` 출력 | c) `print("I can't stop.")` | 작은따옴표가 들어 있으면 큰따옴표로 감싼다 |
| 6 | `'one fish two fish'[6]` | a) `'s'` | 0부터 세면 o n e (공백) f i **s** |
| 7 | `a=[1,2]; a.append(3); a.pop(2)` | a) `[1, 2]` | `pop(2)`는 **인덱스 2**의 값(3)을 꺼냄 |
| 8 | `x=17`, `x*2 <= 34`면 0, 아니면 +1, 그 뒤 +1 | a) 1 | 34 ≤ 34가 참이라 0이 되고 마지막 줄에서 1 |
| 9 | `range(3)` 안에 `range(2)` | d) 6 | 3 × 2 |
| 10 | `mystery(a)`가 `value = a**3`만 하고 끝남 | **보기에 답 없음** (`None`) | 아래 참고 |

```python
def mystery(a):
    value = a ** 3      # return이 없다

val = 2
val = mystery(val)
print(val)              # None
```

> **(중요!!)** 함수 안에서 계산만 하고 `return`하지 않으면 호출 결과는 `None`이다. 문제의 의도는 `return a**3`(답 8)이었을 것이다.
{: .prompt-danger }

---

## 2. 기말고사 샘플 문제 (파이썬 응용 범위)

| 번호 | 코드 | 반환값 | 비고 |
| --- | --- | --- | --- |
| 1 | `'Hello, World!'.split()` | `['Hello,', 'World!']` | 공백 기준이라 쉼표가 앞 단어에 붙음 → c |
| 2 | `"www.booksr.co.kr".find(".kr")` | `13` | 찾은 위치의 시작 인덱스 → b |
| 3 | `re.sub('[0-9]+', '*', 'My number 2 20 369')` | `'My number * * *'` | `+`는 연속된 숫자 덩어리 하나를 통째로 바꿈 → b |
| 4 | `re.search('AB?', 'X-MAN')` | `span=(3, 4), match='A'` | X(0) -(1) M(2) **A(3)**, `B?`는 0번도 허용 → c |
| 5 | NumPy 설명 중 틀린 것 | d) ndarray는 Python 언어 기반 | ndarray는 **C로 구현**된 연속 메모리 배열이라 리스트보다 빠름 |
| 6 | `np.array([1, 2, 3]).shape` | `(3,)` | 1차원 배열은 원소가 하나인 튜플 → c |
| 8 | 데이터프레임에서 두 열만 | `weather[['평균기온', '평균풍속']]` | 열 이름 **리스트**를 대괄호로 한 번 더 감쌈 |
| 9 | 같은 분류가 아닌 것 | d) 클러스터링 | 나머지는 지도학습, 클러스터링은 비지도학습 |
| 10 | 학습 데이터에만 잘 맞는 현상 | 과적합 | 아래 참고 |

```python
s = 'Hello, World!'
r = s.split()
print(s)   # Hello, World!   ← 원본은 그대로
print(r)   # ['Hello,', 'World!']
```

> 문자열은 **불변(immutable)** 이라 `split`, `find`, `replace`, `re.sub`은 원본을 바꾸지 않고 **새 값을 반환**한다. 결과를 쓰려면 변수에 다시 대입해야 한다.
{: .prompt-warning }

---

## 3. 정규표현식 정리

| 메타문자 | 뜻 | 예 |
| --- | --- | --- |
| `[ ]` | 문자 클래스: 안의 문자 중 하나 | `[0-3][0-9]` → 00~39 |
| `( )` | 그룹 | `a(bc)+` → abc, abcbc, … |
| `\|` | OR | `a(b\|c)` → ab, ac |
| `.` | 줄바꿈을 뺀 아무 문자 하나 | `a.b` → aab, a9b, a&b |
| `^`, `$` | 문자열의 시작, 끝 (앵커) | `^meta`, `py$` |
| `*`, `+`, `?` | 0번 이상, 1번 이상, 0 또는 1번 | `ab(cd)?` → ab, abcd |
| `{n}` | 정확히 n번 | `abc{3}` → abccc (`{3}`은 바로 앞 c에만 적용) |

| 이스케이프 | 같은 뜻 |
| --- | --- |
| `\d` / `\D` | `[0-9]` / 숫자가 아닌 문자 |
| `\w` / `\W` | `[a-zA-Z0-9_]` / 그 밖의 문자 |
| `\s` / `\S` | 공백·탭·줄바꿈 / 공백이 아닌 문자 |

| 함수 | 반환 |
| --- | --- |
| `re.search(p, s)` | 처음 매칭되는 위치의 `Match` 객체, 없으면 `None` |
| `re.findall(p, s)` | 매칭된 문자열의 리스트 |
| `re.split(p, s)` | 패턴으로 나눈 리스트 |
| `re.sub(p, repl, s)` | 치환한 **새 문자열** |

```python
import re
print(re.findall(r'\d+', 'My number 2 20 369'))     # ['2', '20', '369']
print(re.findall(r'a(?:b|c)+', 'abcb ac ad'))        # ['abcb', 'ac']
```

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 중간 샘플 10번 | `mystery`에 return 없음, 보기 2, 4, 6, 8 | 실행 결과는 `None`, 의도대로라면 `return a**3` 추가 → 8 | return이 없으면 함수 값은 None |
| 기말 샘플 1~3번 | "실행한 후 s에 들어 있는 결과는?" | "식의 결과(반환값)는?" | `s.split()` 등은 s를 바꾸지 않아 s는 원래 문자열 그대로 |
| 기말 샘플 10번 | 보기 b) 과적합, c) 과대적합 | 둘은 같은 말(overfitting) | 정답이 두 개가 됨. 반대 개념은 과소적합(underfitting) |
| 중간 샘플 1번 보기 | 밑즐(_) | 밑줄(_) | 오타 |
| 기말 샘플 9번 보기 | 판단트리, 랜텀포레스트 | 결정트리, 랜덤포레스트 | 오타 |
| 정규표현식 슬라이드 10.2 | Exception Handing | Exception Handling | 철자 오류 |

---

## 최종 핵심 정리

1. 파이썬 변수는 대소문자를 구별하고, input()은 항상 문자열을 반환한다
2. 인덱스는 0부터 세며, list.pop(i)는 인덱스 i의 원소를 꺼낸다
3. return이 없는 함수의 호출 결과는 None이다
4. 문자열은 불변이라 split, find, re.sub은 원본을 바꾸지 않고 새 값을 반환한다
5. 정규표현식의 +는 1번 이상, ?는 0 또는 1번, {n}은 바로 앞 글자의 n번 반복이다
6. re.search는 처음 매칭된 위치의 Match 객체를, findall은 모든 매칭의 리스트를 반환한다
7. ndarray는 C로 구현된 같은 자료형의 연속 배열이라 리스트보다 빠르고, 1차원의 shape은 (n,)이다
8. 클러스터링은 비지도학습이고, 학습 데이터에만 잘 맞는 현상을 과적합이라 한다

---

End.
