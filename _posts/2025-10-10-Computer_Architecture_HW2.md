---
title: 02. Computer_Architecture_HW2 (Procedure Call and Stack)
description: "컴퓨터구조 과제 2: C 함수 leaf_example(f = g - h*i - j)을 MIPS 프로시저로 구현. 인자 레지스터 $a0~$a3, jal/jr $ra, 스택에 레지스터 저장·복원 과정을 SPIM 실행 결과와 함께 정리"
Author: YoungHoon Ko
date: 2025-10-10 16:12:32 +0900
categories: [University(2025-Second-Semester), Computer Architecture]
tags: [assembly, mips_programming]
image: /assets/img/lang/asm.png
---

## 학습 목표

- C 함수를 MIPS **프로시저**(procedure)로 옮기는 방법
- 인자 전달(`$a0`~`$a3`), 반환값(`$v0`), 복귀 주소(`$ra`)의 역할
- `jal`로 호출하고 `jr $ra`로 돌아오는 흐름
- 스택 포인터 `$sp`를 줄여 레지스터를 저장하고 복원하는 이유
- caller-saved(`$t`)와 callee-saved(`$s`) 레지스터의 차이

---

## 1. 과제 내용

> **과제 2**: 아래 C 함수를 MIPS로 작성하라. `main`과 `leaf_example` 프로시저로 구성하고, `main`에서 g = 150, h = 5를 `$a0`, `$a1`에 넣어 호출한다. i, j는 입력받고, 결과 출력은 호출이 끝난 뒤 `main`에서 한다.
{: .prompt-info }

```c
int leaf_example(int g, int h, int i, int j)
{
    int f;
    f = g - (h * i) - j;
    return f;
}
```

요구 출력 형식 (학번은 입력이 아니라 문자열로 출력)

```text
Type a number for i: 20
Type a number for j: 30
The final result of Bxxxxxx is 20
```

- 150 − 5 × 20 − 30 = 20
- 과제 문서의 주의 사항: `j`는 점프 명령어 이름과 같아서 라벨이나 전역 변수 이름으로 쓰면 오류가 난다. 그래서 코드에서는 `prompt_j`처럼 이름을 붙였다

---

## 2. MIPS 프로시저 호출 규칙

| 레지스터 | 용도 | 누가 보존? |
| --- | --- | --- |
| `$a0`~`$a3` | 인자 4개 | 보존 안 함 |
| `$v0`, `$v1` | 반환값 | 보존 안 함 |
| `$ra` | 복귀 주소 (`jal`이 PC + 4를 저장) | 호출하는 쪽 |
| `$t0`~`$t9` | 임시 값 | 호출하는 쪽(caller)이 필요하면 저장 |
| `$s0`~`$s7` | 오래 쓰는 값 | 호출된 쪽(callee)이 쓰기 전에 저장, 끝나면 복원 |
| `$sp` | 스택의 맨 위 주소 | 들어온 값 그대로 돌려줘야 함 |

- `jal leaf_example`: `$ra ← PC + 4`로 돌아올 주소를 저장하고 함수로 점프
- `jr $ra`: 저장해 둔 주소로 돌아감
- 스택은 **높은 주소에서 낮은 주소로** 자란다. 그래서 공간을 만들 때 `addi $sp, $sp, -12`처럼 빼 준다

---

## 3. 코드

```assembly
# 컴퓨터 구조 HW2 Mips 실습
# 작성자: 고영훈
# 날짜: 2025/10/01

.data
    prompt_i: .asciiz "Type a number for i: "   # j를 입력 받을 때 출력할 문자열
    prompt_j: .asciiz "Type a number for j: "   # j를 입력 받을 때 출력할 문자열
    rst_1: .asciiz "The final result of "
    stdnt_id: .asciiz "A12345"               # 학번
    rst_2: .asciiz " is "
    newline: .asciiz "\n"
    buffer: .space 20     # 문자열 입력 받을 버퍼(20바이트 크기)
    g_val: .word 150
    h_val: .word 5

.text

main:
    # 두 개의 정수 i, j 입력
    # 질문 출력 
    li $v0, 4 # print string
    la $a0, prompt_i
    syscall

    # 정수i 입력
    li $v0, 5 # 5번이 정수를 입력받음(li는 바로 불러오기임)
    syscall
    move $t0, $v0 # 입력 받은 정수를 $t0에 넣음

    # 줄바꿈
    li $v0,4 
    la $a0,newline
    syscall

    # 질문 출력 
    li $v0, 4
    la $a0, prompt_j
    syscall

    # 정수j 입력
    li $v0, 5
    syscall
    move $t1, $v0

    # 인자 전달 -- 이걸 안 해서 계속 틀렸었음
    lw $a0, g_val      # g
    lw $a1, h_val      # h
    move $a2, $t0      # i
    move $a3, $t1      # j

    # leaf_example 함수 호출
    jal leaf_example
    move $s0, $v0 # leaf_example 함수의 반환값을 $s0에 저장

    # 줄바꿈
    li $v0,4 
    la $a0,newline
    syscall

    # 결과 출력
    ## "The final result of " 출력
    li $v0, 4
    la $a0, rst_1
    syscall

    ## 학번 출력
    li $v0, 4
    la $a0, stdnt_id
    syscall

    ## " is " 출력
    li $v0, 4
    la $a0, rst_2
    syscall

    ## 결과값 출력
    li $v0, 1 # print integer
    move $a0, $s0
    syscall 

    # 코드 종료
    li $v0, 10 # exit
    syscall


# ======================== Functions ========================

leaf_example:
    # 변수 백업
    addi $sp, $sp, -12
    sw $t1, 8($sp)
    sw $t0, 4($sp)
    sw $s0, 0($sp)
    
    # f값 계산
    mul $t1, $a1, $a2 # h * i
    sub $t0, $a0, $t1 # g - (h * i)
    sub $s0, $t0, $a3 # f = g - (h * i) - j

    # 결과 저장
    move $v0, $s0

    # 변수 복원
    lw $t1, 8($sp)
    lw $t0, 4($sp)
    lw $s0, 0($sp)
    addi $sp, $sp, 12

    # 복귀
    jr $ra

# ======================== Functions ========================


# ======================== End ========================
```

