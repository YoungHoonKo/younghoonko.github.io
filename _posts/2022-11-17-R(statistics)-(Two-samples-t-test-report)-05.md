---
title: 05. Statistics(Two samples t-test report)
Author: YoungHoon Ko
date: 2022-11-17 13:33:00 +0900
categories: [University(2022-Second-Semester), Statistics]
tags: [r, statistics]
image: /assets/img/lang/r.png
---

[toc]

# 2022-11-15 통계학 과제

## 이표본 t-검정 보고서

### 문제

```markdown
R의 InsectSprays에서 B,F를 뿌릴 때, 죽는 벌레 수가 동일한지 검정하기 위하여,
유의수준 0.05에서 이표본 T-검정을 실시해보자.
그림1은 자료의 상자도표이다.
```

### boxplot

<img src="/assets/img/boxplot2.png" alt="이미지" style="zoom:50%;" />

### 풀이

```markdown
두 스프레이를 뿌릴 때 죽은 평균 벌레수가 동일한지 알아보기위하여, 다음과 같이 가설을 세우자.
      												`𝐻0: 𝜇B = 𝜇F vs 𝐻1: 𝜇B ≠ 𝜇F`
```

```markdown
표본크기는 각각 𝑛1,=12 𝑛2 =12이고, 표본평균은 𝑥ҧ = 15.33333,𝑦ത = 16.66667이고,
표본표준편차는 𝑠𝑋 =4.271115, 𝑠𝑌 =6.213378이다.
등분산검정에 대한 유의확률 𝑝 =0.2294
가 유의수 준 𝛼 = 0.05보다 크므로, `등분산`이다.

등분산 T-검정을 이용하여 계산한 평균차이 (𝜇𝐵 − 𝜇𝐹 ) 에 대한 95% 신뢰구간은 (-5.847224, 3.180557) 이고, 
검정통계량은 𝑇 = -0.61259이며, 유의확률 은 𝑝 = 0.5464 이다. 
따라서 유의수준 0.05 에서 `귀무가설을 기각한다`. 

즉, 유의수준 0.05에서 살충제 B와 F의 효과는 같다.
```

### R코드

```R
> x <- InsectSprays[InsectSprays$spray == 'B', "count"]
> x
 [1] 11 17 21 11 16 14 17 17 19 21  7 13
> y <- InsectSprays[InsectSprays$spray == 'F', "count"]
> y
 [1] 11  9 15 22 15 16 13 10 26 26 24 13
```

```R
> aggregate(count~spray, data = xy, sd) # 표준편차
  spray    count
1     B 4.271115
2     F 6.213378
```

```R
> aggregate(count~spray, data = xy, mean) # 표본평균
  spray    count
1     B 15.33333
2     F 16.66667
```

```R
> count <- c(x, y)
> count
 [1] 11 17 21 11 16 14 17 17 19 21  7 13 11  9 15 22 15 16
[19] 13 10 26 26 24 13
> spray <- c(rep('B', length(x)), rep('F', length(y)))
> spray
 [1] "B" "B" "B" "B" "B" "B" "B" "B" "B" "B" "B" "B" "F"
[14] "F" "F" "F" "F" "F" "F" "F" "F" "F" "F" "F"
```

```R
> xy <- data.frame(count, spray)
> xy
   count spray
1     11     B
2     17     B
3     21     B
4     11     B
5     16     B
6     14     B
7     17     B
8     17     B
9     19     B
10    21     B
11     7     B
12    13     B
13    11     F
14     9     F
15    15     F
16    22     F
17    15     F
18    16     F
19    13     F
20    10     F
21    26     F
22    26     F
23    24     F
24    13     F
```

```R
> boxplot(count~spray, data = xy) # 위의 박스도표
```

```R
> var.test(x, y) # 등분산확률

	F test to compare two variances

data:  x and y
F = 0.47253, num df = 11, denom df = 11, p-value =
0.2294
alternative hypothesis: true ratio of variances is not equal to 1
95 percent confidence interval:
 0.1360301 1.6414182
sample estimates:
ratio of variances 
         0.4725275 
```

```R
> t.test(x, y, var.equal = TRUE)  # 등분산 T-검정

	Two Sample t-test

data:  x and y
t = -0.61259, df = 22, p-value = 0.5464
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -5.847224  3.180557
sample estimates:
mean of x mean of y 
 15.33333  16.66667 
```

