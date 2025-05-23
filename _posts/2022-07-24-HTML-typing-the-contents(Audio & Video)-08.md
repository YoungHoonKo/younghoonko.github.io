---
title: 08. HTML-typing the contents(Audio & Video)
Author: YoungHoon Ko
date: 2022-07-24 13:14:38 +0900
categories: [HTML, HTML-Study]
tags: [html]
image: /assets/img/lang/html.png
---

[toc]

# 4. 웹 문서에 내용 입력하기

## 4-5. 오디오와 비디오 삽입하기

이번 학습에서는 HTML 문서에 오디오 및 비디오 콘텐츠를 삽입하는 방법에 대해 공부하였다. `<object>`, `<embed>`, `<audio>`, `<video>` 태그의 용도와 차이점을 중심으로 실습과 이론을 함께 정리하였다.

---

### 1. 멀티미디어 삽입 태그 정리

#### 1. `<object>` 태그

- 다양한 멀티미디어 파일(PDF, 오디오, 비디오 등)을 삽입할 수 있다.
- 기본 형식:

~~~html
<object width="너비" height="높이" data="파일"></object>
~~~

- 예시: PDF 삽입

~~~html
<object width="900" height="800" data="math.pdf"></object>
~~~

#### 2. `<embed>` 태그

- HTML 초기부터 사용된 태그로 **대부분의 브라우저에서 호환**됨.
- `<object>`와의 차이:
  - `src` 속성을 사용
  - **닫는 태그 없음**
- 예시: 오디오 삽입

~~~html
<embed src="musics/pop.mp3" width="300" height="100">
~~~

- `<audio>` 또는 `<video>` 태그를 지원하지 않는 경우 대안으로 사용 가능

#### 3. `<audio>` 및 `<video>` 태그

- `<audio>`: 배경 음악, 효과음 등을 삽입할 때 사용
- `<video>`: 비디오 파일 삽입 시 사용
- 공통 속성을 함께 사용하여 재생 컨트롤 제공 가능

##### `<audio>` 기본 형식

~~~html
<audio src="musics/pop.mp3" controls></audio>
~~~

##### `<video>` 기본 형식

~~~html
<video src="videos/field.mp4" controls width="700"></video>
~~~

---

### 2. `<audio>`, `<video>` 공통 속성 정리

| 속성            | 설명                                                         |
|------------------|--------------------------------------------------------------|
| controls         | 재생, 일시정지, 볼륨 등을 제어할 수 있는 컨트롤 바 표시     |
| autoplay         | 페이지 로드 시 자동 재생                                     |
| loop             | 반복 재생                                                    |
| muted            | 음소거 상태로 시작                                            |
| preload          | 페이지 로드시 미리 불러올 방식 지정                          |
| width, height    | 플레이어 크기 지정 (하나만 지정 시 비율 유지됨)             |
| poster="파일명"  | 비디오 재생 전 포스터 이미지 지정 (video 전용 속성)         |

---

### 3. 웹 브라우저별 미디어 파일 지원

| 브라우저         | 오디오(mp3) | 비디오(mp4) | 비디오(webm) |
|------------------|-------------|-------------|---------------|
| Internet Explorer| o (v9+)     | o (v9+)     | x             |
| Chrome           | o (all)     | o (all)     | o (25+)       |
| Firefox          | o (22+)     | o (35+)     | o (28+)       |
| Safari           | o (4+)      | o (3.2+)    | x             |
| Opera            | o (15+)     | o (25+)     | o (16+)       |
| iOS Safari       | o (4.1+)    | o (all)     | x             |
| Android Browser  | o (2.3+)    | o (4.4+)    | x             |

---

### 4. 실습 예제

#### 🎧 비디오 삽입과 자동 재생 설정

~~~html
<video src="videos/sample.mp4" autoplay controls width="700"></video>
~~~

---

> 📌 정리  
HTML의 멀티미디어 태그는 웹 문서에 다양한 콘텐츠를 삽입할 수 있는 중요한 도구이다. 태그의 차이점과 속성을 정확히 이해하고 사용함으로써 다양한 브라우저에서도 안정적으로 오디오 및 비디오 콘텐츠를 제공할 수 있다.

---

End
