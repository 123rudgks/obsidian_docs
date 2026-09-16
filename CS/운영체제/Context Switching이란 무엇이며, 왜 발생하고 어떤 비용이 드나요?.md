## 1. Context Switching이란?

**Context Switching**은 CPU가 현재 실행 중인 프로세스나 스레드의 실행 상태(Context)를 저장하고, 다음에 실행할 프로세스나 스레드의 실행 상태를 복원하여 실행 대상을 변경하는 과정이다.

CPU 코어 하나는 한 순간에 하나의 실행 흐름만 실제로 실행할 수 있다. 하지만 시스템에는 여러 프로세스와 스레드가 동시에 실행될 준비를 하고 있기 때문에 운영체제는 CPU를 여러 실행 흐름에게 번갈아 할당해야 한다.

```
Thread A 실행
↓
A의 Context 저장
↓
B의 Context 복원
↓
Thread B 실행
```

즉 Context Switching은 여러 프로세스와 스레드가 하나의 CPU를 나누어 사용할 수 있게 해주는 핵심 메커니즘이다.

---

# 2. Context란 무엇인가?

Context는 쉽게 말하면 다음과 같다.

> **프로세스나 스레드가 어디까지, 어떤 상태로 실행되고 있었는지를 나타내는 실행 상태 정보**

대표적으로 다음 정보들이 포함된다.

```
Context
├─ Program Counter
├─ CPU Register
├─ Stack Pointer
└─ 기타 CPU 실행 상태
```

Context Switching 이후 다시 해당 스레드를 실행하려면 이전에 어디까지 실행했는지, 계산 중이던 값이 무엇이었는지, Stack의 어느 위치를 사용하고 있었는지를 복원할 수 있어야 한다.

---

# 3. CPU Register란?

**Register**는 CPU 내부에 존재하는 매우 작고 빠른 저장 공간이다.

CPU는 연산을 수행할 때 필요한 값이나 중간 계산 결과를 Register에 저장한다.

예를 들어 다음 계산을 한다고 하자.

```
10 + 20
```

개념적으로는 다음과 같이 처리될 수 있다.

```
Register A = 10
Register B = 20

CPU 연산

Register C = 30
```

메모리 계층을 단순하게 비교하면 다음과 같다.

```
CPU Register
→ 가장 빠르고 매우 작음

CPU Cache
→ 매우 빠름

RAM
→ 상대적으로 느림
```

Thread A가 실행되다가 Thread B로 전환되면 B가 CPU Register를 사용하게 되므로, 나중에 A의 계산을 이어가기 위해 A의 기존 Register 상태를 저장해 두어야 한다.

---

# 4. Program Counter란?

**Program Counter(PC)**는 CPU Register의 한 종류로, 일반적으로 **다음에 실행할 명령어의 위치**를 가리킨다.

예를 들어 다음과 같은 기계어 명령이 있다고 하자.

```
주소 100 : 값을 읽는다.
주소 104 : 다른 값을 읽는다.
주소 108 : 두 값을 더한다.
주소 112 : 결과를 저장한다.
```

현재 104번 명령까지 실행했다면 Program Counter는 개념적으로 다음 실행 위치를 가리킨다.

```
PC = 108
```

CPU는 대략 다음 흐름을 반복한다.

```
PC가 가리키는 명령어 가져오기
↓
명령어 실행
↓
PC를 다음 명령어 위치로 이동
↓
다음 명령어 실행
```

Context Switching 시 Program Counter를 저장하지 않으면 나중에 다시 해당 Thread가 실행될 때 어디에서부터 이어서 실행해야 하는지 알 수 없다.

---

# 5. Stack Pointer란?

**Stack Pointer(SP)** 역시 CPU Register의 한 종류다.

Stack Pointer는 현재 Thread가 사용하는 Stack에서 현재 Stack의 위치를 가리킨다.

예를 들어 Thread A가 다음과 같이 함수를 호출했다고 하자.

```
main()
↓
foo()
↓
bar()
```

Thread Stack을 단순화하면 다음과 같다.

