---
title: 08. Data Structure_Internal Sorting and Graph
description: "자료구조 13~14주차: 내부 정렬(버블, 삽입, 퀵, 병합, 힙, 기수)의 원리와 비교 횟수 실측, 인접 리스트 기반 그래프 세 가지 구현(A, B, C), 간선을 정렬된 위치에 삽입해 연결 노드를 오름차순으로 출력하기, DFS와 BFS"
Author: YoungHoon Ko
date: 2025-06-05 18:00:00 +0900
categories: [University(2025-First-Semester), Data Structure]
tags: [c, data-structure, sorting, graph]
math: true
image: /assets/img/lang/C.png
---

## 학습 목표

- 내부 정렬 6가지(버블, 삽입, 퀵, 병합, 힙, 기수)의 아이디어
- 버블 정렬의 pass 과정 손으로 따라가기
- 같은 데이터로 정렬별 비교 횟수 비교하기
- 그래프 용어와 인접 리스트 표현
- 14주차 실습: 연결 노드를 오름차순으로 출력하도록 AddEdge 고치기
- 깊이 우선 탐색(DFS)과 너비 우선 탐색(BFS) (보충)

---

## 1. 내부 정렬

**내부 정렬(internal sorting)**: 정렬할 데이터가 모두 메모리(배열) 안에 있는 경우. 데이터가 커서 디스크를 써야 하면 외부 정렬이다.

| 정렬 | 아이디어 | 평균 | 최악 | 안정 |
| --- | --- | --- | --- | --- |
| 버블 | 이웃끼리 비교해 교환, 한 pass마다 가장 큰(작은) 값이 끝으로 | $$O(n^2)$$ | $$O(n^2)$$ | O |
| 삽입 | 앞쪽 정렬된 부분에 새 원소를 끼워 넣음 | $$O(n^2)$$ | $$O(n^2)$$ | O |
| 퀵 | 피벗 기준으로 작은 쪽, 큰 쪽으로 나누고 재귀 | $$O(n \log n)$$ | $$O(n^2)$$ | X |
| 병합 | 반으로 나눠 정렬한 뒤 합침 | $$O(n \log n)$$ | $$O(n \log n)$$ | O |
| 힙 | 최대 힙을 만들고 루트(최댓값)를 끝으로 보내기 반복 | $$O(n \log n)$$ | $$O(n \log n)$$ | X |
| 기수 | 자릿수별로 버킷에 나눠 담기 (비교하지 않음) | $$O(dn)$$ | $$O(dn)$$ | O |

- 안정(stable): 값이 같은 원소의 원래 순서가 유지되는지
- 삽입 정렬은 거의 정렬된 데이터에서 $$O(n)$$에 가깝게 빨라진다

### 버블 정렬 1단계 pass

강의 슬라이드는 이웃한 두 값을 비교해 **앞이 작으면 교환**하는 내림차순 버블 정렬을 보여 준다. 1 pass가 끝나면 가장 작은 값이 맨 끝으로 간다.

```c
for (int j = 0; j < n - 1; j++)
    if (A[j] < A[j + 1]) {           /* 내림차순: 앞이 작으면 교환 */
        temp = A[j]; A[j] = A[j + 1]; A[j + 1] = temp;
    }
```

`{5, 11, 14, 6, 8, 10, 9, 12}`로 1 pass를 돌리면

```text
내림차순 1pass: 11 14  6  8 10  9 12  5
```

가장 작은 5가 끝으로 갔다. n개를 정렬하려면 이 pass를 n-1번 반복하고, i번째 pass는 이미 자리 잡은 끝 i개를 빼고 비교한다.

### 같은 데이터로 비교 횟수 실측

```text
원본      :  5 11 14  6  8 10  9 12
bubble    :  5  6  8  9 10 11 12 14   (비교 28회)
insertion :  5  6  8  9 10 11 12 14   (비교 17회)
quick     :  5  6  8  9 10 11 12 14   (비교 16회)
merge     :  5  6  8  9 10 11 12 14   (비교 17회)
heap      :  5  6  8  9 10 11 12 14   (비교 26회)
```

- 버블 정렬은 데이터와 상관없이 항상 $$\frac{n(n-1)}{2} = 28$$번 비교한다 (조기 종료를 넣지 않은 경우)
- n = 8은 너무 작아서 $$O(n^2)$$과 $$O(n \log n)$$의 차이가 크게 드러나지 않는다. n이 커질수록 차이가 벌어진다
- 위 결과는 각 정렬을 C로 직접 구현해 비교 연산마다 카운터를 올려 얻은 값이다(퀵 정렬은 마지막 원소를 피벗으로 사용)