### leaf_example의 스택 변화

| 단계 | 명령어 | 스택 상태 |
| --- | --- | --- |
| 진입 | `addi $sp, $sp, -12` | 3워드(12바이트) 확보 |
| 저장 | `sw $t1, 8($sp)` / `sw $t0, 4($sp)` / `sw $s0, 0($sp)` | 원래 값 백업 |
| 계산 | `mul`, `sub`, `sub` | `$s0` = f |
| 반환 | `move $v0, $s0` | 결과를 `$v0`에 |
| 복원 | `lw` 3번 + `addi $sp, $sp, 12` | `$sp`가 들어올 때 값으로 돌아감 |

- 교과서(Patterson & Hennessy)의 `leaf_example`도 `$t1`, `$t0`, `$s0` 세 개를 저장한다. 엄밀히는 `$t` 레지스터는 호출된 쪽이 보존할 의무가 없어서 `$s0`만 저장해도 된다
- 반대로 `leaf_example` 안에서 `$t` 레지스터만 쓰면 스택 작업이 아예 필요 없다. 다른 함수를 부르지 않는 **리프(leaf) 프로시저**라서 `$ra`도 저장할 필요가 없다

---

## 4. 실행 결과 (SPIM)

입력값은 프롬프트 뒤에 직접 적었다. 학번은 공개용이라 `A12345`로 바꿨다.

```text
Type a number for i: 20

Type a number for j: 30

The final result of A12345 is 20
```

| i | j | 계산 | 출력 |
| --- | --- | --- | --- |
| 20 | 30 | 150 − 100 − 30 | 20 |
| 0 | 0 | 150 − 0 − 0 | 150 |
| 1 | 2 | 150 − 5 − 2 | 143 |
| −3 | 4 | 150 + 15 − 4 | 161 |
| 30 | 0 | 150 − 150 − 0 | 0 |

모두 C 식과 같은 값이 나왔다.

> 처음에 계속 틀렸던 이유(코드 주석): 입력받은 i, j를 `$t0`, `$t1`에만 두고 **`$a2`, `$a3`로 옮기지 않은 채** 호출했다. 프로시저는 인자 레지스터만 보므로, 호출 전에 `move $a2, $t0` / `move $a3, $t1`이 꼭 필요하다.
{: .prompt-warning }

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 이 글의 이전 버전 문제 | "The final result is 20" | "The final result of (학번) is 20" | 과제 문서의 출력 형식 (코드는 원래 맞게 출력함) |
| 이전 버전 결과 그림 | 이미지 앞에 백틱이 붙어 그림이 안 보임 | 실행 결과를 텍스트로 정리 | 마크다운 문법 오류 |

---

## 최종 핵심 정리

1. 인자는 `$a0`~`$a3`, 반환값은 `$v0`, 복귀 주소는 `$ra`에 담는다
2. `jal`은 `$ra ← PC + 4` 후 점프, `jr $ra`는 그 주소로 복귀한다
3. 스택은 아래(낮은 주소)로 자라므로 `$sp`를 빼서 공간을 만들고, 끝나면 같은 만큼 더해 원래대로 돌린다
4. `$s` 레지스터는 호출된 쪽이 보존해야 하므로 쓰기 전에 스택에 저장하고 복귀 전에 복원한다
5. `$t` 레지스터는 호출하는 쪽 책임이라, 리프 함수에서는 `$t`만 쓰면 스택 작업 없이 끝낼 수 있다
6. 호출 전에 입력값을 인자 레지스터로 옮겨야 한다. 이걸 빼먹는 것이 가장 흔한 실수
7. `j`는 명령어 이름이라 라벨·변수 이름으로 쓰지 않는다
8. SPIM 실행 결과 150 − 5 × 20 − 30 = 20을 포함한 5가지 입력이 모두 C 식과 같았다

---

End.