```
Thread A Stack

┌───────────────┐
│ main() Frame  │
├───────────────┤
│ foo() Frame   │
├───────────────┤
│ bar() Frame   │ ← 현재 Stack Pointer 부근
└───────────────┘
```

`bar()`가 종료되면 Stack Frame이 제거되고 Stack Pointer도 이전 위치로 돌아간다.

각 Thread는 자신만의 Stack을 가지기 때문에 Stack Pointer 역시 Thread마다 다르다.

```
Thread A
→ Stack A
→ SP = A의 Stack 위치

Thread B
→ Stack B
→ SP = B의 Stack 위치
```

따라서 Context Switching에서는 Stack Pointer 역시 저장하고 복원해야 한다.

---

# 6. Program Counter, Register, Stack Pointer를 한 번에 정리

```
Program Counter
→ 다음에 실행할 명령어 위치

CPU Register
→ 현재 CPU 계산 상태와 값

Stack Pointer
→ 현재 Thread Stack의 위치
```

Context Switching에서는 다음과 같은 전환이 이루어진다.

```
Thread A 실행

PC = A의 실행 위치
Registers = A의 계산 상태
SP = A의 Stack 위치

        ↓ Context Switching

Thread B 실행

PC = B의 실행 위치
Registers = B의 계산 상태
SP = B의 Stack 위치
```

---

# 7. PCB와 Context Switching의 관계

PCB는 **Process Control Block**의 약자로, 운영체제가 각 프로세스를 관리하기 위해 유지하는 자료구조다.

개념적으로 다음과 같은 정보를 가진다.

```
PCB
├─ PID
├─ Process State
├─ Program Counter 관련 정보
├─ Register 관련 정보
├─ Scheduling 정보
├─ Memory 관리 정보
├─ 열린 파일 정보
└─ 기타 프로세스 관리 정보
```

프로세스가 CPU를 사용하다가 다른 프로세스로 전환되면 운영체제는 다시 기존 프로세스를 이어서 실행할 수 있도록 필요한 상태를 PCB 또는 관련 커널 자료구조에 저장한다.

```
Process A 실행
↓
A의 Context 저장
↓
PCB A 등 커널 자료구조

PCB B 등에서 Context 복원
↓
Process B 실행
```

실제 운영체제에서는 Thread 실행 상태를 TCB(Thread Control Block)나 다른 task 구조체 등으로 관리할 수도 있다.

---

# 8. Process State

프로세스의 대표적인 상태는 다음과 같다.

```
New
 ↓
Ready
 ↓
Running
 ├────→ Waiting / Blocked
 │           ↓
 │         Ready
 │
 └────→ Terminated
```

핵심 상태는 세 가지다.

## Ready

CPU만 할당받으면 바로 실행할 수 있는 상태.

```
Ready
→ 실행 준비 완료
→ CPU를 기다리는 중
```

## Running

현재 CPU에서 실제 명령어를 실행하고 있는 상태.

## Waiting / Blocked

CPU를 받아도 당장 실행할 수 없는 상태.

예:

- Disk I/O 결과 대기
    
- Network I/O 대기
    
- Lock 대기
    
- 다른 이벤트 대기
    

```
Running
↓
I/O 요청
↓
Waiting

I/O 완료
↓
Ready
```

---

# 9. Context Switching은 언제 발생하는가?

대표적인 경우는 다음과 같다.

## 1) Time Slice가 끝났을 때

선점형 Scheduling에서는 하나의 Thread가 CPU를 일정 시간 이상 독점하지 못하도록 Time Quantum을 둘 수 있다.

```
Thread A 실행
↓
Time Quantum 종료
↓
Timer Interrupt
↓
Kernel 진입
↓
다른 Thread 선택
↓
Context Switching
```

## 2) 더 높은 우선순위의 작업이 실행되어야 할 때

```
낮은 우선순위 Thread 실행
↓
높은 우선순위 Thread Ready
↓
CPU 선점
↓
Context Switching
```

## 3) I/O를 기다릴 때