### 퀵 정렬과 병합 정렬의 핵심

```c
/* 퀵 정렬: 피벗(마지막 원소)보다 작은 값을 앞으로 모은 뒤 피벗을 그 사이에 둔다 */
void quick(int a[], int lo, int hi) {
    if (lo >= hi) return;
    int pv = a[hi], i = lo - 1;
    for (int j = lo; j < hi; j++)
        if (a[j] < pv) swap(&a[++i], &a[j]);
    swap(&a[i + 1], &a[hi]);
    quick(a, lo, i);
    quick(a, i + 2, hi);
}

/* 병합 정렬: 반씩 정렬한 뒤 두 정렬된 배열을 합친다 */
void msort(int a[], int lo, int hi) {
    if (lo >= hi) return;
    int m = (lo + hi) / 2;
    msort(a, lo, m);
    msort(a, m + 1, hi);
    merge(a, lo, m, hi);
}
```

> **(중요!!)** 퀵 정렬은 평균 $$O(n \log n)$$이지만, 이미 정렬된 배열에서 끝 원소를 피벗으로 고르면 매번 한쪽으로만 나뉘어 $$O(n^2)$$이 된다. 병합 정렬은 항상 $$O(n \log n)$$이지만 추가 배열이 필요하다.
{: .prompt-warning }

---

## 2. 그래프

| 용어 | 뜻 |
| --- | --- |
| 정점(vertex, node) | 점 |
| 간선(edge) | 두 정점을 잇는 선. 방향이 없으면 무방향 그래프 |
| 인접(adjacent) | 간선으로 바로 연결된 관계 |
| 차수(degree) | 한 정점에 연결된 간선 수 |

표현 방법

| | 인접 행렬 | 인접 리스트 |
| --- | --- | --- |
| 구조 | $$V \times V$$ 2차원 배열 | 정점마다 연결 리스트 |
| 공간 | $$O(V^2)$$ | $$O(V + E)$$ |
| 두 정점이 연결됐는지 | $$O(1)$$ | 리스트를 따라가야 함 |
| 적합한 경우 | 간선이 많은(조밀한) 그래프 | 간선이 적은(희소한) 그래프 |

### 실습 그래프

정점 0~9, 간선 13개의 무방향 그래프.

```text
0-1, 0-2, 0-4, 2-3, 4-6, 4-7, 5-7, 5-8, 5-9, 6-9, 7-8, 7-9, 8-9
```

### 제공된 세 가지 구현

| 코드 | 인접 리스트 표현 | 간선 추가 위치 |
| --- | --- | --- |
| graphA | `Node **adjList` (포인터 배열) | 리스트 끝 |
| graphB | `List *adjList` (head를 가진 구조체 배열) | 리스트 끝 |
| graphC | `List *adjList` | 리스트 앞 (출력이 역순) |

---

## 3. 14주차 실습: 연결 노드를 오름차순으로

> 그래프를 출력할 때 각 정점에 연결된 노드들이 **오름차순으로** 출력되도록 기존 코드를 수정하시오. (B 선택)
{: .prompt-info }

원래 graphB는 간선을 **오름차순 순서대로 추가했기 때문에** 우연히 정렬되어 보였다. 간선을 임의 순서로 넣으면 바로 드러난다.

```c
AddEdge(g, 5, 9); AddEdge(g, 0, 4); AddEdge(g, 7, 9); AddEdge(g, 0, 2); AddEdge(g, 2, 3);
AddEdge(g, 5, 7); AddEdge(g, 5, 8); AddEdge(g, 4, 6); AddEdge(g, 0, 1); AddEdge(g, 6, 9);
AddEdge(g, 4, 7); AddEdge(g, 7, 8); AddEdge(g, 8, 9);
```

원래 graphB(끝에 추가)로 실행한 결과

```text
Node 0 connects to: 4, 2, 1,
Node 5 connects to: 9, 7, 8,
Node 7 connects to: 9, 5, 4, 8,
Node 9 connects to: 5, 7, 6, 8,
```

(일부 정점만 표시) 넣은 순서대로 연결되어 정렬되지 않는다.

### 수정: 정렬 삽입

3장(단일 연결 리스트)의 **정렬 위치 삽입**을 그대로 쓴다. 간선은 무방향이므로 양쪽 리스트에 모두 넣는다.

