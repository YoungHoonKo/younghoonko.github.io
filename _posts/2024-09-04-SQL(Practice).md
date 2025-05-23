---
title: 09. SQL(Practice)
Author: YoungHoon Ko
date: 2024-09-04 16:45:00 +0900
categories: [SQL, Practice_SQL]
tags: [sql, basic, sql_p]
image: /assets/img/lang/sql.png
---

[toc]

# Practice 문제 풀이

## 🧠 문제

> 2014년에 이루어진 주문들 가운데 State별 평균 주문금액이 큰 순서대로 State명과 주문 금액을 출력하라

---

## 🔍 문제 분석 (한글로 정리)

- 2014년 주문 데이터 → `WHERE`에서 날짜 필터링 필요
- State 정보 → `c_customer` 테이블
- 주문 금액 → **제품 가격 × 주문 수량**
- 제품 가격 → `c_product`, 수량 → `c_order`
- 최종 출력 → **State명과 평균 주문 금액**
- 정렬 → 평균 주문금액 기준 `내림차순 정렬`

---

## 🧩 테이블 구조 및 연결 키

- `c_order`: `ORDERID(PK)`, `CUSTOMERID`, `PRODUCTID`, `ORDERDATE`, `QUANTITY`
- `c_customer`: `CUSTOMERID(PK)`, `STATE`
- `c_product`: `PRODUCTID(PK)`, `PRICE`

---

## ✅ SQL 풀이 과정

### 1단계: 테이블 구조 확인

```sql
SELECT * FROM class.c_order;
SELECT * FROM class.c_customer;
SELECT * FROM class.c_product;
```

---

### 2단계: Join을 위한 메인 테이블 선정

- `c_order`가 주문의 중심 → `CUSTOMERID`, `PRODUCTID` 모두 포함

```sql
SELECT *
FROM class.c_order co
JOIN class.c_customer cc ON co.CUSTOMERID = cc.CUSTOMERID
JOIN class.c_product cp ON co.PRODUCTID = cp.PRODUCTID;
```

---

### 3단계: 2014년 주문만 필터링

```sql
SELECT *
FROM class.c_order co
JOIN class.c_customer cc ON co.CUSTOMERID = cc.CUSTOMERID
JOIN class.c_product cp ON co.PRODUCTID = cp.PRODUCTID
WHERE co.ORDERDATE >= '2014-01-01'
  AND co.ORDERDATE <= '2014-12-31';
```

---

### 4단계: State별 평균 주문금액 집계

```sql
SELECT cc.STATE, AVG(cp.PRICE * co.QUANTITY) AS AVG_Amount
FROM class.c_order co
JOIN class.c_customer cc ON co.CUSTOMERID = cc.CUSTOMERID
JOIN class.c_product cp ON co.PRODUCTID = cp.PRODUCTID
WHERE co.ORDERDATE >= '2014-01-01'
  AND co.ORDERDATE <= '2014-12-31'
GROUP BY cc.STATE;
```

---

### 5단계: 평균 금액 기준 내림차순 정렬

```sql
SELECT cc.STATE, AVG(cp.PRICE * co.QUANTITY) AS AVG_Amount
FROM class.c_order co
JOIN class.c_customer cc ON co.CUSTOMERID = cc.CUSTOMERID
JOIN class.c_product cp ON co.PRODUCTID = cp.PRODUCTID
WHERE co.ORDERDATE >= '2014-01-01'
  AND co.ORDERDATE <= '2014-12-31'
GROUP BY cc.STATE
ORDER BY AVG_Amount DESC;
```

---

## 🧾 결과

| 순위 |        State         | Average Order Amount |
| :--: | :------------------: | :------------------: |
|  1   |    New Hampshire     |        225.94        |
|  2   |       Arkansas       |        205.73        |
|  3   |      New Mexico      |        180.80        |
|  4   |     Connecticut      |        179.91        |
|  5   |      Wisconsin       |        179.41        |
|  6   | District of Columbia |        178.28        |
|  7   |       Missouri       |        166.71        |
|  8   |      Tennessee       |        166.12        |
|  9   |       Delaware       |        155.53        |
|  10  |       Arizona        |        155.33        |

---

## Summary

- 문제는 **한글로 정리하며 파악**하는 습관이 중요하다.
- **SELECT는 컬럼(가로)**, **WHERE은 조건(세로)** 로 접근
- **조인 키와 메인 테이블 선택**이 쿼리 성능과 가독성에 영향을 미친다.
- 집계 함수와 `GROUP BY`, `ORDER BY`의 조합으로 원하는 결과를 얻는다.

---

End
