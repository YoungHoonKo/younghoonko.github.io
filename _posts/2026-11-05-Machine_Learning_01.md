---
title: 01. Machine Learning_NumPy & Pandas (Data Handling and Preprocessing)
description: "머신러닝및실습(2) 1~4주차 정리: NumPy 인덱싱, 슬라이싱, reshape, axis와 뷰/복사, Pandas concat, merge, loc, iloc, 누락값 처리, pivot과 pivot_table, melt, to_numeric, category, 그리고 AirKorea와 기상청 데이터를 합치는 실전 전처리와 window sliding"
Author: YoungHoon Ko
date: 2026-11-05 15:35:00 +0900
categories: [University(2026-First-Semester), Machine Learning]
tags: [machine-learning, deep-learning, python]
image: /assets/img/lang/ml.png
---

## 학습 목표

- NumPy에서 인덱싱, 슬라이싱, reshape 조건, axis의 의미, 뷰와 복사의 차이
- Pandas `concat`과 `merge`의 차이와 옵션(axis, ignore_index, join, how, on)
- `loc`과 `iloc`, 열 추출과 행 추출, 데이터 확인 함수
- 누락값(NaN)의 특성, 발생 원인, 처리 방법
- pivot, pivot_table, melt로 데이터 재구조화
- 실전 전처리: 측정일시 형식 통일, 데이터 병합, window sliding으로 입력과 정답 만들기

> 시험에 서술형으로 나올 수 있는 정의(인덱싱, 슬라이싱, 데이터, 데이터프레임, 시리즈, 피벗)는 **굵게** 표시했음. 모든 코드는 numpy 2.5, pandas 3.0에서 다시 실행한 결과
{: .prompt-info }

---

## 1. NumPy

### 정의

- **Indexing**: 데이터에 접근하기 위해 인덱스를 사용하는 것
- **Slicing**: 인덱스를 활용해 일부분 또는 전체를 추출하는 것

### 핵심 규칙

| 항목 | 내용 |
| --- | --- |
| 열 슬라이싱 | 모든 행의 특정 열은 `a[:, a:b]`. 행 자리를 비워 둘 수 없음 |
| 행 슬라이싱 | `a[a:b]`처럼 하나만 쓰면 행 기준 |
| reshape 조건 | 원소 개수가 같아야 함. (a, b) → (c, d, e)이면 a×b = c×d×e |
| 2차원 axis | axis=0은 행 인덱스 방향(위→아래), axis=1은 열 인덱스 방향(왼→오) |
| 3차원 axis | 하나씩 밀려서 axis=0은 깊이, 1은 행, 2는 열 |
| 뷰와 복사 | 슬라이싱 결과는 원본을 참조(뷰). 독립된 배열은 `.copy()` |

### 실행 결과

```python
a = np.arange(12).reshape(3, 4)
a[:, 1:3]                 # 모든 행의 1~2열
a[1:3]                    # 1~2행
a.reshape(5, 3)           # 12개를 15칸에 담을 수 없음
a.sum(axis=0), a.sum(axis=1)
v = a[0, 0:2]; v[0] = 99  # 뷰를 수정
c = a[1, 0:2].copy(); c[0] = -1
```

```text
[[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]]
a[:, 1:3] =
 [[ 1  2]
 [ 5  6]
 [ 9 10]]
a[1:3] =
 [[ 4  5  6  7]
 [ 8  9 10 11]]
reshape(2,2,3) shape: (2, 2, 3)
ValueError: cannot reshape array of size 12 into shape (5,3)
sum axis=0: [12 15 18 21] / axis=1: [ 6 22 38]
3D sum axis=0 shape: (2, 3) axis=2 shape: (2, 2)
슬라이스 수정 후 원본 a[0]: [99  1  2  3]
copy 수정 후 원본 a[1]: [4 5 6 7]
```

- `axis=0`으로 합하면 행이 사라져 열 개수(4)만큼 결과가 남고, `axis=1`이면 행 개수(3)만큼 남음
- 뷰를 수정하면 원본 `a[0, 0]`도 99로 바뀌지만, `copy()`로 만든 배열은 원본에 영향이 없음

---

## 2. Pandas 기본 개념

- **데이터**: 프로그램이 다룰 수 있도록 기호화, 숫자화한 자료
- **데이터프레임**: 행과 열로 표현한 2차원 데이터. 길이가 같은 벡터를 열로 모은 구조이며 배열, 리스트, 딕셔너리로 만들 수 있음
- **시리즈**: 컬럼 하나로 이루어진 1차원 자료구조. 출력할 때 인덱스가 함께 나옴

### concat과 merge

| 함수 | 동작 | 주요 옵션 |
| --- | --- | --- |
| `pd.concat([df1, df2])` | 그대로 이어 붙임. 기본 axis=0(위아래) | `axis=1`(좌우), `ignore_index=True`(인덱스 새로 부여), `join='outer'`(기본, 합집합) 또는 `'inner'` |
| `pd.merge(df1, df2)` | 공통 열(key) 값을 기준으로 연결. 기본은 교집합(inner) | `on='key'`, `how='outer'`, `'left'`, `'right'` |

