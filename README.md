# YoungHoonKo's Blog

[![Blog](https://img.shields.io/badge/Blog-younghoonko.github.io-2f6feb)](https://younghoonko.github.io)
[![Theme](https://img.shields.io/badge/Theme-Chirpy%207.3.0-4c1)](https://github.com/cotes2020/jekyll-theme-chirpy)
[![Deploy](https://github.com/YoungHoonKo/younghoonko.github.io/actions/workflows/jekyll.yml/badge.svg)](https://github.com/YoungHoonKo/younghoonko.github.io/actions/workflows/jekyll.yml)

홍익대학교 소프트웨어·데이터공학 전공 **고영훈**의 학습 기록 블로그 저장소입니다.
수업에서 배운 내용을 코드로 직접 실행해 확인하고, 결과와 해석까지 정리합니다.

👉 **https://younghoonko.github.io**

---

## What's Inside

| 분야 | 주제 |
| --- | --- |
| Data Science / Big Data | 데이터 전처리, 시각화, 통계 분석, 상관·회귀, 시계열, 텍스트 분석, R(dplyr, ggplot2) |
| Artificial Intelligence | NLP, 텍스트 전처리, TF-IDF, N-gram, Word Embedding, RNN |
| Communication Theory | 푸리에 변환, LTI 시스템, AM/FM 변조, 힐베르트 변환, 확률변수 |
| Statistics (R) | 중심극한정리, t-검정, 회귀분석, 분산분석 |
| Computer Engineering | C, 포인터와 구조체, 디지털공학, MIPS 어셈블리, 인터넷공학(Wireshark) |
| Programming & Tools | Python, SQL, HTML, Git, Markdown |

학기별 과목 정리와 기술 스택은 블로그의 [About](https://younghoonko.github.io/about/) 페이지에 있습니다.

### Writing Principles

- 강의 자료를 그대로 옮기지 않고, **코드를 직접 실행한 결과**로 내용을 확인합니다.
- 그래프와 그림은 직접 만들어 싣고, 강의 자료의 오류는 **정정 표**로 따로 정리합니다.
- 최근 글은 마지막에 **핵심 정리**를 두어 복습할 때 빠르게 볼 수 있게 합니다.

---

## Repository Structure

[Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 테마 저장소를 기반으로 하며, 글을 쓰거나 꾸밀 때 주로 다루는 위치는 아래와 같습니다.

```text
.
├── _posts/              # 블로그 글 (YYYY-MM-DD-제목.md)
├── _tabs/               # 사이드바 메뉴 페이지 (About, Archives, Categories, Tags)
├── assets/img/          # 글에 들어가는 이미지
│   ├── lang/            # 글 썸네일 (r.png, py.png, ...)
│   └── <과목>/           # 과목별 그림 (bigdata, comm, ai, data, ...)
├── _sass/               # 스타일 (사이드바 색, 버튼 정렬 등 커스텀 포함)
├── _data/contact.yml    # 사이드바 아래 연락처 아이콘
├── _config.yml          # 사이트 설정 (제목, 언어, 댓글 giscus 등)
└── .github/workflows/   # GitHub Pages 자동 배포 (jekyll.yml)
```

---

## Writing a Post

`_posts/`에 `YYYY-MM-DD-제목.md` 파일을 만들고 아래 형식의 front matter로 시작합니다.

```yaml
---
title: 01. Big Data_R Basics and R Objects
description: "홈 화면 미리보기에 보일 한 줄 설명"
date: 2026-11-04 15:50:00 +0900   # 현재 시각보다 미래면 게시되지 않음
categories: [University(2026-Second-Semester), Big Data]
tags: [r, bigdata]
math: true                        # 수식이 있을 때
image: /assets/img/lang/r.png     # 썸네일
---
```

- 목차는 오른쪽 패널에 자동으로 생기므로 본문에 `[toc]`를 쓰지 않습니다.
- 인라인 수식은 `$$ ... $$`로 쓰고, 수식 안의 절댓값 기호는 `\lvert`, `\rvert`를 씁니다.

---

## Local Development

Ruby 3.1 이상과 Bundler가 필요합니다.

```bash
bundle install
bundle exec jekyll serve      # http://127.0.0.1:4000
```

## Deployment

`main` 브랜치에 push하면 GitHub Actions(`.github/workflows/jekyll.yml`)가 사이트를 빌드해 GitHub Pages에 자동으로 배포합니다.

---

## Contact

- 📧 Email: nice2go@g.hongik.ac.kr
- 🐙 GitHub: [@younghoonko](https://github.com/younghoonko)
- 📷 Instagram: [@yh._2k_](https://instagram.com/yh._2k_/)

## License

블로그 테마는 [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) (MIT License, © Cotes Chung)를 사용합니다.
블로그 글과 이미지의 이용 조건은 사이트 하단의 라이선스 표기를 따릅니다.
