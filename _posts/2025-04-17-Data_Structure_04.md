---
title: 04. Data Structure_Doubly Linked List
description: "자료구조 7주차: 이중 연결 리스트(Dnode의 left, right 포인터) 구축, 왕복 출력과 합, 정렬 위치 삽입(맨 앞, 맨 뒤, 중간 4단계)과 삭제(맨 앞, 맨 뒤, 중간), 강의 슬라이드 빈칸 정답과 오류 정정"
Author: YoungHoon Ko
date: 2025-04-17 18:00:00 +0900
categories: [University(2025-First-Semester), Data Structure]
tags: [c, data-structure, linked-list]
image: /assets/img/lang/C.png
---

## 학습 목표

- 이중 연결 리스트 노드(`left`, `data`, `right`) 구조
- 이중 연결 리스트 구축과 앞→뒤→앞 왕복 출력
- 왕복하며 노드 값의 합 구하기
- 맨 앞, 맨 뒤, 중간 삽입에서 바꿔야 하는 포인터 개수
- 맨 앞, 맨 뒤, 중간 삭제
- 단일 연결 리스트와 이중 연결 리스트 비교

---

## 1. 이중 연결 리스트

단일 연결 리스트는 뒤로만 갈 수 있어서, 삭제할 때 앞 노드를 따로 기억해야 했다. **이중 연결 리스트(double linked list)**는 노드가 앞뒤 주소를 모두 가진다.

```c
typedef struct dnode {
    int data;
    struct dnode *Lptr;   /* 왼쪽(이전) 노드 */
    struct dnode *Rptr;   /* 오른쪽(다음) 노드 */
} dnode;
```

```text
NULL ← [10] ⇄ [20] ⇄ [30] ⇄ [40] ⇄ [50] → NULL
        head
```

| | 단일 연결 리스트 | 이중 연결 리스트 |
| --- | --- | --- |
| 노드당 포인터 | 1개 | 2개 (메모리 더 사용) |
| 이동 방향 | 앞→뒤만 | 양방향 |
| 노드 하나를 알 때 삭제 | 앞 노드를 다시 찾아야 함 | 그 노드만으로 $$O(1)$$ |
| 삽입 시 바꾸는 포인터 | 2개 | 최대 4개 |

---

## 2. 7주차 실습

> 1) 10, 20, 30, 40, 50 이중 연결 리스트를 구축하시오
> 2) `10 -> 20 -> 30 -> 40 -> 50 -> 40 -> 30 -> 20 -> 10`으로 출력하시오
> 3) 위 순서대로 합을 구하시오
> 4) 5(맨 앞), 60(맨 뒤), 25(중간)를 삽입하시오
> 5) 맨 앞, 맨 뒤, 중간(Key)을 삭제하시오
{: .prompt-info }

### 1) 구축

새 노드 p를 만들 때마다 **양쪽 연결** 두 줄이 필요하다.

```c
for (int i = 1; i < 6; i++) {
    p = (dnode *)malloc(sizeof(dnode));
    p->data = i * 10;
    p->Lptr = NULL;
    p->Rptr = NULL;
    if (head == NULL) {
        head = temp = p;
    } else {
        temp->Rptr = p;   /* 앞 노드 → 새 노드 */
        p->Lptr = temp;   /* 새 노드 → 앞 노드 */
        temp = p;         /* temp 이동 */
    }
}
```

### 2) 왕복 출력과 3) 합

오른쪽 끝까지 가면서 **마지막 노드를 기억**해 두었다가, 거기서 왼쪽으로 돌아온다. 마지막 노드는 이미 출력했으므로 `last->Lptr`부터 시작한다.

```c
int getSum(dnode *head) {
    int sum = 0;
    dnode *temp = head, *last = NULL;
    while (temp != NULL) {          /* 앞 → 뒤 */
        sum += temp->data;
        last = temp;                /* 마지막 노드 기억 */
        temp = temp->Rptr;
    }
    for (temp = last->Lptr; temp != NULL; temp = temp->Lptr)   /* 뒤 → 앞 */
        sum += temp->data;
    return sum;
}
```

### 4) 정렬 위치 삽입

```c
void dnode_Insert(dnode **head, int a) {
    dnode *p = (dnode *)malloc(sizeof(dnode));
    p->data = a;
    p->Lptr = p->Rptr = NULL;

    if (*head == NULL || (*head)->data > a) {    /* 맨 앞 */
        p->Rptr = *head;
        if (*head != NULL) (*head)->Lptr = p;
        *head = p;
        return;
    }
    dnode *temp = *head;
    while (temp->Rptr != NULL && temp->Rptr->data < a)
        temp = temp->Rptr;

    p->Rptr = temp->Rptr;                         /* ① 새 노드 → 뒤 */
    p->Lptr = temp;                               /* ② 새 노드 → 앞 */
    if (temp->Rptr != NULL) temp->Rptr->Lptr = p; /* ③ 뒤 노드 → 새 노드 (맨 뒤면 생략) */
    temp->Rptr = p;                               /* ④ 앞 노드 → 새 노드 */
}
```

> **(중요!!)** 중간 삽입에서 ④ `temp->Rptr = p`를 ①, ③보다 먼저 하면 원래 뒤 노드(30)의 주소를 잃는다. **새 노드의 포인터를 먼저 채우고, 기존 노드의 포인터는 마지막에** 바꾼다.
{: .prompt-warning }

### 5) 삭제

