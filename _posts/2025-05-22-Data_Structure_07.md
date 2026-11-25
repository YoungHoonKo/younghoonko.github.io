---
title: 07. Data Structure_Binary Tree and Binary Search Tree
description: "자료구조 12주차: 이진 트리의 정의와 종류(편향, 포화, 완전), 배열 표현의 인덱스 관계와 연결 표현, 이진 탐색 트리 구축, 전위·중위·후위 순회, 재귀로 높이 구하기, Full Binary Tree 판별과 실습 결과 정정"
Author: YoungHoon Ko
date: 2025-05-22 18:00:00 +0900
categories: [University(2025-First-Semester), Data Structure]
tags: [c, data-structure, tree]
math: true
image: /assets/img/lang/C.png
---

## 학습 목표

- 이진 트리의 정의와 편향·포화·완전 이진 트리
- 완전 이진 트리를 배열로 표현할 때의 부모·자식 인덱스
- 연결 표현(left, data, right)으로 이진 탐색 트리 구축하기
- 전위·중위·후위 순회와 결과 읽기
- 재귀로 트리의 높이 구하기
- Full Binary Tree 판별하기

---

## 1. 이진 트리

**이진 트리**: 노드의 유한 집합으로, 공백이거나 루트와 왼쪽 서브트리, 오른쪽 서브트리로 이루어진다. 각 노드의 자식은 최대 2개다.

| 용어 | 뜻 |
| --- | --- |
| 루트(root) | 맨 위 노드 |
| 리프(leaf, 단말 노드) | 자식이 없는 노드 |
| 높이(height) | 루트에서 가장 깊은 리프까지의 간선 수 (교재에 따라 노드 수로 세기도 함) |

### 종류

| 종류 | 특징 |
| --- | --- |
| 편향 이진 트리 | 모든 노드가 한쪽(왼쪽 또는 오른쪽)으로만 치우침. 사실상 연결 리스트 |
| 포화 이진 트리 (perfect) | 모든 레벨이 꽉 참. 높이 h면 노드 $$2^{h+1} - 1$$개 |
| 완전 이진 트리 (complete) | 마지막 레벨을 뺀 모든 레벨이 꽉 차고, 마지막 레벨은 왼쪽부터 채움 |
| 정 이진 트리 (full) | 모든 노드의 자식이 0개 또는 2개 |

### 배열 표현 (순차 표현)

완전 이진 트리는 루트를 1번으로 두고 레벨 순서로 번호를 매기면 배열에 빈칸 없이 들어간다. 노드가 n개일 때

| 목표 노드 | 인덱스 | 조건 |
| --- | --- | --- |
| 노드 i의 부모 | $$\lfloor i / 2 \rfloor$$ | $$i > 1$$ |
| 노드 i의 왼쪽 자식 | $$2i$$ | $$2i \le n$$ |
| 노드 i의 오른쪽 자식 | $$2i + 1$$ | $$2i + 1 \le n$$ |
| 루트 | 1 | $$n > 0$$ |

힙(heap)이 이 표현을 쓴다. 편향 트리를 배열로 저장하면 빈칸이 많이 생기므로 연결 표현을 쓴다.

### 연결 표현

```c
typedef struct _BT_node {
    int data;
    struct _BT_node *left;
    struct _BT_node *right;
} BT_node;
```

---

## 2. 12주차 실습

> 1. 트리를 구축하시오
> 2. 세 가지 순회를 작성하시오
> 3. 높이 Height(또는 깊이 Depth)를 구하시오
> 4. Full Binary Tree를 확인하시오
>
> 1, 2번은 코드만, 3, 4번은 문제 정의부터 테스트 케이스까지 작성
{: .prompt-info }

### 1. 이진 탐색 트리 구축

**이진 탐색 트리(BST)**: 모든 노드에서 왼쪽 서브트리 < 노드 < 오른쪽 서브트리. 4, 2, 5, 1, 3 순서로 넣으면

```text
        4
       / \
      2   5
     / \
    1   3
```

```c
BT_node *insert(BT_node *T, int data) {
    if (T == NULL) {                               /* 빈 자리에 새 노드 */
        T = (BT_node *)malloc(sizeof(BT_node));
        T->data = data;
        T->left = T->right = NULL;
    } else if (data < T->data) {
        T->left = insert(T->left, data);           /* 작으면 왼쪽 */
    } else if (data > T->data) {
        T->right = insert(T->right, data);         /* 크면 오른쪽 */
    } else {
        printf("같은 데이터가 존재합니다.\n");
    }
    return T;
}
```

### 2. 세 가지 순회

**루트를 언제 방문하느냐**로 이름이 정해진다.

| 순회 | 순서 | 결과 |
| --- | --- | --- |
| 전위(preorder) | 루트 → 왼쪽 → 오른쪽 | 4 2 1 3 5 |
| 중위(inorder) | 왼쪽 → 루트 → 오른쪽 | 1 2 3 4 5 |
| 후위(postorder) | 왼쪽 → 오른쪽 → 루트 | 1 3 2 5 4 |