```
Thread A
↓
Disk read()
↓
I/O 대기
↓
Waiting 상태

CPU
↓
Thread B 실행
```

A가 I/O 결과를 기다리는 동안 CPU까지 놀릴 필요가 없기 때문에 다른 실행 대상을 선택한다.

## 4) Lock이나 이벤트를 기다릴 때

```
Thread A
↓
Lock 요청
↓
이미 다른 Thread가 Lock 소유
↓
Blocked
↓
다른 Thread 실행
```

## 5) Thread가 CPU를 명시적으로 양보할 때

일부 환경에서는 Thread가 `yield()` 등을 통해 Scheduler에게 CPU를 다른 실행 대상에게 양보할 수 있음을 알릴 수 있다.

예를 들어 Java에는 다음 메서드가 있다.

```
Thread.yield();
```

다만 `yield()`는 반드시 다른 Thread가 실행된다는 보장이라기보다 Scheduler에게 주는 힌트에 가깝다.

---

# 10. Scheduler와 Context Switching의 차이

이 둘은 반드시 구분해야 한다.

## Scheduler

> **Ready 상태의 실행 대상 중 다음에 누구에게 CPU를 줄지 결정한다.**

```
Ready Queue

A
B
C
↓
Scheduler

"다음은 B"
```

## Context Switching

> **현재 실행 상태를 저장하고 선택된 실행 상태를 복원하여 CPU 실행 대상을 실제로 바꾼다.**

```
Thread A
↓
A Context 저장
↓
B Context 복원
↓
Thread B
```

핵심:

```
Scheduler
→ 누구를 실행할지 결정

Context Switching
→ 실제 실행 대상을 교체
```

---

# 11. Scheduler와 Kernel의 관계

Scheduler는 Kernel과 별개의 프로그램이 아니라 **Kernel의 구성 요소**다.

```
Kernel
├─ Process / Thread 관리
├─ Memory 관리
├─ File System
├─ Device 관리
└─ Scheduler
```

따라서 실제 흐름은 다음과 같이 볼 수 있다.

```
Thread A 실행
↓
Timer Interrupt / I/O / Block 등
↓
Kernel 진입
↓
Kernel의 Scheduler가 다음 실행 대상 선택
↓
현재 Context 저장
↓
다음 Context 복원
↓
Thread B 실행
```

운영체제 교재에서는 Scheduler와 함께 **Dispatcher**라는 개념을 구분하기도 한다.

```
Scheduler
→ 누구를 실행할지 선택

Dispatcher
→ 선택된 실행 대상으로 실제 CPU 제어를 넘김
```

Context Switching 자체 역시 Kernel 영역에서 수행된다.

---

# 12. Process Context Switching과 주소 공간

같은 Process 내부의 Thread들은 같은 가상 주소 공간을 공유한다.

```
Process A

Thread A
Thread B

→ 동일한 Virtual Address Space 사용
```

하지만 서로 다른 Process들은 독립적인 가상 주소 공간을 가진다.

```
Process A
→ Virtual Address Space A

Process B
→ Virtual Address Space B
```

따라서 Process A에서 Process B로 전환될 경우 CPU가 사용하는 메모리 주소 변환 상태도 바뀔 수 있다.

---

# 13. Page Table과 메모리 관리 상태

각 Process는 서로 다른 가상 주소 공간을 가지기 때문에 같은 가상 주소라도 서로 다른 물리 메모리를 가리킬 수 있다.

```
Process A

Virtual 0x1000
→ Physical Frame 5


Process B

Virtual 0x1000
→ Physical Frame 20
```

이를 가능하게 하는 핵심 정보 중 하나가 **Page Table**이다.

```
Process A
→ Page Table A

Process B
→ Page Table B
```

Process A → B로 전환되면 다음과 같은 메모리 관련 상태도 변경될 수 있다.

```
Page Table A 사용
↓
Context Switch
↓
Page Table B 사용
```

관련되는 상태에는 다음과 같은 것들이 있다.

- 현재 주소 공간
    
- 현재 Page Table
    