이중 연결 리스트는 지울 노드 자체만 찾으면 앞뒤를 모두 알 수 있다.

```c
void dnode_Delete(dnode **head, int a) {
    if (*head == NULL) return;
    dnode *temp = *head;

    if (temp->data == a) {                    /* 맨 앞 */
        *head = temp->Rptr;
        if (*head != NULL) (*head)->Lptr = NULL;
        free(temp);
        return;
    }
    while (temp != NULL && temp->data != a) temp = temp->Rptr;
    if (temp == NULL) return;                 /* 없음 */

    if (temp->Lptr != NULL) temp->Lptr->Rptr = temp->Rptr;   /* 앞 → 뒤 */
    if (temp->Rptr != NULL) temp->Rptr->Lptr = temp->Lptr;   /* 뒤 → 앞 (맨 뒤면 생략) */
    free(temp);
}
```

### 실행 결과

```text
====================================================
1) 구축
10 => 20 => 30 => 40 => 50

2) 출력
10 => 20 => 30 => 40 => 50 => 40 => 30 => 20 => 10

3) 노드들의 합
10 + 20 + 30 + 40 + 50 + 40 + 30 + 20 + 10 = 250

4-1) 맨앞 5 삽입
5 => 10 => 20 => 30 => 40 => 50

4-2) 맨뒤 60 삽입
5 => 10 => 20 => 30 => 40 => 50 => 60

4-3) 중간 25 삽입
5 => 10 => 20 => 25 => 30 => 40 => 50 => 60

5-1) 맨앞 5 삭제
10 => 20 => 25 => 30 => 40 => 50 => 60

5-2) 맨뒤 60 삭제
10 => 20 => 25 => 30 => 40 => 50

5-3) 중간 30 삭제
10 => 20 => 25 => 40 => 50
====================================================
```

---

## 3. 강의 슬라이드 빈칸 정답

| 문제 | 정답 |
| --- | --- |
| 구축 (빨간·파란 화살표) | `temp->right = p;` `p->left = temp;` `temp = temp->right;` |
| 4-1) 맨 앞 삽입 | `p->right = head;` `head->left = p;` `head = p;` |
| 4-2) 맨 뒤 삽입 | `temp->right = p;` `p->left = temp;` |
| 4-3) 중간 삽입 4단계 | `p->right = temp->right;` `temp->right->left = p;` `temp->right = p;` `p->left = temp;` |
| 5-1) 맨 앞 삭제 | `head = temp->right;` `head->left = NULL;` `free(temp);` |
| 5-2) 맨 뒤 삭제 | `while (temp->right->right != NULL) temp = temp->right;` 후 `free(temp->right);` `temp->right = NULL;` |
| 5-3) 중간 삭제 | `q = temp->right;` `temp->right = q->right;` `q->right->left = temp;` `free(q);` |

슬라이드의 중간 삽입 순서(① `temp->right->left = p` ② `p->right = temp->right` ③ `temp->right = p` ④ `p->left = temp`)도 맞다. ①에서 30의 left만 바꾸고 `temp->right`(30의 주소)는 ③까지 유지되기 때문이다.

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 이중 연결 리스트 슬라이드 맨 뒤 삽입 | `2: temp = p -> left;` | `p->left = temp;` | 대입 방향이 반대. 원래대로면 temp가 NULL이 됨 |
| 맨 앞 삭제 | `heap = temp -> right;` `heap -> left = null;` | `head` | 오타 (7주차 슬라이드에서는 head로 고쳐져 있음) |
| 중간 삭제 | `2: temp->right->left = temp->right;` | `temp->right->left = temp;` | ①로 temp->right가 이미 다음 노드로 바뀌었으므로, 그 노드의 left는 temp를 가리켜야 함 |
| 중간 삭제 | free 없음 | 지운 노드를 `free` | 연결만 끊으면 메모리 누수 |
| 맨 뒤 삭제 | `temp-> right == null;` | `temp->right = NULL;` | `==`는 비교, `=`가 대입 |
| 맨 앞 삽입 (단일 리스트 슬라이드 복사) | `if (head->data > p-data) { p->right = temp; head = p; }` | `head->left = p;` 추가 | 이중 연결이므로 기존 head의 left도 바꿔야 함 |
| 구축 슬라이드 | `Struct Dnode`, `For`, `주소혛변수` | `struct`, `for`, `주소형 변수` | C는 대소문자 구분, 오타 |

---

## 최종 핵심 정리

1. 이중 연결 리스트 노드는 left, data, right를 가져 양방향으로 이동한다
2. 구축할 때는 앞 노드의 right와 새 노드의 left, 두 연결을 모두 만든다
3. 왕복 출력은 끝 노드를 기억한 뒤 `last->Lptr`부터 왼쪽으로 돌아온다
4. 10~50 왕복 합은 10+20+30+40+50+40+30+20+10 = 250이다
5. 중간 삽입은 포인터 4개를 바꾸며, 새 노드 쪽을 먼저 채우고 기존 연결은 나중에 바꾼다
6. 맨 앞·맨 뒤 처리에서는 NULL인 쪽 포인터를 건드리지 않도록 조건을 건다
7. 삭제는 지울 노드만 찾으면 `Lptr->Rptr`, `Rptr->Lptr` 두 줄로 끝나고 free한다
8. 이중 연결 리스트는 메모리를 더 쓰는 대신 노드를 알 때 삭제가 $$O(1)$$이다

---

End.