```c
void insertSorted(List *list, int id) {
    Node *newNode = (Node *)malloc(sizeof(Node));
    newNode->nodeId = id;
    newNode->next = NULL;
    if (list->head == NULL || list->head->nodeId > id) {   /* 맨 앞 */
        newNode->next = list->head;
        list->head = newNode;
        return;
    }
    Node *temp = list->head;                               /* 중간 또는 끝 */
    while (temp->next != NULL && temp->next->nodeId < id)
        temp = temp->next;
    newNode->next = temp->next;
    temp->next = newNode;
}

void AddEdge(Graph *g, int fromNode, int toNode) {        /* 무방향 */
    insertSorted(&g->adjList[fromNode], toNode);
    insertSorted(&g->adjList[toNode], fromNode);
}
```

제출한 코드는 같은 삽입 코드를 `fromNode`, `toNode`에 대해 두 번 적었는데, 위처럼 함수 하나로 묶으면 중복이 사라진다(동작은 같다).

실행 결과 (임의 순서로 간선 추가)

```text
--------------------------------------
Node 0 connects to: 1, 2, 4
Node 1 connects to: 0
Node 2 connects to: 0, 3
Node 3 connects to: 2
Node 4 connects to: 0, 6, 7
Node 5 connects to: 7, 8, 9
Node 6 connects to: 4, 9
Node 7 connects to: 4, 5, 8, 9
Node 8 connects to: 5, 7, 9
Node 9 connects to: 5, 6, 7, 8
--------------------------------------
```

- 간선 하나를 넣을 때 리스트를 따라가므로 차수 d인 정점에서 $$O(d)$$. 모든 간선을 넣고 나서 리스트마다 한 번 정렬하는 방법도 있다
- 출력 함수는 `printf("%d, ", ...)`로 찍은 뒤 `printf("\b\b \n")`로 마지막 쉼표를 지운다. 터미널에서는 지워져 보이지만, 파일로 저장하면 백스페이스 문자(`^H`)가 그대로 남는다. 첫 원소 앞에만 쉼표를 빼는 방식이 더 안전하다

### DFS와 BFS (보충)

같은 그래프를 0번에서 시작해 탐색하면(번호가 작은 이웃부터 방문)

```text
DFS(0): 0 1 2 3 4 6 9 5 7 8
BFS(0): 0 1 2 4 3 6 7 9 5 8
```

| | DFS (깊이 우선) | BFS (너비 우선) |
| --- | --- | --- |
| 자료구조 | 스택(또는 재귀) | 큐 |
| 방문 순서 | 한 방향으로 끝까지 간 뒤 되돌아옴 | 가까운 정점부터 차례로 |
| 활용 | 미로 탐색, 연결 요소 | 최단 경로(간선 수 기준) |

6장에서 배운 스택과 큐가 그래프 탐색의 핵심 도구가 된다.

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| Internal Sorting 슬라이드 | 버블 정렬 그림은 8개 값(12, 9, 10, 8, 6, 14, 11, 5)인데 인덱스는 A[0]~A[6] | 원소 8개면 A[0]~A[7] | 개수와 인덱스가 맞지 않음. 코드 슬라이드는 12가 빠진 7개 |
| Internal Sorting 슬라이드 코드 | `Include <stdio.h>`, `void main()`, `for i = 0 to ___` | `#include`, `int main(void)`, C 문법의 for | 의사코드와 C가 섞임 |
| 14주차 보고서 | "리스트 포인터 내부 포인터를 사용함" | head 포인터를 가진 List 구조체 배열 | 표현 보완 |
| graphB 원본 주석 | `// fromNode아 연결된 노드 연결 끝` | fromNode와 | 오타 |

---

## 최종 핵심 정리

1. 내부 정렬은 모든 데이터가 메모리에 있을 때의 정렬이다
2. 버블·삽입 정렬은 $$O(n^2)$$, 퀵·병합·힙 정렬은 평균 $$O(n \log n)$$이다
3. 버블 정렬 한 pass가 끝나면 가장 크거나 작은 값 하나가 끝자리에 고정된다
4. 퀵 정렬은 피벗 선택이 나쁘면 $$O(n^2)$$, 병합 정렬은 항상 $$O(n \log n)$$이지만 추가 공간이 든다
5. 그래프는 인접 행렬($$O(V^2)$$)이나 인접 리스트($$O(V+E)$$)로 표현한다
6. 무방향 그래프의 간선은 양쪽 정점의 리스트에 모두 넣는다
7. 연결 노드를 오름차순으로 유지하려면 연결 리스트의 정렬 위치 삽입을 쓴다
8. DFS는 스택(재귀), BFS는 큐로 구현한다

---

End.