- Page Table 관련 CPU Register
    
- TLB 상태
    
- Memory Mapping 관련 정보
    

따라서 서로 다른 Process 간 Context Switching은 같은 Process 내부 Thread 간 전환보다 일반적으로 더 많은 상태 변경이 필요하다.

---

# 14. TLB란?

TLB는 **Translation Lookaside Buffer**의 약자다.

> **가상 주소 → 물리 주소 변환 결과를 저장하는 빠른 캐시**

라고 이해하면 된다.

가상 메모리에서는 일반적으로 다음 주소 변환이 필요하다.

```
Virtual Address
↓
Page Table
↓
Physical Address
```

매번 Page Table을 메모리에서 조회하면 느리기 때문에 최근 주소 변환 결과를 TLB에 저장한다.

```
TLB

Virtual Page    Physical Frame

0x1000    →     Frame 5
0x2000    →     Frame 8
0x3000    →     Frame 2
```

그러면 다음 주소 접근에서는:

```
Virtual Address
↓
TLB 조회
↓
변환 정보 존재
↓
빠르게 Physical Address 확인
```

할 수 있다.

Process Context Switching으로 주소 공간이 바뀌면 TLB의 기존 변환 정보가 새로운 Process에 그대로 유효하지 않을 수 있다.

현대 CPU는 ASID, PCID 등의 기술로 이 비용을 줄이기도 하지만, 핵심은 다음과 같다.

```
TLB
→ 주소 변환 결과 캐시

Process 전환
→ 주소 공간 변경 가능
→ TLB 효율에도 영향 가능
```

---

# 15. CPU Cache와 Context Switching

Context Switching 시 CPU Cache를 Register처럼 저장하고 다른 Cache 내용으로 복원하는 것은 아니다.

예를 들어 Thread A가 실행되며 다음 데이터가 Cache에 있다고 하자.

```
CPU Cache

A 데이터
A 데이터
A 명령어
A 데이터
```

Thread B로 Context Switching이 발생해도 Cache 전체가 즉시 B의 데이터로 교체되는 것은 아니다.

```
Thread A
↓
Context Switch
↓
Thread B

CPU Cache에는
A 데이터가 여전히 남아 있을 수 있음
```

B가 실행되면서 필요한 데이터가 Cache에 없다면 Cache Miss가 발생한다.

```
B의 데이터 필요
↓
Cache에 없음
↓
Cache Miss
↓
하위 Cache / RAM에서 가져옴
↓
기존 A 데이터 일부가 밀려날 수 있음
```

따라서 Context Switching 이후 새로운 실행 대상에게 Cache가 충분히 준비되어 있지 않으면 성능이 저하될 수 있다.

---

# 16. 왜 Cache를 Context와 함께 저장하지 않는가?

CPU Cache는 Register보다 훨씬 크다.

Context Switching마다 Cache 전체를 저장하고 복원한다면 오히려 그 비용이 매우 커진다.

```
Context Switching
↓
Cache 전체 저장
↓
다른 Cache 전체 복원
```

은 비효율적이다.

따라서 일반적으로 Cache는 그대로 유지하고, 새로운 실행 대상이 사용하면서 필요한 데이터가 자연스럽게 Cache에 채워진다.

---

# 17. 같은 Process Thread 전환이 상대적으로 가벼운 이유

같은 Process 내부의 Thread들은 다음 자원을 공유한다.

```
Code
Data
Heap
Virtual Address Space
```

따라서:

```
Thread A
↓
Thread B
```

로 바뀌더라도 같은 주소 공간과 같은 Page Table을 사용할 수 있다.

주로 변경해야 하는 것은:

```
Program Counter
CPU Register
Stack Pointer
Thread 실행 상태
```

등이다.

반면:

```
Process A
↓
Process B
```

에서는 주소 공간 자체가 바뀔 수 있기 때문에 Page Table, TLB 등의 메모리 관련 상태에도 더 큰 영향을 줄 수 있다.

따라서 일반적으로:

