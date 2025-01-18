## 큐(Queue)
먼저 들어오는 데이터가 먼저 나가는 @FIFO

```cpp
#include<queue>

//선언
queue<자료형> 이름:

//추가 및 삭제
push(element) // 큐에 element(원소)를 추가
pop()         // 큐에 있는 원소를 삭제 (반환값 없음)

//서칭
front()       //큐 제일 앞에 있는 원소 반환
back()        //큐 제일 뒤에 있는 원소 반환
top()         //맨 앞에 있는 원소 반환

//기타
empty()       //큐가 비었으면 true 아니면 false 반환
size()        //큐의 크기 반환
```
---

## 우선 순위 큐(Priority Queue)
**우선순위**가 높은 데이터가 먼저 나가는 형태

```cpp
#include<queue>

//선언
priority_queue<자료형, Container, 비교함수> 변수명
//선언한 자료형 변수들을 비교함수에 따라 정렬하는 우선순위큐를 생성
//ex) priority_queue<int,vector<int>,cmp> priority_q
//    선언한 자료형 변수들을 cmp 우선순위에 따라 정렬하는 우선순위큐를 생성
// cmp = greater<자료형> => 오름차순 정렬

priority_queue<자료형> 변수명
//선언한 자료형 변수들을 내림차순에 따라 정렬하는 우선순위큐를 생성
```

