---
title: 04. Statistics(One sample t-test report)
Author: YoungHoon Ko
date: 2022-11-11 23:33:00 +0900
categories: [University(2022-Second-Semester), Statistics]
tags: [r, statistics]
image: /assets/img/lang/r.png
---

[toc]

# 2022-11-11 통계학 과제

## 일표본 t-검정 보고서

### 문제

```markdown
R의 state.x77에서 기대수명 (Life.Exp)dl 71세라고 말할 수 있을 지에 알아보기 위하여, 
`유의수준 0.05`에서 일표본 T-검정을 실시 해보자. 그림1은 자료의 상자도표이다
```

### 그림1

<img src="/assets/img/boxplot.png" alt="이미지" style="zoom:50%;" />

### 풀이

```markdown
평균이 71인지 알아보기 위하여, 다음과 같이 가설을 세우자.
                       `H0: μ = 71   vs   H1 ∶ μ ≠ 71`
표본크기는 50이고, 표본평균은 𝑥ҧ = 70.8786 이고, 표본표준편차는 𝑠=1.342394이다.
평균에 대한 95% 신뢰구간 => (70.4971 71.2601)
검정통계량 => 𝑇 = -0.63948
유의확률 => 𝑝 = 0.5255이다. 
따라서 유의수준 0.05 에서 귀무가설을 기각하지 않는다.
즉, `유의수준 0.05에서 기대수명이 71세라고 말할 수 있다.` 
```

```R
# 코드
x <- state.x77[,4]
t.test(x, mu = 71)
sd(x)
```

```R
# 결과

# t.test(x, mu = 71)
One Sample t-test

data:  x
t = -0.63948, df = 49, p-value = 0.5255  # t = 검정통계량, p-value = 유의확률
alternative hypothesis: true mean is not equal to 71
95 percent confidence interval:
 70.4971 71.2601 # 95% 신뢰구간 (70.4971 71.2601)
sample estimates:
mean of x 
  70.8786 # 표본평균

# sd(x)
[1] 1.342394
```

```markdown
자료가 정규분포를 따르는지 확인하기 위하여, 샤피로의 검정을 실시하였다.
유의확률 P = 0.4423가 유의수준 0.05보다 크므로, 자료의 분포가 정규분포라고 볼 수 있다.
그림의 QQ-plot에서 자료들이 거의 일직선에 놓임을 확인할 수 있다.
```

```R
# 코드
shapiro.test(x)
qqnorm(x)
qqline(x)
```

```R
# 결과

# shapiro.test(x)
shapiro.test(x)

	Shapiro-Wilk normality test

data:  x
W = 0.97724, p-value = 0.4423 
```

### 그림2    

<img src="/assets/img/QQ-line.png" alt="이미지" style="zoom:50%;" />