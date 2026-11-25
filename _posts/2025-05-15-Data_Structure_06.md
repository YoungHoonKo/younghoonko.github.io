---
title: 06. Data Structure_Stack, Queue and Postfix Notation
description: "자료구조 10~11주차: 배열 기반 스택(push, pop, overflow, underflow), 연결 리스트 기반 스택, 배열 기반 선형 큐에서 빈칸이 있어도 가득 찼다고 나오는 문제와 원형 큐, 스택 응용인 중위 표기식의 후위 표기 변환과 계산"
Author: YoungHoon Ko
date: 2025-05-15 18:00:00 +0900
categories: [University(2025-First-Semester), Data Structure]
tags: [c, data-structure, stack, queue]
math: true
image: /assets/img/lang/C.png
---

## 학습 목표

- 스택(LIFO)과 큐(FIFO)의 개념
- 배열로 스택 구현: push, pop, 가득 참(overflow)과 빔(underflow)
- 연결 리스트로 스택 구현
- 배열로 큐 구현과 선형 큐의 한계
- 원형 큐(circular queue)로 빈칸 재사용하기
- 스택 응용: 중위 표기식을 후위 표기식으로 바꾸고 계산하기

---

## 1. 스택 (Stack)

**Last In First Out (LIFO)**, Last Come First Service. 마지막에 들어온 데이터를 제일 먼저 꺼낸다. 접시 쌓기, 함수 호출, 되돌리기(Ctrl+Z)가 모두 스택이다.

| 연산 | 동작 | 실패 조건 |
| --- | --- | --- |
| push | 맨 위(top)에 넣기 | 가득 차 있으면 **overflow** |
| pop | 맨 위에서 꺼내기 | 비어 있으면 **underflow** |

### 10주차 실습: 배열 기반 스택

> Size가 3인 배열 기반 Stack을 구현하세요. 10, 20, 30, 40을 순서대로 PUSH하고, POP을 4번 수행해 리턴값을 출력하세요. 문제 정의부터 테스트 케이스 작성까지 진행합니다.
{: .prompt-info }

`index`는 **마지막으로 넣은 칸**을 가리키고, 빈 스택은 `-1`에서 시작한다.

```c
#include <stdio.h>
#define SIZE 3

void push(int data, int stack[], int *index) {
    if (*index >= SIZE - 1) {
        printf("스택이 가득 찼습니다: %d 삽입 실패\n", data);
    } else {
        (*index)++;
        stack[*index] = data;
        printf("PUSH: %d\n", data);
    }
}

int pop(int stack[], int *index) {
    if (*index < 0) {
        printf("스택이 비었습니다.\n");
        return -1;
    }
    int value = stack[*index];
    (*index)--;
    return value;
}

int main() {
    int stack[SIZE];
    int index = -1;
    push(10, stack, &index);
    push(20, stack, &index);
    push(30, stack, &index);
    push(40, stack, &index);
    for (int i = 0; i < 4; i++) {
        int result = pop(stack, &index);
        if (result != -1) printf("POP 반환값: %d\n", result);
    }
    return 0;
}
```

실행 결과

```text
PUSH: 10
PUSH: 20
PUSH: 30
스택이 가득 찼습니다: 40 삽입 실패
POP 반환값: 30
POP 반환값: 20
POP 반환값: 10
스택이 비었습니다.
```

- `index`를 포인터로 넘겨야 push, pop이 main의 `index`를 바꿀 수 있다
- pop이 실패를 `-1`로 알리면, 스택에 실제로 `-1`이 들어 있을 때 구분이 안 된다. 실패 여부를 따로 돌려주는 편이 안전하다

### 강의 슬라이드 코드의 문제

슬라이드는 `index`를 **다음에 넣을 빈칸**으로 쓰면서 아래처럼 작성했다.

```c
void push(int data, int index) {      /* index를 값으로 받음 */
    if (index != Max) { stack[index] = data; index = index + 1; }
}
int pop() {
    if (index == Min) return ???;
    else { temp = stack[index]; index = index - 1; }   /* 순서가 반대 */
    return temp;
}
```

실제로 돌려 보면

```text
슬라이드 push 두 번 뒤 idx = 0, stack[0] = 20
슬라이드 pop: 0 (기대값 20)
```

- push: `index`가 복사본이라 main의 `index`는 계속 0 → 같은 칸에 덮어씀
- pop: `index`가 빈칸을 가리키므로 **먼저 줄이고 나서** 읽어야 함 → `index = index - 1; temp = stack[index];`

