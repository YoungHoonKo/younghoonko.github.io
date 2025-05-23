---
title: 06. HTML-Typing the contents(Table)
Author: YoungHoon Ko
date: 2022-07-16 18:49:20 +0900
categories: [HTML, HTML-Study]
tags: [html]
image: /assets/img/lang/html.png
---

# 4. 웹 문서에 내용 입력하기

[toc]

## 4-3. 표 만들기

이번 학습에서는 HTML에서 테이블을 만드는 다양한 태그들을 정리하였다. 표는 정보를 구조적으로 표현할 수 있는 중요한 구성 요소로, 콘텐츠를 행(row)과 열(column)로 구분하여 표시할 수 있게 해준다.

---

### 1. 표의 기본 구성 요소

- HTML 표는 **행(row), 열(column), 셀(cell)**로 이루어져 있다.
- 셀은 행과 열이 만나는 지점으로 실제 내용이 표시되는 공간이다.

---

### 2. `<table>` 태그와 `<caption>` 태그

- `<table>` 태그는 표 전체를 정의하는 태그로, 표의 시작과 끝을 감싼다.
- `<caption>` 태그는 표의 제목 역할을 하며, `<table>` 태그 바로 아래 위치시킨다.

~~~html
<table>
  <caption>title</caption>
</table>
~~~

---

### 3. `<tr>`, `<td>`, `<th>` 태그

- `<tr>`: 한 행(row)을 의미
- `<td>`: 일반 셀 데이터를 나타냄 (table data)
- `<th>`: 제목 셀을 의미하며, **진하게 중앙 정렬됨**

~~~html
<table>
  <tr>
    <th>진하게 표시됨</th>
    <th>중앙에 배치됨</th>
  </tr>
  <tr>
    <td>일반 셀에 내용입력</td>
    <td>일반 셀에 내용입력</td>
  </tr>
</table>
~~~

---

### 4. `<thead>`, `<tbody>`, `<tfoot>` 태그

표를 **제목, 본문, 요약**의 세 부분으로 나누어 구조화할 수 있다. 이는 접근성 향상 및 스크립트 제어에도 유리하다.

- `<thead>`: 표의 제목 행을 감싸며, 보통 `<th>` 태그와 함께 사용
- `<tbody>`: 표의 본문 내용을 감싸며, 반복되는 데이터에 활용
- `<tfoot>`: 표의 요약 정보를 작성할 때 사용

~~~html
<table>
  <caption>title</caption>
  <thead>
    <tr>
      <th>진한 글씨</th>
      <th>중앙에 배치됨</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>내용</td>
      <td>내용</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>요약</td>
      <td>요약</td>
    </tr>
  </tfoot>
</table>
~~~

---

### 5. `<rowspan>`, `<colspan>` 속성 — 셀 병합

표에서 셀을 가로 또는 세로로 병합하려면 `<td>` 또는 `<th>` 태그에 `rowspan`, `colspan` 속성을 지정한다.

- `rowspan`: 행(row) 병합
- `colspan`: 열(column) 병합

~~~html
<table>
  <caption>title</caption>
  <thead>
    <tr>
      <th rowspan="2">진한 글씨</th>
      <th>중앙에 배치됨</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>내용</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td colspan="2">요약</td>
    </tr>
  </tfoot>
</table>
~~~

---

### 6. `<col>`, `<colgroup>` 태그 — 열 속성 설정

- 표 전체에서 **특정 열의 스타일이나 속성을 지정**할 때 사용
- `<col>`: 하나의 열에 스타일 적용
- `<colgroup>`: 여러 열을 그룹화하여 한 번에 지정
- **위치는 반드시 `<caption>` 다음에 위치해야 한다**

~~~html
<table>
  <caption>표 제목</caption>
  <colgroup>
    <col style="background-color: lightyellow;">
    <col>
  </colgroup>
  <thead>
    <tr><th>항목</th><th>값</th></tr>
  </thead>
  <tbody>
    <tr><td>예시</td><td>데이터</td></tr>
  </tbody>
</table>
~~~

---

> 📌 정리  
HTML 표는 `<table>`을 중심으로 다양한 하위 태그를 활용해 구조를 설계할 수 있다. 구조적 의미를 고려하여 `<thead>`, `<tbody>`, `<tfoot>` 등을 적절히 사용하고, 셀 병합이나 열 스타일 지정도 이해하고 있어야 웹 페이지 구성에 유용하다.

---

End
