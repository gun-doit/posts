컴퓨터 시스템에서는 프로그램이나 프로세스가 작동하는데 다양한 자원을 사용한다.

@OSEK@에서 Resource는 @RTOS@ 의 공유 자원을 의미한다. 
우선순위가 다른 여러 Task가 공유된 자원에 병행접근 하는 것을 조정 (임계영역 보호) 

---
## Resource를 공장에 비유해보자
1. **작업 (Task)** 
    1. 공장에서 일을 처리하는 여러 **작업자**, 이 작업자들은 각각의 일을 수행하고 있는데, 작업을 하다가 **특정 기계나 도구(Resource)**가 필요할 때, 그 기계를 사용할 수 있어야 한다.

2. **공유 자원 (Resource)** 
    1. 공장에서 **공유하는 도구나 기계**와 같다. 예를 들어, 절단기나 용접기 같은 중요한 기계들이 있을 수 있다. 여러 작업자가 이 기계를 동시에 사용하려고 하면 문제가 생길 수 있기 때문에, 작업자들이 서로 순서를 지켜 사용해야 한다.
    OSEK에서 Resource는 이런 **공유 자원**을 의미한다.

3. **자원 할당 규칙** 
    1. 공장의 관리자 (OSEK 시스템)는 특정 기계를 사용하는 작업자의 우선순위를 미리 정해 놓고, 기계가 사용 중일 때 다른 작업자가 그 기계를 기다리도록 한다.
OSEK에서는 작업자들이 자원을 사용할 때 **우선순위**를 정해준다. 이를 Priority Ceiling Protocol이라고 한다. 이 프로토콜을 통해 자원이 충돌 없이 안전하게 사용할 수 있도록 관리한다.

4. **자원 보호**
    1. 만약 작업자 A가 절단기를 사용하는 동안 작업자 B가 이를 사용하려고 하면, 관리자는 A가 절단기를 다 사용할 때까지 B를 대기시킨다. 이를 리소스 잠금(locking)이라고 한다. 작업자 A가 절단기를 다 사용하면  리소스 해제(unlock)가 이루어지고, 그제서야 작업자 B는 절단기를 사용할 수 있다.

5. **우선순위 역전 방지** 
    1. 우선순위가 높은 작업자가 낮은 작업자 때문에 기계를 사용하지 못하면 문제가 발생한다. 이를 방지하기 위해 Priority inversion이 발생하지 않도록 조정한다.
---

## Priority Inversion
공유 자원을 Lock을 걸 때 deadlock 혹은 priority inversion과 같은 문제가 발생할 수 있다.
**Priority Inversion**은 우선순위가 낮은 Task가 공유장원을 점유함으로써 우선순위가 높은 Task를 선점하는 상태를 말한다.
이로 인해서 우선순위가 높아서 우선적으로 처리되어야 하는 Task의 수행 시간이 비결정적으로 바뀌게 된다.

![이미지 설명](/public/Resource.png)

같은 공유자원을 사용하는 T1, T4에서 T4는 T1보다 낮은 우선순위를 가지고 있다.
하지만 공유자원을 먼저 점유한 T4에 의해 T1는 세마포어를 점유하지 못하고 T4가 세마포어를 release하고 ready 상태로 들어간 후에야 running 상태로 들어간다.

---
## Priority Ceiling Protocol
**우선순위가 낮은 Task가 Resource를 가지고 preemption될 때 발생할 수 있는 교착상태를 해결하기 위한 방법이다.**

- Resource를 GET 하는 순간 지정된 priority Task의 priority가 상승
- Resource를 release 하는 순간 Task는 원래의 priority로 돌아가고 Scheduling Policy가 preemptive일 경우 scheduling이 발생


![이미지 설명](/public/e9f06e7f71dfc905a2d468bce88bcd.png)


**동일한 Resource를 사용하는 group내에서 Task가 Resource를 점유하는 경우 group간 Task끼리는 선점되지 않는다**
![이미지 설명](/public/87d1d9b872dc5edf1754ffe990de1f.png)


---
## Resource 제약 사항
- 동일 Resource에 대해 중첩해서 획득할 수 없다.
- Task/ISR는 점유된 리소스를 가지고 종료할 수 없다.
- 한 개 Task안에서 다중 리소르를 점유하는 경우, 사용자는 LIFO(=stack) 방식으로 리소르를 request/relase
- 리소스를 사용하는 Task의 우선순위는 리소스에 할당된 우선순위 보다 크지 않아야 한다.
