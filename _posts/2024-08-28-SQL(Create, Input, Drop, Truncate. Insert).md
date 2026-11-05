---
title: 03. SQL(Create, Input, Drop, Truncate. Insert)
description: "SQL 스키마와 테이블의 개념, CREATE로 테이블 만들기, INSERT로 데이터 입력, DROP과 TRUNCATE의 차이 정리."
Author: YoungHoon Ko
date: 2024-08-28 11:45:00 +0900
categories: [SQL, Practice_SQL]
tags: [sql, basic]
image: /assets/img/lang/sql.png
---

# 스키마와 테이블의 정의

## 스키마란 무엇인가?

- 테이블이나 뷰 또는 프로시저와 펑션 등 업무나 데이터의 **영역별로 구분되는 개념**
- 테이블들과 다양한 객체를 가진 집합

## 테이블이란 무엇인가?

- 데이터가 물리적으로 저장되는 **단위**
- 테이블이 모여서 하나의 스키마 또는 데이터베이스가 되며 다른 오브젝트들의 원천이 됨
- 모든 객체나 오브젝트는 테이블을 기반으로 관리됨

---

# Create & Insert

### ✅ CREATE: 테이블 생성

```sql
create schema test_schema;

create table test_schema.test_table(
  id int,
  name varchar(100),
  input_data date
);
```

> 테이블 생성 후 확인 쿼리

```sql
select * from test_schema.test_table;
```

📷 생성된 테이블 확인 결과  

|  **id**  | **name** | **input_data** |
| :------: | :------: | :------------: |
| *(없음)* | *(없음)* |    *(없음)*    |

---

### ✅ INSERT: 데이터 입력

```sql
insert into test_schema.test_table values
(1, '홍길동','2024-08-28'),
(2, '고길동','2024-08-29'),
(3, '김길동','2024-08-30');
```

📷 데이터 입력 결과  

| **d** | **name** | **input_data** |
| :---: | :------: | :------------: |
|   1   |  홍길동  |   2024-08-28   |
|   2   |  고길동  |   2024-08-29   |
|   3   |  김길동  |   2024-08-30   |

---

# 테이블 복제: AS vs LIKE

### ✅ AS: 구조 + 데이터 복사

```sql
create table test_schema.test_table2
as select * from test_schema.test_table;

select * from test_schema.test_table2;
```

📷 결과  

| **id** | **name** | **input_data** |
| :----: | :------: | :------------: |
|   1    |  홍길동  |   2024-08-28   |
|   2    |  고길동  |   2024-08-29   |
|   3    |  김길동  |   2024-08-30   |

---

### ✅ LIKE: 구조만 복사

```sql
create table test_schema.test_table3
like test_schema.test_table;

select * from test_schema.test_table3;
```

📷 결과  

|  **id**  | **name** | **input_data** |
| :------: | :------: | :------------: |
| *(없음)* | *(없음)* |    *(없음)*    |

---

# Drop & Truncate

### ✅ DROP: 테이블 완전 삭제

```sql
drop table test_schema.test_table3;
```

📷 결과  
```sql
ERROR 1146 (42S02): Table 'test_schema.test_table3' doesn't exist
```

---

### ✅ TRUNCATE: 데이터만 삭제 (테이블 구조 유지)

```sql
truncate table test_schema.test_table2;

select * from test_schema.test_table2;
```

📷 결과 (테이블 구조는 유지되고 데이터만 삭제됨)  

|  **id**  | **name** | **input_data** |
| :------: | :------: | :------------: |
| *(없음)* | *(없음)* |    *(없음)*    |

---

# Insert 예제 모음

```sql
-- 방법 1: 칼럼 순서대로 입력
insert into test_schema.test_table values
(1, '홍길동','2024-08-28');

-- 방법 2: 칼럼 순서를 지정해 입력
insert into test_schema.test_table(name, id, input_data) values
('박길동', 2, '2024-09-01');

-- 방법 3: 일부 칼럼만 입력
insert into test_schema.test_table(id, input_data) values
(1, '2024-08-28');
```

📷 다양한 Insert 결과  

| **id** | **name** | **input_data** |
| :----: | :------: | :------------: |
|   1    |  홍길동  |   2024-08-28   |
|   2    |  박길동  |   2024-09-01   |
|   1    |   NULL   |   2024-08-28   |

---

# Summary

- **스키마**: 데이터의 논리적 영역 구분
- **테이블**: 데이터 저장 단위
- `CREATE`: 테이블 또는 스키마 생성
- `INSERT`: 테이블에 데이터 추가
- `DROP`: 테이블 구조 + 데이터 완전 삭제
- `TRUNCATE`: 테이블 구조 유지, 데이터만 삭제

---

End
