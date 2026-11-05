---
title: 02. Git(User Setting)
description: "Git 설치와 설치 확인, git config로 사용자 이름과 이메일을 설정하고 확인하는 방법."
Author: YoungHoon Ko
date: 2022-07-27 16:12:38 +0900
categories: [Git, Git-Study]
tags: [git, terminal, github]
image: /assets/img/lang/git.png
---

# Getting Started with Git

## 1. Install Git

- 공식 사이트: [https://git-scm.com/book/ko/v2/시작하기-Git-설치](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EC%84%A4%EC%B9%98)
- Git은 Windows, macOS, Linux 모두에서 설치 가능하며, 위 링크에 설치법이 자세히 안내되어 있다.

---

## 2. 설치 확인 (Check)

1. **Mac**: 터미널 실행  
   **Windows**: 설치된 **Git Bash** 실행
2. 아래 명령어 입력:

```bash
git
```

3. 다음과 같은 옵션 목록이 출력되면 설치가 정상적으로 완료된 것이다.

---

## 3. 사용자 설정 (User Setting)

Git을 사용하기 전, **사용자 정보(user.name, user.email)**를 반드시 설정해야 한다.  
이는 Git이 버전 기록을 남길 때 **누가** 작업했는지를 기록하기 위해서이다.

### ✅ 설정 방법

```bash
git config --global user.name "사용자 이름"
git config --global user.email "사용자 이메일"
```

- `--global` 옵션은 시스템 전체에 설정을 적용하며, 일반적으로 사용한다.
- 이름은 한글도 가능하지만 **영문 이름을 권장**한다.

### 📌 설정 확인

```bash
git config --global user.name
git config --global user.email
```

---

> 📌 정리  
Git을 설치한 후 가장 먼저 해야 할 일은 사용자 정보 설정이다. 올바르게 설정해두면 협업 시 이력 추적이 명확해지고, 커밋 정보가 일관되게 관리된다.

---

End
