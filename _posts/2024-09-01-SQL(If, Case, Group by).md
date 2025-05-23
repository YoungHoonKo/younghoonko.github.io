---
title: 06. SQL(If, Case, Group by)
Author: YoungHoon Ko
date: 2024-09-01 10:50:00 +0900
categories: [SQL, Practice_SQL]
tags: [sql, basic, sql_p]
image: /assets/img/lang/sql.png
---

[toc]

# 분기문과 집계문

이번 학습에서는 SQL에서 조건 분기 처리를 위한 `IF`, `CASE`, 그리고 데이터를 그룹화하여 집계하는 `GROUP BY` 문법을 실습하였다.

---

## 1. IF, CASE

### ✅ 테이블 생성 및 데이터 입력

```sql
CREATE table class.exam (
  id int auto_increment primary key,
  name varchar(100),
  math int,
  english int
);

INSERT INTO class.exam VALUES 
(null, 'Mike', 100, 100),
(null, 'John', 96, 84),
(null, 'Cole', 83, 66),
(null, 'Jordan', 100, 100),
(null, 'Curry', 76, 93),
(null, 'Lebron', 58, 89);
```

#### 결과

| **id** | **name** | **math** | **english** |
| :----: | :------: | :------: | :---------: |
|   1    |   Mike   |   100    |     100     |
|   2    |   John   |    96    |     84      |
|   3    |   Cole   |    83    |     66      |
|   4    |  Jordan  |   100    |     100     |
|   5    |  Curry   |    76    |     93      |
|   6    |  Lebron  |    58    |     89      |

---

### ✅ IF 및 CASE 문법 활용

```sql
SELECT name, math,
  IF(math >= 80, 'good', 'try') AS Feedback,
  english,
  CASE 
    WHEN english >= 90 THEN '수'
    WHEN english >= 80 THEN '우'
    WHEN english >= 70 THEN '미'
    WHEN english >= 60 THEN '양'
    ELSE '가'
  END AS english
FROM class.exam;
```

- `IF`: 조건이 하나일 때 간단히 사용 가능
- `CASE`: 여러 조건을 순차적으로 검사할 수 있음
- `END`는 반드시 붙여야 함
- 큰 범위부터 좁은 범위 순서로 작성

#### 결과

| **name** | **math** | **Feedback** | **english** | **english** |
| :------: | :------: | :----------: | :---------: | :---------: |
|   Mike   |   100    |     good     |     100     |     수      |
|   John   |    96    |     good     |     84      |     우      |
|   Cole   |    83    |     good     |     66      |     양      |
|  Jordan  |   100    |     good     |     100     |     수      |
|  Curry   |    76    |     try      |     93      |     수      |
|  Lebron  |    58    |     try      |     89      |     우      |

---

## 2. IFNULL

```sql
INSERT INTO class.exam VALUES (null, 'Kim', null, null);

SELECT name, math, IFNULL(math, 0) AS in_math
FROM class.exam;
```

- `NULL`과 `0`은 다르며, 연산 시 `NULL`이 포함되면 전체가 `NULL`로 처리됨
- `IFNULL(value, replacement)`는 값이 NULL이면 지정된 대체값을 출력

#### 결과

| **name** | **math** | **in_math** |
| :------: | :------: | :---------: |
|   Mike   |   100    |     100     |
|   John   |    96    |     96      |
|   Cole   |    83    |     83      |
|  Jordan  |   100    |     100     |
|  Curry   |    76    |     76      |
|  Lebron  |    58    |     58      |
|   Kim    |   NULL   |      0      |

```sql
SELECT 100 + NULL; -- 결과: NULL
```

---

## 3. GROUP BY

### ✅ 기본 Group By 예시

```sql
SELECT STATE, COUNT(*) AS cnt
FROM class.c_customer
GROUP BY STATE;
```

- 각 주별로 몇 명이 있는지를 집계

---

### ✅ 집계함수 없이 그룹화 시 오류

```sql
SELECT BIGCATEGORY, COUNT(*) AS cnt, PRICE
FROM class.c_product
GROUP BY BIGCATEGORY;
```

- `PRICE`는 집계함수를 사용하지 않으면 오류 발생

---

### ✅ 집계함수 적용 예시

```sql
SELECT BIGCATEGORY, SUBCAREGORY,
  COUNT(*) AS cnt,
  SUM(PRICE) AS sum,
  MAX(PRICE) AS max_,
  MIN(PRICE) AS min_
FROM class.c_product
GROUP BY BIGCATEGORY, SUBCAREGORY;
```

- `GROUP BY` 절로 묶는 컬럼 외에는 반드시 집계함수로 처리해야 함

---

## Summary

- **IF**: 조건이 하나일 때 사용
- **CASE**: 조건이 여러 개일 때 사용, 반드시 `END`로 마무리
- **IFNULL**: NULL 값을 특정 값으로 대체
- **GROUP BY**: 행을 그룹화할 때 사용, 집계함수와 함께 사용해야 함
  - `GROUP BY` 컬럼 외에는 반드시 집계함수로 묶을 것

---

End
