---
title: 02. Internet Engineering - Wireshark_1
description: "인터넷공학 Wireshark 실습 1: 기본 HTTP GET 요청과 응답을 캡처해 HTTP 버전, 언어, 상태 코드 등을 분석."
Author: YoungHoon Ko
date: 2026-05-05 18:55:32 +0900
categories: [University(2026-First-Semester), Internet Engineering]
tags: [internet, network]
image: /assets/img/lang/internet.png
---

# 인터넷 공학(2) - Wireshark 실습

작성 : C289002 고영훈

---

# 1. 기본 HTTP GET/응답 상호작용

![a](/assets/img/internet_engineering/a.png)

## 문제 1

### 문제

사용 중인 브라우저는 HTTP 1.0, 1.1, 2 중 어느 버전을 사용하고 있는가?  
서버는 어떤 HTTP 버전을 사용하고 있는가?

### 정답

HTTP 1.1 버전 사용

---

## 문제 2

### 문제

브라우저는 서버에 어떤 언어를 수용할 수 있다고 알리고 있는가?

### 정답

Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7

한국어, 영어 수용 가능

---

## 문제 3

### 문제

여러분의 컴퓨터 IP 주소는 무엇인가?  
gaia.cs.umass.edu 서버의 IP 주소는 무엇인가?

### 정답

- 내 컴퓨터의 IP 주소(Source): 172.16.2.212
- gaia.cs.umass.edu 서버의 IP 주소(Destination): 128.119.245.12

---

## 문제 4

### 문제

서버가 브라우저에 반환한 상태 코드는 무엇인가?

### 정답

200 (HTTP/1.1 200 OK (text/html))

---

## 문제 5

### 문제

여러분이 요청한 HTML 파일은 서버에서 마지막으로 언제 수정되었는가?

### 정답

Last-Modified: Tue, 28 Oct 2025 05:59:01 GMT

---

## 문제 6

### 문제

브라우저로 몇 바이트의 내용이 반환되는가?

### 정답

[Content length: 128]

128 bytes

(Content-Length와 File Data 확인)

---

## 문제 7

### 문제

패킷 내용 창의 raw data를 조사할 때, 패킷 목록 창에는 표시되지 않는 헤더가 보이는가? 있다면 하나를 적어라.

### 정답

Host: gaia.cs.umass.edu

---

# 2. HTTP 조건부 GET/응답 상호작용

![b](/assets/img/internet_engineering/b.png)

## 문제 9

### 문제

서버 응답을 조사하라. 서버가 파일 내용을 명시적으로 반환했는가? 어떻게 알 수 있는가?

### 정답

서버는 파일 내용을 명시적으로 반환했다.

응답이 200 OK이며, 응답 패킷 안에 Content-Length와 File Data가 보여 실제 HTML 데이터가 포함되어 있음을 알 수 있다.

---

## 문제 10

### 문제

이제 두 번째 HTTP GET 요청을 조사하라. HTTP GET 안에 “IF-MODIFIED-SINCE:” 줄이 보이는가? 보인다면 그 뒤에는 어떤 정보가 오는가?

### 정답

보인다.

If-Modified-Since: Tue, 28 Oct 2025 05:59:01 GMT

파일이 2025년 10월 28일 05:59:01 GMT 이후에 수정되었을 때만 새 파일을 보내 달라고 요청하는 의미이다.

- 수정 O → 200 OK + 파일 내용 전송
- 수정 X → 304 Not Modified + 파일 내용 미전송

---

## 문제 11

### 문제

이 두 번째 HTTP GET에 대한 서버의 HTTP 상태 코드와 구문(phrase)은 무엇인가?  
서버가 파일 내용을 명시적으로 반환했는가? 설명하라.

### 정답

- 상태 코드: 304
- 구문: Not Modified

서버는 파일 내용을 명시적으로 반환하지 않았다.

304 Not Modified는 파일이 변경되지 않았음을 의미하며, 이 경우 서버는 본문 데이터를 보내지 않고 브라우저가 캐시된 파일을 사용하게 한다.

이미 캐시가 있는 경우 → 304 Not Modified → 파일 내용을 명시적으로 반환 X

---

# 3. 긴 문서 가져오기

![c](/assets/img/internet_engineering/c.png)

![c1](/assets/img/internet_engineering/c1.png)

## 문제 12

### 문제

브라우저는 몇 개의 HTTP GET 요청 메시지를 보냈는가?  
Bill of Rights에 대한 GET 메시지는 trace의 몇 번 패킷에 있는가?

### 정답

1966번 패킷

---

## 문제 13

### 문제

HTTP GET 요청에 대한 응답의 상태 코드와 구문이 포함된 것은 trace의 몇 번 패킷인가?

### 정답

2172번 패킷

---

## 문제 14

### 문제

응답의 상태 코드와 구문은 무엇인가?

### 정답

- 상태 코드: 304
- 구문: Not Modified

---

## 문제 15

### 문제

단일 HTTP 응답과 Bill of Rights 텍스트를 실어 나르기 위해 몇 개의 데이터 포함 TCP 세그먼트가 필요했는가?

### 정답

캡처에서는 Not Modified로 표시되기 때문에 텍스트 데이터를 실어 나르는 TCP는 0개라고 볼 수 있다.

---

# 4. 내장 객체를 포함한 HTML 문서

![d](/assets/img/internet_engineering/d.png)

## 문제 16

### 문제

브라우저는 몇 개의 HTTP GET 요청 메시지를 보냈는가?  
이 GET 요청들은 어떤 인터넷 주소들로 전송되었는가?

### 정답

4개의 HTTP GET 요청 메시지를 보냄

- 첫 번째 GET: 2.56.99.24 (kurose.cslash.net)
- 두 번째 GET: 128.119.245.12 (gaia.cs.umass.edu)
- 세 번째 GET: 128.119.245.12 (gaia.cs.umass.edu)
- 네 번째 GET: 128.119.245.12 (gaia.cs.umass.edu)

---

## 문제 17

### 문제

브라우저가 두 이미지를 순차적으로(serially) 내려받았는지, 아니면 두 웹사이트로부터 병렬로(parallel) 내려받았는지 알 수 있는가? 설명하라.

### 정답

각 GET의 시간과 각 응답 시작 시간을 비교한다.

포트 번호보다 시간 흐름이 더 중요하다.

두 이미지는 순차적(serially)으로 내려받았다.

pearson.png 요청과 응답(301) 이후에 8E_cover_small.jpg 요청이 시작되므로, 두 요청이 동시에 진행된 병렬 다운로드로 보기 어렵다.

---

# 5. HTTP 인증

![e](/assets/img/internet_engineering/e.png)

## 문제 18

### 문제

브라우저의 초기 HTTP GET 메시지에 대해 서버는 어떤 응답(상태 코드와 구문)을 반환하는가?

### 정답

- 상태 코드: 401
- 구문: Unauthorized

---

## 문제 19

### 문제

브라우저가 두 번째로 HTTP GET 메시지를 보낼 때, 어떤 새로운 필드가 HTTP GET 메시지에 포함되는가?

### 정답

Authorization: Basic d2lyZXNoYXJrLXN0dWRlbnRzOm5ldHdvcms=

필드가 새롭게 추가됨

---

End.