> **(중요!!)** top을 "마지막 원소"로 볼지 "다음 빈칸"으로 볼지 하나로 정하고, push/pop의 증감 순서를 거기에 맞춘다. 마지막 원소 방식은 push가 `++top` 후 저장, pop이 읽은 후 `top--`. 다음 빈칸 방식은 그 반대다.
{: .prompt-warning }

### 11주차 실습 1: 연결 리스트 기반 스택

> 연결 리스트 기반 스택을 구현하시오. 10~50을 PUSH → POP 2회(50, 40) → 다시 PUSH(40)
{: .prompt-info }

맨 앞(head)을 top으로 쓰면 push와 pop이 모두 $$O(1)$$이고 크기 제한이 없다.

```c
typedef struct Node { int data; struct Node *next; } Node;
Node *top = NULL;

void push(int value) {
    Node *newNode = (Node *)malloc(sizeof(Node));
    newNode->data = value;
    newNode->next = top;      /* 새 노드가 기존 top을 가리킴 */
    top = newNode;
    printf("Pushed: %d\n", value);
}

int pop(void) {
    if (top == NULL) { printf("스택이 비어있습니다.\n"); return -1; }
    Node *temp = top;
    int value = temp->data;
    top = top->next;
    free(temp);
    printf("Popped: %d\n", value);
    return value;
}
```

실행 결과 (구분선 생략)

```text
Pushed: 10
Pushed: 20
Pushed: 30
Pushed: 40
Pushed: 50
현재 스택 상태 (top → bottom): 50 40 30 20 10
Popped: 50
현재 스택 상태 (top → bottom): 40 30 20 10
Popped: 40
현재 스택 상태 (top → bottom): 30 20 10
Pushed: 40
현재 스택 상태 (top → bottom): 40 30 20 10
```

---

## 2. 큐 (Queue)

**First In First Out (FIFO)**. 먼저 들어온 데이터를 먼저 꺼낸다. 줄 서기, 프린터 대기열, 너비 우선 탐색(BFS)이 큐를 쓴다.

- `rear`(뒤)에서 넣고(enqueue), `front`(앞)에서 꺼낸다(dequeue)
- `front == rear`이면 비어 있다

### 11주차 실습 2: 배열 기반 큐

> 크기 5의 배열 큐: 10~40 삽입 → 삭제(10) → 50 삽입 → 삭제(20) → 60 삽입(에러)
{: .prompt-info }

```c
#define MAX 5
struct queue { int data[MAX]; int front; int rear; };

void enqueue(struct queue *q, int data) {
    if (q->rear == MAX) {
        printf("Trying to insert: %d\n", data);
        printf("queue is full\n");
    } else {
        q->data[q->rear++] = data;
        printf("queue 삽입: %d\n", data);
    }
}

int dequeue(struct queue *q) {
    if (q->rear == q->front) { printf("queue is empty\n"); return -1; }
    int data = q->data[q->front++];
    printf("dequeue: %d\n", data);
    return data;
}
```

실행 결과 (구분선 생략)

```text
queue 삽입: 10
queue 삽입: 20
queue 삽입: 30
queue 삽입: 40
현재 큐 상태: 10 20 30 40
dequeue: 10
현재 큐 상태: 20 30 40
queue 삽입: 50
현재 큐 상태: 20 30 40 50
dequeue: 20
현재 큐 상태: 30 40 50
Trying to insert: 60
queue is full
현재 큐 상태: 30 40 50
```

마지막에 원소는 3개뿐인데 "queue is full"이 나온다. `front`와 `rear`가 **앞으로만 움직여서** 앞쪽 두 칸(`data[0]`, `data[1]`)을 다시 쓰지 못하기 때문이다. 이것이 **선형 큐의 한계**이고, 실습의 테스트 케이스도 이 현상을 확인하도록 만들어져 있다.

### 원형 큐 (보충)

배열의 끝 다음을 처음으로 이어 붙인다. 인덱스를 `% MAX`로 돌린다.

- 초기: `front = rear = 0`
- 비었음: `front == rear`
- 가득 참: `(rear + 1) % MAX == front` (가득 참과 빔을 구분하려고 **한 칸은 비워 둔다**)

```c
void enqueue(CQueue *q, int x) {
    if ((q->rear + 1) % MAX == q->front) { printf("queue is full: %d 삽입 실패\n", x); return; }
    q->rear = (q->rear + 1) % MAX;
    q->data[q->rear] = x;
}
int dequeue(CQueue *q) {
    if (q->front == q->rear) { printf("queue is empty\n"); return -1; }
    q->front = (q->front + 1) % MAX;
    return q->data[q->front];
}
```