```
같은 Process 내부 Thread Switching
→ 상대적으로 가벼움

서로 다른 Process Switching
→ 상대적으로 무거움
```

이라고 설명한다.

---

# 18. Context Switching Overhead

Context Switching은 애플리케이션의 실제 업무를 처리하는 과정이 아니라 실행 대상을 관리하기 위한 과정이다.

따라서 다양한 Overhead가 발생한다.

## 1) CPU 상태 저장/복원 비용

```
A PC/Register/SP 저장
↓
B PC/Register/SP 복원
```

그동안 애플리케이션의 실제 연산은 진행되지 않는다.

## 2) CPU Cache 효율 저하

```
A 실행
↓
Cache에 A 데이터 축적
↓
B로 Switch
↓
B 데이터 부족
↓
Cache Miss 증가 가능
```

## 3) TLB와 주소 공간 관련 비용

프로세스가 바뀌면 Page Table 등 주소 변환 환경이 바뀔 수 있다.

```
Process A 주소 공간
↓
Process B 주소 공간
```

따라서 TLB 등의 효율도 영향을 받을 수 있다.

---

# 19. Context Switching이 많으면 좋은가?

Context Switching은 반드시 필요하지만 무조건 많다고 좋은 것은 아니다.

Context Switching이 적으면 하나의 작업이 CPU를 오래 사용할 수 있어 응답성이 떨어질 수 있다.

```
A ───────────────────
                     B
```

반대로 너무 자주 Switching하면:

```
A
Switch
B
Switch
C
Switch
A
Switch
...
```

관리 비용이 커진다.

즉 다음과 같은 Trade-off가 있다.

```
Switching이 너무 적음
→ CPU 독점 가능
→ 응답성 저하

Switching이 너무 많음
→ 응답성/공정성 향상 가능
→ Context Switching Overhead 증가
```

---

# 20. 전체 흐름 정리

```
여러 Process / Thread
        ↓
     Ready Queue
        ↓
     Scheduler
        ↓
다음 실행 대상 결정
        ↓
현재 Context 저장
├─ Program Counter
├─ Registers
└─ Stack Pointer
        ↓
Process가 바뀐다면
Page Table 등 주소 공간 상태 변경
        ↓
다음 Context 복원
        ↓
새 Thread / Process 실행
        ↓
Cache / TLB 효율 변화 가능
        ↓
Context Switching Overhead
```

---

# 21. 핵심 개념 정리

## Context

**현재 프로세스나 스레드가 어디까지 어떤 상태로 실행되고 있었는지를 나타내는 실행 상태**

## Program Counter

**다음에 실행할 명령어의 위치를 가리키는 CPU Register**

## CPU Register

**CPU가 현재 연산에 사용하는 값과 실행 상태를 저장하는 매우 빠른 저장 공간**

## Stack Pointer

**현재 Thread의 Stack 위치를 가리키는 CPU Register**

## Scheduler

**Ready 상태의 실행 대상 중 다음에 누구에게 CPU를 줄지 결정하는 Kernel 구성 요소**

## PCB

**운영체제가 Process를 관리하기 위해 유지하는 정보 구조**

## TLB

**가상 주소에서 물리 주소로의 변환 결과를 캐싱하는 빠른 메모리**

## Context Switching

**현재 Context를 저장하고 다른 Context를 복원하여 CPU 실행 대상을 변경하는 과정**

---

# 22. 면접 답변

