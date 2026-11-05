---
title: How to use Typora
description: "Typora에서 쓰는 마크다운 문법 정리: 목차, 헤더, 인용, 목록, 코드 블록 등 블록 요소와 인라인 요소의 작성법."
Author: YoungHoon Ko
date: 2022-07-10 10:31:00 +0900
categories: [Markdown, Md-Basics]
tags: [markdown]
image: /assets/img/lang/md.png
---

# 마크다운 문법

**소스 코드는 cmd(ctrl)+ /로 확인할 수 있습니다.**

## 목차 설정방법

~~~markdown
[toc]을 사용하면 위에 보이는 목차처럼 목차가 생성됩니다. 새로운 항목을 추가할 때마다 목차가 갱신되어 실시간으로 확인할 수 있습니다.
~~~

## 블럭 요소

### 헤더

헤더는 1-6단계의 크기가 있습니다. `#`, `##`, `###` 등을 사용합니다.

~~~markdown
# This is an H1
## This is an H2
### This is an H3
~~~

### 인용

`>`를 사용해 인용문을 작성할 수 있습니다.

~~~markdown
> 안녕하세요
> 저는 고영훈입니다.
> 만나서 반갑습니다.
>> 저는 홍길동의 친구입니다.
~~~

> 이 문장은 '>' 인용 예문입니다.
>
> > 이 문장은 '>>' 인용 예문입니다.

### 리스트

#### Unordered List

`*`, `+`, `-` 를 사용합니다.

~~~markdown
* 항목 1
    * 하위 항목
+ 항목 2
- 항목 3
~~~

#### Ordered List

~~~markdown
1. 첫 번째
2. 두 번째
3. 세 번째
~~~

### 체크리스트

~~~markdown
- [ ] 할 일 1
- [ ] 할 일 2
~~~

- [ ] 체크리스트 예시1
- [ ] 체크리스트 예시2

### 코드블럭

~~~python
print("hello")
print("Typora")
~~~

수식을 적을 때도 코드블럭 안에 작성해야 오류가 없습니다.

### 표

~~~markdown
| 제목1 | 제목2 |
|-------|-------|
| 값1  | 값2  |
~~~

| 언어    | 단어  |
| ------- | ----- |
| Korean  | 물    |
| English | Water |

### 각주

footnote를 이렇게 넣을 수 있습니다(맨 밑 참고). [^footnote]

### 수평선

***
---
<hr/>

~~~markdown
***
---
<hr />
~~~

### 링크

#### Reference Link

~~~markdown
[링크 이름][id]
[id]: https://github.com/YoungHoonKo "깃허브 주소"
~~~

#### Internal Link

~~~markdown
[github](https://github.com/YoungHoonKo '깃허브')
~~~

### URL

<https://github.com/YoungHoonKo>

~~~markdown
<http://www.example.com>
~~~

### 이미지

![소녀](/assets/img/profile_2.jpg)

~~~markdown
![사진 이름](사진 주소)
~~~

### 강조

*이탤릭체*
_이탤릭체_

~~~markdown
*내용*
_내용_
~~~

### 굵은 글씨

**굵은 글씨**
__굵은 글씨__

~~~markdown
**내용**
__내용__
~~~

### 코드 인라인

`print('hello')`

### 취소선

~~지운 내용~~

~~~markdown
~~내용~~
~~~

### 밑줄

<u>밑줄</u>

~~~html
<u>밑줄</u>
~~~

### 이모티콘

:smile: 를 사용하거나 이모지 창에서 선택할 수 있습니다.

[^footnote]: 각주