```text
    A    B    C
0  a0   b0  NaN
1  a1   b1  NaN
0  a2  NaN   c2
1  a3  NaN   c3
    A
0  a0
1  a1
2  a2
3  a3
    A   B   A   C
0  a0  b0  a2  c2
1  a1  b1  a3  c3
  key  x   y
0  k1  2  20
1  k2  3  30
  key    x     y
0  k0  1.0   NaN
1  k1  2.0  20.0
2  k2  3.0  30.0
3  k3  NaN  40.0
  key  x     y
0  k0  1   NaN
1  k1  2  20.0
2  k2  3  30.0
```

- concat은 원래 인덱스를 **유지**하므로 0, 1, 0, 1이 생김 → `ignore_index=True` 또는 `reset_index(drop=True)`
- `join='inner'`는 공통 컬럼 A만 남김. 기본값인 outer는 없는 칸이 NaN
- merge 기본(inner)은 공통 key인 k1, k2만, outer는 전부, left는 왼쪽 key 기준

### merge, concat이 안 되거나 결과가 이상할 때

- 기준 열이 한쪽에 없음, 이름이 다름(`id`와 `ID`), 자료형이 다름(`int`와 `str`), 날짜 형식이 다름
- 공백, 대소문자, 숨은 문자 차이(`"A"`와 `"A "`), 중복 key 때문에 행이 늘어남
- concat에서 컬럼 구성이 다르면 NaN, `axis=1`에서 인덱스가 다르면 정렬이 어긋남

### 데이터 추출과 확인

| 코드 | 의미 |
| --- | --- |
| `df['열']` | 열 하나 → Series, 두 개 이상이면 DataFrame |
| `df.loc[인덱스 이름, 컬럼 이름]` | 이름 기준. 인덱스에 없는 값(예: -1)은 KeyError |
| `df.iloc[행 번호, 열 번호]` | 정수 위치 기준. -1은 마지막 행 |
| loc, iloc에 쓸 수 있는 것 | 리스트, 슬라이싱, range, 조건 |
| `head(n)`, `tail(n)` | 앞, 뒤에서 **n개** |
| `shape`, `ndim`, `columns`, `dtypes`, `info()` | 크기, 차원, 컬럼명, 자료형, 요약 정보 |
| `drop(행 이름, axis=0)`, `drop(열 이름, axis=1)` | axis를 잘못 주면 KeyError |
| `count(axis=0)` | 열마다 NaN이 아닌 값의 개수 |
| `sort_index()`, `sort_values('열', ascending=False)` | 인덱스 정렬, 값 정렬 |

```text
{'국어': 80, '수학': 95} {'국어': 60, '수학': 65}
[85, 95, 75] [85, 95]
['one', 'two']
head(2) 행 수: 2
loc[-1] KeyError: -1
drop('one', axis=1) KeyError: "['one'] not found in axis"
```

loc 슬라이싱 `'one':'three'`는 **끝을 포함**하고, iloc 슬라이싱 `0:2`는 끝을 포함하지 않음.

---

## 3. 누락값 (NaN)

- 값이 없음을 뜻하며 0, 공백, False와 다르고 **NaN끼리도 같지 않음**
- 발생하는 경우: 누락값이 있는 데이터를 연결할 때, 데이터를 잘못 입력했을 때, 존재하지 않는 데이터를 추출할 때
- 개수: `df.shape[0] - df.count()` 또는 `df.isnull().sum()`, 존재 여부: `df.isnull().values.any()`
- 처리: `dropna()`(하나라도 NaN이면 행 삭제), `fillna(상수)`, `ffill()`(직전 값), `bfill()`(직후 값), `fillna(df.mean())`
- NaN이 포함된 연산 결과는 NaN이지만, `sum()`, `mean()`은 기본값 `skipna=True`라서 NaN을 건너뜀

```text
NaN == NaN: False
개수: {'x': 2, 'y': 1} / isnull().sum(): {'x': 2, 'y': 1}
any: True
ffill: [1.0, 1.0, 3.0, 3.0] bfill: [1.0, 3.0, 3.0, nan]
mean fill: [[1.0, 2.667], [2.0, 2.0], [3.0, 2.0], [2.0, 4.0]]
sum skipna: 4.0 / skipna=False: nan
dropna 행 수: 1
```

---

## 4. 피벗과 자료형

- **피벗**: 많은 데이터 중 필요한 자료만 뽑아 새로운 표로 재구성하는 기능
- `pivot(index, columns, values)`: 중복된 (index, columns) 조합이 있으면 **에러**
- `pivot_table(..., aggfunc='sum')`: 중복을 집계 함수로 처리
- `melt(id_vars, value_vars, var_name, value_name)`: 넓은 표를 긴 표로 (pivot의 반대)
- `to_numeric(errors=...)`: `'raise'`는 에러, `'coerce'`는 변환할 수 없는 값을 NaN으로
- **category**: 함수가 아니라 자료형. 같은 문자열이 반복되는 범주형 데이터에서 메모리와 속도가 유리

