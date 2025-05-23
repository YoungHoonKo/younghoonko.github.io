---
title: 02. HTML-Basic structure
Author: YoungHoon Ko
date: 2022-07-12 12:45:00 +0900
categories: [HTML, HTML-Study]
tags: [html]
image: /assets/img/lang/html.png
---

# HTML 구조 파악하기

[toc]

## HTML 기본 구조 살펴보기

~~~html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>HTML 기본 구조 살펴보기</title>
</head>
<body>
  <h1>프런트엔드 웹 개발</h1>
  <hr>
  <p>HTML</p>
  <p>CSS</p>
  <p>JavaScript</p>
</body>
</html>
~~~

---

## HTML 구조 설명

HTML 문서는 일반 문서와 달리 **엄격한 구조**를 따라야 합니다. 위 예시는 매우 간단하지만, HTML 문서에 반드시 포함되어야 하는 핵심 구조를 모두 갖추고 있습니다.

### 1. 기본 구성 요소

- `<!DOCTYPE html>` : 해당 문서가 HTML5로 작성되었음을 선언
- `<html>` : 문서의 시작과 끝을 정의하는 태그
- `<head>` : 브라우저가 문서를 이해하는 데 필요한 정보들을 포함
- `<body>` : 실제 사용자 화면에 보여질 콘텐츠를 담는 부분

### 2. `lang` 속성의 의미

```html
<html lang="ko">
```

- `lang` 속성은 문서의 주 언어를 정의합니다.
- 검색엔진에서 특정 언어 필터링 시 유리하며,
- 화면 낭독기 같은 접근성 도구에서도 해당 언어에 맞는 억양이나 발음 적용에 도움을 줍니다.

### 3. `<head>` 안의 구성 요소

####  `<meta>` 태그

```html
<meta charset="UTF-8">
```

- 문서에 어떤 문자 인코딩을 사용할지를 정의합니다. UTF-8은 다양한 언어를 지원합니다.

```html
<meta name="keyword" content="html 구조">
<meta name="description" content="html 구조를 설명드리겠습니다.">
<meta name="author" content="YoungHoon Ko">
```

- 검색엔진과 브라우저를 위한 추가 설명 정보를 담을 수 있습니다.

####  `<title>` 태그

```html
<title>이것은 제목입니다.</title>
```

- 웹 브라우저 탭이나 검색 결과에 표시되는 문서 제목입니다.

### 4. `<body>` 안의 태그들

- `<h1>`: 제목 태그로, 숫자가 클수록 크기가 작아집니다 (`h1`~`h6`)
- `<p>`: 단락(paragraph)을 나타냅니다.
- `<hr>`: 수평선을 삽입합니다 (horizontal rule)

---

> 📌 **정리**: HTML 문서는 `DOCTYPE` 선언부터 시작하여, `html`, `head`, `body` 순으로 명확하게 구조화되어야 하며, 각 영역마다 의미와 목적이 뚜렷합니다.

---

End
