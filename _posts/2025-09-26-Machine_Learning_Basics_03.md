---
title: 03. Machine Learning Basics_Vectors, Norms and NumPy Practice
description: "머신러닝 실습(9/16, 9/23): NumPy 배열로 벡터·행렬 만들기, 내적, L1·L2 노름을 함수와 직접 계산으로 비교, 슬라이싱, 원소별 곱을 반복문과 벡터 연산으로 비교, SymPy 기호 변수. 모든 코드를 다시 실행해 출력 확인"
Author: YoungHoon Ko
date: 2025-09-26 18:00:00 +0900
categories: [University(2025-Second-Semester), Machine Learning Basics]
tags: [machine-learning, python, numpy]
math: true
image: /assets/img/lang/ml.png
---

## 학습 목표

- 특징 벡터를 NumPy 배열로 표현하기
- 벡터 내적의 계산과 의미
- L1 노름(맨해튼), L2 노름(유클리드)의 정의와 계산
- 1차원·2차원 배열 슬라이싱 규칙
- 반복문 대신 벡터 연산(브로드캐스팅)을 쓰는 이유
- SymPy로 기호 수학 다루기

---

## 1. 벡터와 행렬

- **스칼라**는 크기만, **벡터**는 크기와 방향을 가진다
- 행렬은 수를 행과 열로 배열한 2차원 구조이고, A_ij는 i행 j열의 스칼라 원소다
- 머신러닝에서 샘플 하나의 특징 벡터는 1차원 배열, 데이터 전체는 (샘플 수, 특징 수) 모양의 2차원 배열이 된다

```python
import numpy as np

a = np.array([1, 2, 3, 4, 5])                 # 1차원 벡터
b = np.array([-1.2, 123, 1243])               # 실수 벡터
c = np.array([[3, 1], [5, 3], [2, 3]])        # 3 x 2 행렬
print(a); print(b); print(c)
```

실행 결과

```text
[1 2 3 4 5]
[-1.200e+00  1.230e+02  1.243e+03]
[[3 1]
 [5 3]
 [2 3]]
```

- 배열 안의 자료형은 하나로 통일된다. `b`는 실수 배열이라 지수 표기로 출력됐다

---

## 2. 내적 (Dot Product)

$$\mathbf{a} \cdot \mathbf{b} = \sum_{i=1}^{n} a_i b_i$$

```python
a = np.array([2, -1, 3])
b = np.array([1, 2, -1])
print(np.dot(a, b))        # 2 - 2 - 3

at = np.array([-1, 1, -1])
bt = np.array([1, -1, 1])
print(np.dot(at, bt))      # -1 - 1 - 1
```

실행 결과

```text
-3
-3
```

- 내적의 결과는 벡터가 아니라 **스칼라**다
- 서로 다른 벡터 쌍이어도 내적 값은 같을 수 있다(수업 메모). 내적은 크기와 사이각을 함께 담기 때문: 두 번째 쌍은 bt = −at라 정반대 방향이고, 크기의 곱이 3이라서 −3이 된다
- 내적이 0이면 두 벡터는 수직이다. 뒤에서 배우는 퍼셉트론의 가중합 Σ w_i x_i도 내적이다

---

## 3. 노름 (Norm)

벡터의 **크기**를 재는 방법이다.

$$\lVert \mathbf{a} \rVert_1 = \sum_i \lvert a_i \rvert, \qquad \lVert \mathbf{a} \rVert_2 = \sqrt{\sum_i a_i^2}$$

| 노름 | 이름 | 기하학적 의미 |
| --- | --- | --- |
| L1 | 맨해튼 거리 | 축을 따라 이동한 거리의 합 (격자 도로) |
| L2 | 유클리드 거리 | 시작점과 끝점을 잇는 직선 거리 |

```python
an = np.array([2, 2, -2, -2])
print(np.linalg.norm(an, 1))   # |2|+|2|+|-2|+|-2|
print(np.linalg.norm(an))      # sqrt(4+4+4+4), 기본값이 L2
```

실행 결과

```text
8.0
4.0
```

9/23 실습은 난수 100개로 라이브러리 함수와 직접 계산이 같은지 확인했다.

```python
import math
np.random.seed(1024)
values = np.random.rand(100)

print(np.linalg.norm(values, 1), sum(abs(values)))
print(np.linalg.norm(values, 2), math.sqrt(sum(abs(values)**2)))
```

실행 결과