같은 테스트 케이스로 실행한 결과

```text
현재 큐 상태: 10 20 30 40
dequeue: 10
enqueue: 50
현재 큐 상태: 20 30 40 50
dequeue: 20
enqueue: 60
현재 큐 상태: 30 40 50 60
```

빈칸을 재사용해서 60이 들어간다. 크기 5인 배열이면 원소는 최대 4개까지 넣을 수 있다.

---

## 3. 스택 응용: 후위 표기식

| 표기 | 예 | 특징 |
| --- | --- | --- |
| 중위(infix) | `A + B * C` | 사람이 쓰는 방식, 괄호와 우선순위 필요 |
| 후위(postfix) | `A B C * +` | 괄호가 필요 없고, 스택 하나로 왼쪽부터 계산 가능 |

### 중위 → 후위 변환 규칙

1. 피연산자는 바로 출력
2. `(`는 스택에 넣음
3. `)`를 만나면 `(`가 나올 때까지 꺼내 출력
4. 연산자는 스택 top의 우선순위가 **같거나 높으면** 먼저 꺼내 출력한 뒤 넣음
5. 끝나면 스택에 남은 연산자를 모두 출력

```c
int prec(char op) { return (op == '*' || op == '/') ? 2 : (op == '+' || op == '-') ? 1 : 0; }

void to_postfix(const char *in, char *out) {
    char st[64]; int top = -1, k = 0;
    for (const char *p = in; *p; p++) {
        char c = *p;
        if (isalnum((unsigned char)c)) out[k++] = c;
        else if (c == '(') st[++top] = c;
        else if (c == ')') { while (st[top] != '(') out[k++] = st[top--]; top--; }
        else { while (top >= 0 && prec(st[top]) >= prec(c)) out[k++] = st[top--]; st[++top] = c; }
    }
    while (top >= 0) out[k++] = st[top--];
    out[k] = '\0';
}
```

후위식 계산은 피연산자를 push하고, 연산자를 만나면 두 개를 pop해 계산한 결과를 다시 push한다(먼저 꺼낸 것이 오른쪽 피연산자).

실행 결과 (강의 예제 2개 포함)

```text
A+B*C           -> ABC*+
A*(B+C)*D       -> ABC+*D*
(A+B)*(C-D)/E   -> AB+CD-*E/
2+3*4           -> 234*+  = 14
(2+3)*4         -> 23+4*  = 20
```

---

### 강의 자료 및 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| 스택 슬라이드 push | `void push(int data, int index)` | `int *index`로 받거나 전역 변수 사용 | 값 복사라 호출한 쪽 index가 변하지 않음 (실행해서 확인) |
| 스택 슬라이드 pop | `temp = stack[index]; index = index-1;` | `index = index - 1; temp = stack[index];` | index가 다음 빈칸을 가리키는 방식이므로 먼저 줄여야 함 |
| 스택 슬라이드 push | `if (index != Max) stack[index] = data; index = index + 1;` | 두 문장을 `{ }`로 묶음 | 중괄호가 없으면 가득 차도 index가 증가 |
| 스택 슬라이드 | "땔 수 없음", "넣을떄" | "뗄 수 없음", "넣을 때" | 오타 |
| Queue 슬라이드 | `front = real` | `front = rear` | 오타 |
| Queue 슬라이드 | `int n = 10; int Q[n];` (전역) | `#define N 10` | 전역 배열 크기는 상수식이어야 함 |
| 11주차 스택 보고서 | "Stack에 메모리 할당으로 노드를 생성한다" | 노드를 malloc으로 만들어 top 앞에 붙인다 | 의미 보완 |

---

## 최종 핵심 정리

1. 스택은 LIFO, 큐는 FIFO 구조다
2. 배열 스택은 가득 찼을 때 push하면 overflow, 비었을 때 pop하면 underflow다
3. top이 마지막 원소인지 다음 빈칸인지에 따라 push/pop의 증감 순서가 반대가 된다
4. 함수가 top이나 index를 바꾸려면 포인터로 넘기거나 전역 변수로 둔다
5. 연결 리스트 스택은 head를 top으로 써서 push와 pop이 $$O(1)$$이고 크기 제한이 없다
6. 선형 배열 큐는 front가 앞으로만 움직여 빈칸이 있어도 가득 찼다고 판단한다
7. 원형 큐는 `% MAX`로 인덱스를 돌리고 한 칸을 비워 가득 참과 빔을 구분한다
8. 중위식은 스택으로 후위식으로 바꿀 수 있고, 후위식은 스택 하나로 괄호 없이 계산한다

---

End.