```text
pivot ValueError: Index contains duplicate entries, cannot reshape
subject  kor  math
grade             
1        180    90
2         70    60
  id subject  score
0  A     kor     80
1  B     kor     70
2  A    math     90
3  B    math     60
coerce: [1.0, 2.0, nan]
raise: Unable to parse string "abc" at position 2
object bytes: 6800132 / category bytes: 100276
```

- 1학년 kor이 두 번 나와서 `pivot`은 실패하고, `pivot_table`은 80 + 100 = 180으로 합침
- 남/여 10만 개를 category로 바꾸면 메모리가 약 68배 줄어듦

---

## 5. 실전 데이터 처리 (AirKorea + 기상청)

2020년 서울 대기오염(AirKorea, 월별 엑셀 12개)과 기상청(KMA) 데이터를 합쳐 미세먼지 예측용 데이터를 만든 실습.

1. `glob.glob('./2020_airkorea/2020y*')`로 파일 목록을 얻고 정렬
2. `globals()['seoul_air_{}'.format(i)] = pd.read_excel(...)`로 변수 12개를 자동 생성
3. 주소가 도봉구 측정소인 행만 남기고 필요한 열(측정일시, SO2, CO, O3, NO2, PM10)을 추린 뒤 `concat`으로 1년치를 합침
4. 결측 처리: `isnull().values.any()`로 NaN이 있는 열(CO)을 찾고, -999 같은 이상값도 검사한 다음 `round(df.mean(), 3)`으로 구한 열 평균으로 `fillna`
5. 측정일시 형식 통일: AirKorea는 `2020010124`처럼 **24시**를 쓰므로 `00`시로 바꾸고 하루를 더함
6. `pd.merge(air, kma, on='측정일시', how='left')`로 병합하고, 2021-01-01 00시처럼 기상 데이터가 없는 마지막 행은 삭제 (8,784행 → 8,783행)
7. window sliding: 3시간의 데이터(입력)로 다음 1시간의 PM10(정답)을 만드는 함수 `extract_inputoutput`

```text
2020-01-01 01:00:00 2021-01-01 00:00:00
```

- `dt.timedelta(days=1)`처럼 인자는 **복수형**(days, hours, minutes)
- window sliding 결과: 입력 X는 8,780행 × 33열(11열 × 3시간), 정답 Y는 8,780행
- `.at[행, 열]`은 값 하나만 빠르게 읽고 쓸 때 사용

---

### 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| head, tail | 앞에서부터 n-1까지 | 앞(뒤)에서 **n개** | `head(2)` 결과는 2행 |
| loc | 음수 넣으면 오류, 문자열 입력 | 인덱스 **이름**으로 찾으므로 인덱스에 없는 값이면 KeyError | 인덱스가 정수면 loc에 정수를 씀 |
| count | `count(axis=0)` - 열을 기준으로 행의 개수 | 열마다 NaN이 아닌 값의 개수 | 실행 결과 x는 2개, y는 3개 |
| 정렬 | `sort_value`, `accending` | `sort_values`, `ascending` | 함수 이름, 인자 이름 오타 |
| NaN 확인 | `isnull().value.any()` | `isnull().values.any()` | 속성 이름 오타 |
| 평균 대체 | `df.fillan(df.mean())` | `df.fillna(df.mean())` | 오타 |
| pivot_table | `pf.pivot_table(df, ...)` | `pd.pivot_table(df, ...)` | 오타 |
| fillna | `fillna(method='ffill')` | `ffill()`, `bfill()` | pandas 2.1부터 deprecated, 3.0에서 제거 |
| to_numeric | `errors='ignore'` | 사용하지 않음 | pandas 2.2부터 deprecated |
| 신경망 구조 (5주차) | 입력층, 은닉층, 입력층 | 입력층, 은닉층, **출력층** | 오타 |

---

## 최종 핵심 정리

1. 인덱싱은 인덱스로 원소에 접근, 슬라이싱은 인덱스 범위로 일부를 추출하는 것
2. reshape는 원소 개수가 같아야 하고, 2차원에서 axis=0은 행 방향, axis=1은 열 방향
3. 슬라이싱은 원본을 참조하는 뷰라서 독립적으로 쓰려면 `copy()`가 필요함
4. concat은 그대로 이어 붙이고(기본 위아래, 인덱스 유지), merge는 key 값을 기준으로 연결함(기본 inner)
5. loc은 이름 기준(끝 포함), iloc은 정수 위치 기준(끝 미포함, -1은 마지막)
6. NaN은 어떤 값과도 같지 않고, `isnull`, `dropna`, `fillna`, `ffill`, `bfill`로 처리하며 `skipna=True`가 기본
7. pivot은 중복이 있으면 에러, pivot_table은 aggfunc로 집계하고, melt는 그 반대 방향의 변환
8. 실전 전처리는 파일 모으기 → 형식 통일(24시 처리) → merge → 결측 행 제거 → window sliding 순서

---

End.
