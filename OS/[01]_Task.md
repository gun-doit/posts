# Task
> OS가 제어하는 프로그램의 기본 단위
> Task State Modle -> Task는 실행되면서 상태가 변한다
> OSEK에서는 2타입의 Task에 대한 Model을 제공 : &rBasic Task, Extended Task&r

---

### Basic Task
![Image](https://poisonpotato.site/public/1736750829701.png)
&bRunning&b : CPU에 할당된 상태, insturction 수행, 하나의 Task만이 running 상태에 있을 수 있다.
&bready&b : running전 우선 state, scheduler는 ready state에 있는 task 중 다음 에 실행할 Task를 결정한다.
&bsuspended&b : 휴식 상태, activated가 가능한 상태

---

### Extended Task
![Image](https://poisonpotato.site/public/1736750985022.png)
&bWait&b : 특정 event가 발생하기 전까지 동작하지 않는 상태가 추가
system service에 의해 발생, 동작을 이어가려면 event가 발생해야 한다

---
## Scheduling
> OS가 CPU를 사용하려고 하는 프로세스들 사이에 &r우선순위&r를 관리하는 작업
> : 자원을 어떤 프로세스에 얼마나 할당하는지 정책을 만드는 것이라 볼 수 있음.

### Priority 기반의 Scheduling

1. ready/running state상의 모든 task검색
2. Highest priority를 가진 task 집합 결정
3. 그 중에서 가장 먼저 들어온 task 찾아서 Running 상태로 전송

### PreTaskHook / PostTaskHook
TaskHook → 디버깅이나 타이밍 정보를 측정 등 위하여 Task 동작 전후로 사용자가 작성하여 사용할 수 있는 루틴

동작 방식
순서 : PreTaskHook → Task running → PostTaskHook
running state 들어간 직후 PreTaskHook 동작
running state 에서 나오기 직전에 PostTaskHook동작
TaskHook 동작시 Task의 상태는 Running 상태를 유지
TaskHook 내에 사용자 루틴을 작성
![Image](https://poisonpotato.site/public/1736751130750.png)