```text
54.402564725568055 54.40256472556806
6.220987851745569 6.22098785174557
```

- 마지막 자리만 다른 것은 부동소수점 덧셈 순서 차이다. 비교할 때는 `np.isclose`를 쓴다
- 노름은 항상 0 이상이고, 두 벡터 사이 거리는 ‖a − b‖로 잰다. kNN의 유클리드 거리, 선형 회귀의 제곱 오차(L2)가 모두 여기서 나온다

---

## 4. 슬라이싱

```python
scores = np.array([28, 72, 91, 94, 89, 75, 99])
print(scores[3:])      # 인덱스 3부터 끝까지
print(scores[4:-1])    # 4부터 마지막 원소 직전까지
print(scores[-3:])     # 뒤에서 3개

mt = np.array([[1, 2, 3, 100],
               [4, 5, 6, 101],
               [7, 8, 9, 102],
               [10, 11, 12, 103]])
print(mt[0:2, 2:3])    # 0~1행, 2열
```

실행 결과

```text
[94 89 75 99]
[89 75]
[89 75 99]
[[3]
 [6]]
```

- 끝 인덱스는 **포함되지 않는다**
- `mt[0:2, 2:3]`은 모양이 (2, 1)인 2차원 배열이고, `mt[0:2, 2]`는 (2,) 모양의 1차원 배열 `[3 6]`이다. sklearn에 넣을 특징 행렬은 2차원이어야 해서 이 차이가 중요하다(선형 회귀 글 참고)

---

## 5. 원소별 곱: 반복문 vs 벡터 연산

```python
np.random.seed(1025)
values2 = np.random.rand(100)

# 1) 반복문
weighted_values = np.empty(len(values))
for i in range(len(values)):
    weighted_values[i] = values2[i] * values[i]

# 2) 벡터 연산
rst = values * values2

print(np.allclose(rst, weighted_values))
```

실행 결과

```text
True
```

- 같은 모양의 배열끼리 `*`, `+`는 **원소별**로 계산된다. 반복문보다 코드가 짧고, C로 구현된 내부 루프를 써서 훨씬 빠르다
- 행렬 곱은 `*`가 아니라 `@` 또는 `np.dot`

---

## 6. SymPy: 기호 수학

```python
from sympy import symbols, sqrt, simplify

z = symbols('z')
print(simplify(sqrt(z**2)))

z = symbols('z', real=True)
print(simplify(sqrt(z**2)))
```

실행 결과

```text
sqrt(z**2)
Abs(z)
```

- 실습 코드 주석에는 결과가 z의 제곱처럼 적혀 있었지만, 실제로는 **그대로 sqrt(z**2)** 로 남는다. z가 복소수일 수도 있어서 SymPy가 단순화하지 않기 때문이다
- `real=True`로 실수라고 알려 주면 `|z|`가 된다

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 9/16 실습 주석 | `simplify(sqrt(z**2))` → "x의 제곱" | `sqrt(z**2)` 그대로 (실수 가정 시 `Abs(z)`) | 실제 실행 결과 |
| 9/16 실습 | `z.is_integer`만 쓰고 출력 안 함 | `print(z.is_integer)` → True | 스크립트에서는 값이 화면에 안 나옴 |
| 4주차 필기 | 행렬은 "스칼라 값이나 자려를" | 스칼라 값을 행과 열로 배열한 것 | 오타 |

---

## 최종 핵심 정리

1. 특징 벡터는 1차원 배열, 데이터 전체는 (샘플 수, 특징 수) 모양의 2차원 배열로 표현한다
2. 내적 Σ a_i b_i는 스칼라이고, 크기와 사이각을 함께 담아 서로 다른 벡터 쌍도 같은 값을 줄 수 있다
3. L1 노름은 절댓값의 합(맨해튼), L2 노름은 제곱합의 제곱근(유클리드)
4. [2, 2, −2, −2]의 L1 = 8, L2 = 4
5. 라이브러리와 직접 계산 결과는 부동소수점 마지막 자리만 달랐다 → `np.isclose`로 비교
6. 슬라이싱의 끝 인덱스는 포함되지 않고, `mt[0:2, 2:3]`(2차원)과 `mt[0:2, 2]`(1차원)는 모양이 다르다
7. 원소별 연산은 반복문 대신 `values * values2`로 쓰면 짧고 빠르며, 행렬 곱은 `@`
8. SymPy의 `sqrt(z**2)`는 실수 가정이 있어야 `Abs(z)`로 단순화된다

---

End.