> Context Switching은 CPU가 현재 실행 중인 프로세스나 스레드의 실행 상태를 저장하고, 다음에 실행할 프로세스나 스레드의 상태를 복원하여 실행 대상을 변경하는 과정입니다.
> 
> CPU 코어보다 실행해야 할 프로세스나 스레드가 많기 때문에 운영체제의 Scheduler가 CPU 시간을 나누어 사용하도록 하며, Time Slice가 끝나거나 I/O 대기, Lock 대기, 더 높은 우선순위 작업의 등장 등이 발생할 때 Context Switching이 일어날 수 있습니다.
> 
> Context에는 Program Counter, CPU Register, Stack Pointer 등 현재 실행 상태를 이어가기 위해 필요한 정보가 포함됩니다. Scheduler는 Kernel의 일부로 다음 실행 대상을 결정하고, Kernel은 필요한 Context를 저장하고 복원하여 실제 실행 대상을 변경합니다.
> 
> 이 과정에서는 Register 등의 상태를 저장하고 복원하는 직접적인 CPU 비용이 발생하고, 새로운 실행 대상의 데이터가 CPU Cache에 없어서 Cache Miss가 증가하거나, 서로 다른 Process 간 전환 시 Page Table과 TLB 같은 주소 변환 상태에 영향을 줄 수 있습니다.
> 
> 일반적으로 서로 다른 Process 간 Context Switching은 주소 공간까지 변경될 수 있기 때문에 같은 Process 내부 Thread 간 Context Switching보다 비용이 더 큰 편입니다.

---

# 23. 전체 구조 한 장 정리

```
Thread A 실행
│
├─ PC
├─ Registers
└─ Stack Pointer
        ↓

Timer / I/O / Lock / yield 등
        ↓

      Kernel
        ↓
    Scheduler
        ↓
"다음은 Thread B"
        ↓

A Context 저장
        ↓

Process 변경 시
Page Table 등의
주소 공간 상태 변경
        ↓

B Context 복원
        ↓

Thread B 실행
        ↓

CPU Cache는 기존 내용 유지
        ↓

필요한 B 데이터가 없으면
Cache Miss 발생 가능
```

# 24. 복습 질문

### Q1. Context란 무엇인가?

프로세스나 스레드가 어디까지 어떤 상태로 실행되고 있었는지를 나타내는 실행 상태 정보다. Program Counter, Register, Stack Pointer 등이 대표적인 Context 정보다.

### Q2. Program Counter가 필요한 이유는 무엇인가?

Context Switching 후 해당 Thread가 다시 CPU를 받았을 때 이전에 실행하던 다음 명령어부터 이어서 실행해야 하기 때문이다.

### Q3. Stack Pointer가 Thread마다 다른 이유는?

각 Thread는 독립적인 Stack을 가지며 서로 다른 함수 호출 상태를 가지고 있기 때문이다.

### Q4. Scheduler와 Context Switching의 차이는?

Scheduler는 Ready 상태의 작업 중 누구에게 CPU를 줄지 결정하고, Context Switching은 현재 작업의 Context를 저장하고 선택된 작업의 Context를 복원하여 실제 실행 대상을 변경한다.

### Q5. Scheduler는 Kernel과 별개의 프로그램인가?

아니다. Scheduler는 일반적으로 Kernel의 구성 요소다.

### Q6. Process Context Switching이 Thread Context Switching보다 일반적으로 무거운 이유는?

서로 다른 Process는 독립적인 가상 주소 공간을 가지므로 Process가 바뀌면 Page Table 등 메모리 주소 변환 상태까지 변경될 수 있기 때문이다.

### Q7. TLB는 무엇인가?

Translation Lookaside Buffer의 약자로, 가상 주소에서 물리 주소로의 변환 결과를 캐싱하는 빠른 메모리다.

### Q8. Context Switching 시 CPU Cache도 함께 저장하고 복원하는가?

일반적으로 그렇지 않다. 기존 Cache 내용은 남아 있을 수 있으며 새로운 실행 대상이 필요한 데이터를 사용하면서 점차 Cache 내용이 교체된다.

### Q9. Context Switching이 Overhead인 이유는?

PC, Register, Stack Pointer 등을 저장하고 복원하는 동안 실제 애플리케이션 작업이 진행되지 않고, Cache Miss나 TLB 관련 비용도 추가로 발생할 수 있기 때문이다.

### Q10. Thread가 스스로 CPU를 놓는 대표적인 상황은?

`yield()`처럼 명시적으로 양보할 수도 있고, I/O 대기, Lock 대기, sleep 등으로 더 이상 실행할 수 없는 상태가 되어 CPU를 놓을 수도 있다.