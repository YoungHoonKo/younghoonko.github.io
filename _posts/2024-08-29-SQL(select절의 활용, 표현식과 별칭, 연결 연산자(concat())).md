---
title: 04. SQL(SELECT절의 활용, 표현식과 별칭, 연결 연산자(CONCAT()))
Author: YoungHoon Ko
date: 2024-08-29 15:40:00 +0900
categories: [SQL, Practice_SQL]
tags: [sql, select]
image: /assets/img/lang/sql.png
---

[toc]

# SELECT 절의 다양한 활용

이번 학습에서는 SQL에서 가장 기본이 되는 **SELECT 절**을 다양한 방식으로 활용하는 방법을 배웠다.  
조건 지정, 표현식 사용, 별칭(alias), 그리고 문자열 연결 함수(CONCAT)까지 함께 실습하였다.

---

## 1. SELECT로 특정 컬럼 출력

```sql
select name, height from member;
```

- member 테이블에서 `name`, `height` 컬럼만 출력
- 특정 열만 추출하여 데이터를 효율적으로 확인 가능

---

## 2. 조건 지정: WHERE 절

```sql
select * from member where height >= 180;
```

- 키가 180cm 이상인 사람만 출력
- 조건식을 통해 원하는 데이터만 필터링 가능

---

## 3. 문자열 패턴 매칭: LIKE

```sql
select * from member where name like '%길동%';
```

- 이름에 `길동`이 포함된 모든 행 출력
- `%`는 와일드카드로, 문자열 일부 일치 조건 표현에 사용됨

---

## 4. 표현식 사용

```sql
select name, height, height+5 from member;
```

- 각 멤버의 키에 5를 더한 값을 같이 출력
- 컬럼 간 연산도 직접 쿼리 내에서 수행 가능

---

## 5. 별칭(Alias) 지정

```sql
select name as "이름", height as "키", height+5 as "키+5" from member;
```

- 출력 결과의 컬럼 이름을 보기 좋게 변경
- 가독성 향상 및 보고서 작성 시 유용

---

## 6. 연결 연산자: CONCAT()

```sql
select name, height, concat(name, height) from member;
```

- 문자열과 숫자를 연결하여 하나의 문자열로 출력
- 여러 칼럼을 하나로 합쳐서 표현할 때 유용

---

# Summary

- **SELECT**는 원하는 컬럼 또는 조건에 따라 데이터를 조회하는 데 사용
- **WHERE**는 조건 필터링, **LIKE**는 문자열 패턴 매칭
- 컬럼 간 연산, 표현식, 별칭, 문자열 연결 등도 함께 사용 가능
- 데이터 조회 결과를 보다 명확하게 표현하고 분석할 수 있음

---

End