```c
void preorder(BT_node *T)  { if (!T) return; printf("%d ", T->data); preorder(T->left); preorder(T->right); }
void inorder(BT_node *T)   { if (!T) return; inorder(T->left); printf("%d ", T->data); inorder(T->right); }
void postorder(BT_node *T) { if (!T) return; postorder(T->left); postorder(T->right); printf("%d ", T->data); }
```

> **(중요!!)** 이진 탐색 트리를 **중위 순회**하면 항상 오름차순으로 나온다. 수식 트리에서는 전위가 전위 표기식(prefix), 중위가 중위식, 후위가 후위 표기식(postfix)이 된다.
{: .prompt-warning }

### 3. 높이

트리의 높이 = 1 + (왼쪽 서브트리 높이와 오른쪽 서브트리 높이 중 큰 값). 빈 트리를 -1로 두면 리프의 높이가 0이 된다.

```c
int height(BT_node *T) {
    if (T == NULL) return -1;
    int lheight = height(T->left);
    int rheight = height(T->right);
    return (lheight > rheight ? lheight : rheight) + 1;
}
```

- 테스트: 루트 4 → 2 → 1(또는 3)까지 간선 2개 → **높이 2**
- 빈 트리를 0으로 두면 같은 트리의 높이가 3(노드 수 기준)으로 나온다. 어떤 정의를 쓰는지 문제에 맞춘다

### 4. Full Binary Tree 판별

모든 노드의 자식이 **0개 또는 2개**이면 Full Binary Tree. 자식이 1개인 노드가 하나라도 있으면 아니다.

```c
int isFullTree(BT_node *T) {
    if (T == NULL) return 1;
    if (T->left == NULL && T->right == NULL) return 1;           /* 리프 */
    if (T->left != NULL && T->right != NULL)                     /* 자식 2개 */
        return isFullTree(T->left) && isFullTree(T->right);
    return 0;                                                    /* 자식 1개 */
}
```

### 실행 결과

```text
----------------------------------------------------------
preorder 방식 : 4 2 1 3 5
inorder 방식 : 1 2 3 4 5
postorder 방식 : 1 3 2 5 4
트리의 높이(깊이) : 2
Full Binary Tree인가?: Yes
----------------------------------------------------------
```

노드 4(자식 2, 5), 노드 2(자식 1, 3)는 자식이 2개이고 1, 3, 5는 리프이므로 **Full Binary Tree가 맞다(Yes)**. 보고서의 출력 정의에는 "Full Binary Tree : No 출력"이라고 적혀 있었지만, 실제 실행 결과와 제출한 캡처 화면은 모두 Yes였다.

`isFullTree`가 No를 내는지 확인하려면 자식이 하나뿐인 노드를 만들면 된다. 예를 들어 6을 추가로 넣으면 노드 5가 오른쪽 자식 하나만 가지므로 No가 나온다.

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 12주차 4번 보고서 출력 정의 | `// Full Binary Tree : No 출력` | Yes | 4, 2, 5, 1, 3 트리는 모든 노드의 자식이 0개 또는 2개 (실행해서 확인) |
| 12주차 3번 보고서 전체 코드 | `height` 함수 뒤에 `return 0; }`가 한 번 더 있음 | 삭제 | 그대로 붙여 넣으면 컴파일 오류 |
| 12주차 최종 `.c` 파일 | `void inorder(BT_node* T);거지` | 끝의 글자 삭제 | 컴파일 오류 |
| 이진트리 슬라이드 연결 표현 | `Node left; Node Right;` | `struct Node *left, *right;` | 자기 자신을 값으로 품을 수 없고 포인터여야 함 |
| 12주차 코드 | 전역 `temp`로 재귀 결과를 받음 | `T->left = insert(T->left, data);` | 전역 변수가 필요 없음 |
| 이진트리 슬라이드 | 완전 이진트리: "노드의 수가 n<=()라면" | 마지막 레벨을 제외하고 꽉 차고 마지막 레벨은 왼쪽부터 채운 트리 | 식이 빠져 있어 정의를 보완 |

---

## 최종 핵심 정리

1. 이진 트리는 각 노드의 자식이 최대 2개인 트리이며 공백일 수도 있다
2. 편향 트리는 한쪽으로만 치우치고, 포화 트리는 모든 레벨이 꽉 찬다
3. 완전 이진 트리를 1번부터 배열에 저장하면 부모는 $$\lfloor i/2 \rfloor$$, 자식은 $$2i$$, $$2i+1$$이다
4. 이진 탐색 트리는 왼쪽 < 루트 < 오른쪽을 재귀적으로 만족한다
5. 순회 이름은 루트를 방문하는 시점(전·중·후)으로 정해지고, BST의 중위 순회는 오름차순이다
6. 높이는 1 + max(왼쪽 높이, 오른쪽 높이)이며 빈 트리를 -1로 두면 간선 수 기준이 된다
7. Full Binary Tree는 모든 노드의 자식이 0개 또는 2개인 트리다
8. 4, 2, 5, 1, 3으로 만든 트리는 높이 2인 Full Binary Tree다

---

End.
