## 1. Virtual Memory란?

**Virtual Memory(가상 메모리)**는 각 프로세스에게 자신만의 독립적인 가상 주소 공간을 제공하고, 이 가상 주소를 실제 물리 메모리 주소와 매핑하여 사용하는 메모리 관리 방식이다.

```
Process
↓
Virtual Address
↓
MMU
↓
TLB / Page Table
↓
Physical Address
↓
RAM
```

프로세스는 실제 RAM의 물리 주소를 직접 사용하는 대신 Virtual Address를 사용한다.

---

# 2. 왜 Virtual Memory가 필요한가?

프로세스가 Physical Memory를 직접 사용한다면 각 프로세스는 어떤 물리 주소를 사용할지, 다른 프로세스와 충돌하지 않는지, 실행할 때마다 달라질 수 있는 실제 메모리 위치를 어떻게 처리할지 등을 직접 고려해야 한다.

또한 물리 메모리에 자유롭게 접근할 수 있다면 다른 프로세스의 메모리를 침범할 위험도 있다.

운영체제는 이런 복잡성을 대신 관리하고 각 프로세스에게 독립적인 Virtual Address Space를 제공한다.

```
Process A
Virtual 0x1000
→ Physical Frame 5

Process B
Virtual 0x1000
→ Physical Frame 20
```

두 프로세스는 같은 Virtual Address를 사용해도 서로 다른 Physical Memory에 연결될 수 있다.

Virtual Memory의 핵심 목적은 다음과 같다.

```
Virtual Memory
├─ Address Space 추상화
├─ Process Isolation
├─ Memory Protection
├─ Physical Memory의 유연한 배치
└─ Demand Paging을 통한 효율적 사용
```

---

# 3. Virtual Address와 Physical Address

## Virtual Address

프로세스가 사용하는 논리적인 메모리 주소다.

```
Process A

0x0000
0x0001
0x0002
...
```

## Physical Address

실제 RAM의 위치를 나타내는 주소다.

```
Physical Memory

Frame 0
Frame 1
Frame 2
Frame 3
...
```

프로세스는 Virtual Address를 사용하고 MMU가 이를 Physical Address로 변환한다.

---

# 4. Address Space

**Address Space**는 프로세스가 사용할 수 있다고 보는 주소의 전체 범위다.

```
Process A
→ Virtual Address Space A

Process B
→ Virtual Address Space B
```

프로세스마다 독립적인 Address Space를 가지므로 같은 Virtual Address를 사용해도 충돌하지 않는다.

---

# 5. Paging

Virtual Memory를 일정 크기의 **Page**로 나누고 Physical Memory를 같은 크기의 **Frame**으로 나눈다.

```
Virtual Memory

Page 0
Page 1
Page 2
Page 3
```

```
Physical Memory

Frame 0
Frame 1
Frame 2
Frame 3
...
```

핵심:

```
Page
→ Virtual Memory의 단위

Frame
→ Physical Memory의 단위
```

Page와 Frame의 연결 관계는 Page Table이 관리한다.

---

# 6. Page Table

**Page Table**은 Virtual Page가 어떤 Physical Frame과 연결되어 있는지를 저장하는 자료구조다.

```
Page Table

Virtual Page    Physical Frame

Page 0       →  Frame 3
Page 1       →  Frame 7
Page 2       →  Frame 1
```

주소 변환:

```
Virtual Address
↓
Virtual Page 확인
↓
Page Table 조회
↓
Physical Frame 확인
↓
Physical Address 생성
```

---

# 7. Page Table은 어디에 저장되는가?

Page Table도 실제 데이터이므로 일반적으로 **Physical Memory, 즉 RAM에 저장된다.**

개념적으로 RAM에는 다음 정보들이 함께 존재할 수 있다.

```
RAM

├─ Process A의 Code / Data / Heap / Stack Page
├─ Process B의 Code / Data / Heap / Stack Page
├─ Process A의 Page Table
├─ Process B의 Page Table
└─ Kernel Data
```

즉 Page Table 역시 RAM을 사용한다.

---

# 8. 모든 Process의 Page Table이 RAM에 존재하는가?

현재 살아 있는 각 Process의 주소 공간을 관리하기 위한 Page Table 구조가 필요하다.

```
Process A → Page Table A
Process B → Page Table B
Process C → Page Table C
```

현재 CPU에서 실행 중이지 않은 Process라도 나중에 다시 실행될 수 있으므로 주소 공간 정보는 유지되어야 한다.

다만 프로세스의 거대한 Virtual Address Space 전체에 대응하는 Page Table Entry를 처음부터 전부 만들어 두는 것은 아니다.

현대 시스템은 보통 **다단계 Page Table**을 사용하며 필요한 영역 위주로 Page Table 구조를 생성한다.

```
Virtual Address Space
────────────────────────────

실제 사용하는 부분
███        ███

        ↓

필요한 Page Table 영역 위주로 구성
```

---

# 9. Process가 종료되면 Page Table은?

Process가 종료되면 운영체제는 해당 주소 공간을 정리한다.

```
Process 종료
↓
사용 중이던 Page 정리
↓
Page Table 구조 정리
↓
관련 Physical Memory 반환
```

따라서 Page Table이 사용하던 메모리도 회수된다.

---

# 10. MMU란?

**MMU = Memory Management Unit**

Virtual Address를 Physical Address로 변환하는 CPU의 하드웨어 구성 요소다.

```
CPU
↓
Virtual Address
↓
MMU
↓
TLB / Page Table
↓
Physical Address
↓
RAM
```

운영체제가 Page Table을 관리하고 MMU가 이를 이용해 주소 변환을 수행한다고 이해하면 된다.

---

# 11. CPU는 현재 어느 Page Table을 사용할지 어떻게 아는가?

CPU에는 현재 주소 공간의 Page Table을 찾기 위한 특별한 Register가 있다.

x86 계열에서는 대표적으로 **CR3**가 사용된다.

```
CPU

CR3
 ↓
현재 Process의 Page Table Root
 ↓
RAM
```

Process A 실행 중:

```
CR3 → Page Table A
```

Process B로 Context Switching:

```
CR3 → Page Table B
```

즉 Process가 바뀌면 현재 사용할 주소 공간과 Page Table 기준도 바뀔 수 있다.

---

# 12. TLB란?

**TLB = Translation Lookaside Buffer**

TLB는 Page Table의 주소 변환 결과를 저장하는 **CPU/MMU 내부의 주소 변환 Cache**다.

중요한 점:

> **TLB는 CPU Register가 아니다.**

```
CPU
│
├─ Registers
│  ├─ General-purpose Register
│  ├─ Program Counter
│  ├─ Stack Pointer
│  └─ CR3 같은 Control Register
│
├─ TLB
│  └─ Virtual → Physical 주소 변환 Cache
│
└─ CPU Cache
   ├─ L1
   ├─ L2
   └─ L3
```

---

# 13. 왜 TLB가 필요한가?

Page Table은 RAM에 있으므로 주소 변환마다 Page Table을 조회하면 느리다.

그래서 최근 주소 변환 결과를 TLB에 저장한다.

```
TLB

Virtual Page    Physical Frame

0x1000      →   Frame 7
0x2000      →   Frame 21
0x3000      →   Frame 4
```

```
Virtual Address
↓
TLB 확인

Hit
→ 빠르게 Physical Address 확인

Miss
→ Page Table 조회
→ 변환 결과를 TLB에 저장
```

즉:

> **TLB는 주소 변환용 Cache다.**

---

# 14. TLB는 Process마다 독립적인가?

아니다.

Page Table은 Process 주소 공간별로 존재하지만 TLB는 Process마다 생성되는 자료구조가 아니라 **CPU 하드웨어 자원**이다.

```
Page Table
→ Process 주소 공간별

TLB
→ CPU/MMU 측 Hardware Cache
```

CPU의 TLB 안에는 여러 Address Space의 변환 정보가 존재할 수 있다.

다만 실제 TLB가 Core별인지, Hardware Thread별인지, 일부 계층이 공유되는지는 CPU 아키텍처에 따라 달라질 수 있다.

---

# 15. 서로 다른 Process가 같은 Virtual Address를 사용하면 TLB가 충돌하지 않는가?

예:

```
Process A
Virtual 0x1000 → Frame 5

Process B
Virtual 0x1000 → Frame 20
```

현대 CPU에서는 주소 공간을 구분하기 위한 식별자를 TLB Entry와 함께 관리할 수 있다.

대표적인 개념:

```
ASID
= Address Space Identifier

PCID
= Process-Context Identifier
```

개념적으로:

```
TLB

Address Space   Virtual Page   Physical Frame

A               0x1000       → Frame 5
B               0x1000       → Frame 20
```

처럼 서로 다른 주소 공간의 변환 결과를 구분할 수 있다.

---

# 16. Process Switching과 TLB Flush

주소 공간 식별자를 활용하지 못하는 경우 Process가 바뀌면 이전 Process의 TLB Entry를 그대로 사용할 수 없다.

```
Process A
↓
Context Switch
↓
TLB Entry 무효화
↓
Process B
```

그 결과:

```
TLB Miss 증가
↓
Page Table 재조회
↓
TLB 다시 채움
```

이 발생할 수 있다.

따라서 Process Context Switching은 TLB 측면에서도 비용이 발생할 수 있다.

ASID/PCID는 서로 다른 Address Space의 Entry를 구분하여 이런 비용을 줄이는 데 도움을 준다.

---

# 17. Page Table과 TLB 비교

|항목|Page Table|TLB|
|---|---|---|
|위치|주로 RAM|CPU/MMU 내부|
|성격|주소 변환의 기준 자료구조|주소 변환 Cache|
|관리 단위|Process 주소 공간별|CPU Hardware|
|저장 내용|Virtual Page → Physical Frame 매핑|최근 사용한 주소 변환 결과|
|속도|TLB보다 느림|매우 빠름|

핵심:

```
Page Table
→ 원본 주소 변환 정보

TLB
→ 최근 주소 변환 결과 Cache
```

---

# 18. Process Isolation

Virtual Memory의 핵심 장점 중 하나다.

```
Process A
→ Page Table A

Process B
→ Page Table B
```

동일한 Virtual Address라도 서로 다른 Physical Frame으로 매핑할 수 있다.

```
A: 0x1000 → Frame 5
B: 0x1000 → Frame 20
```

따라서 A가 자신의 `0x1000`을 수정하더라도 B의 메모리에는 영향을 주지 않는다.

---

# 19. Memory Protection

Page Table Entry에는 주소 매핑 외에도 접근 권한 정보가 포함될 수 있다.

예:

```
Page A
→ Read 가능
→ Write 가능

Page B
→ Read 가능
→ Write 불가

Page C
→ Execute 가능
```

이를 이용해 Code, Read-only Data, 다른 Process의 영역 등에 대한 접근을 제어할 수 있다.

즉 Virtual Memory는 단순 주소 변환 기술이 아니라 Memory Protection의 핵심 기반이기도 하다.

---

# 20. Physical Memory 배치 추상화

Virtual Memory를 사용하면 프로그램은 실제 RAM의 위치에 의존하지 않아도 된다.

프로그램:

```
Virtual 0x1000 사용
```

실행 1:

```
Virtual 0x1000
→ Physical Frame 10
```

실행 2:

```
Virtual 0x1000
→ Physical Frame 38
```

프로그램은 실제 Frame이 어디인지 알 필요가 없다.

---

# 21. Physical Memory가 연속적일 필요가 없다

프로세스에게는 연속적인 Virtual Memory처럼 보이더라도 실제 RAM에서는 서로 다른 Frame에 흩어져 있을 수 있다.

```
Page 0 → Frame 10
Page 1 → Frame 3
Page 2 → Frame 21
Page 3 → Frame 7
```

즉:

```
Virtual
→ 연속적으로 보일 수 있음

Physical
→ 불연속적이어도 됨
```

이 때문에 Physical Memory를 더 유연하게 사용할 수 있다.

---

# 22. Demand Paging

Virtual Memory에서는 모든 Page를 항상 RAM에 둘 필요가 없다.

**Demand Paging**은 실제로 필요할 때 Page를 RAM에 적재하는 방식이다.

```
Process가 Page 7 접근
↓
Page 7이 RAM에 없음
↓
Page Fault
↓
운영체제가 Page 적재
↓
Page Table 갱신
↓
실행 재개
```

따라서 프로그램의 전체 Virtual Address Space가 동시에 RAM에 존재할 필요가 없다.

---

# 23. Virtual Memory = Disk인가?

아니다.

```
Virtual Memory
≠ Disk
```

Virtual Memory는 다음과 같은 더 큰 메모리 관리 개념이다.

```
Virtual Address Space
Address Translation
Page Table
Memory Protection
Paging
Process Isolation
Demand Paging
```

Disk나 Swap은 RAM에 없는 Page의 backing storage로 사용될 수 있는 수단이다.

---

# 24. Process Memory 구조와 Virtual Memory

이전에 배운:

```
Process Memory

Code
Data
Heap
Stack
```

은 프로세스의 Virtual Address Space를 논리적으로 바라본 구조라고 이해하면 된다.

```
Process Virtual Address Space

┌───────────────┐
│ Code          │
├───────────────┤
│ Data          │
├───────────────┤
│ Heap          │
│      ↓        │
│               │
│      ↑        │
│ Stack         │
└───────────────┘
```

각 영역의 Page는 실제 RAM의 서로 다른 Physical Frame에 배치될 수 있다.

---

# 25. Context Switching과 Virtual Memory

서로 다른 Process는 일반적으로 서로 다른 Page Table을 사용한다.

```
Process A
→ Page Table A

Process B
→ Page Table B
```

Process A → Process B로 Context Switching이 발생하면 Address Space도 바뀐다.

이 과정에서는 Page Table 기준 변경이나 TLB 영향 등이 발생할 수 있다.

반면 같은 Process 내부의 Thread들은 같은 Address Space와 Page Table을 공유한다.

```
Thread A
Thread B

→ 같은 Address Space
→ 같은 Page Table
```

따라서 같은 Process 내부 Thread Switching이 일반적으로 더 가벼운 이유 중 하나가 된다.

---

# 26. Virtual Memory를 사용하는 이유를 정확히 정리

Virtual Memory를 단순히:

```
"프로세스가 메모리를 관리하기 편하게 해준다"
```

라고만 이해하면 부족하다.

더 정확한 구조:

```
Virtual Memory

1. Address Space 추상화
2. Process Isolation
3. Memory Protection
4. Physical Memory의 유연한 배치
5. 필요한 Page만 RAM에 적재 가능
```

즉 OS가 Physical Memory 관리의 복잡성을 대신 담당하고, Process에게 일관되고 독립적인 Virtual Address Space를 제공한다.

---

# 27. 사용자 이해를 다듬은 최종 설명

> 프로세스가 물리 메모리를 직접 사용하면 실제 RAM의 배치와 주소에 의존하게 되고, 프로세스 간 충돌과 보호 문제도 발생할 수 있다. 운영체제는 각 프로세스에게 독립적인 Virtual Address Space를 제공하고 Page Table을 통해 Physical Memory와 매핑함으로써 실제 물리 메모리 배치를 추상화한다. 이를 통해 프로세스는 실제 물리 주소를 신경 쓰지 않고 일관된 주소 공간을 사용할 수 있고, 운영체제는 프로세스 간 격리와 보호, 효율적인 Physical Memory 관리를 수행할 수 있다.

---

# 28. 전체 주소 변환 흐름

```
Process
↓
Virtual Address
↓
MMU
↓
TLB 조회
│
├─ Hit
│   ↓
│ Physical Address
│
└─ Miss
    ↓
Page Table 조회
    ↓
Physical Frame 확인
    ↓
TLB 갱신
    ↓
Physical Address

        ↓

RAM 접근
```

Page가 RAM에 없다면:

```
Page Fault
↓
운영체제 개입
↓
Page를 RAM에 적재
↓
Page Table 갱신
↓
실행 재개
```

---

# 29. 핵심 개념 정리

## Virtual Memory

**프로세스에게 독립적인 Virtual Address Space를 제공하고 Physical Memory와 매핑하여 사용하는 메모리 관리 방식**

## Virtual Address

**프로세스가 사용하는 논리적 주소**

## Physical Address

**실제 RAM의 주소**

## Page

**Virtual Memory를 나눈 단위**

## Frame

**Physical Memory를 나눈 단위**

## Page Table

**Virtual Page와 Physical Frame의 매핑을 저장하는 Process 주소 공간별 자료구조**

## MMU

**Virtual Address를 Physical Address로 변환하는 CPU의 하드웨어 구성 요소**

## TLB

**최근 Virtual → Physical 주소 변환 결과를 저장하는 CPU/MMU 내부 Cache**

## CR3 등 Control Register

**현재 사용할 Page Table 구조를 찾는 데 필요한 CPU Register**

## ASID / PCID

**TLB 안에서 서로 다른 Address Space의 변환 결과를 구분하기 위한 식별자**

---

# 30. 면접 답변

> Virtual Memory는 각 프로세스에게 독립적인 가상 주소 공간을 제공하고, Page Table을 통해 Virtual Address를 실제 Physical Memory와 매핑하는 메모리 관리 방식입니다.
> 
> 프로세스는 실제 RAM의 위치를 직접 사용할 필요 없이 자신의 Virtual Address만 사용하며, 운영체제가 Process별 Page Table을 관리하고 MMU가 이를 이용해 Physical Address로 변환합니다.
> 
> Page Table 자체는 일반적으로 RAM에 존재하며, CPU는 현재 Process의 Page Table을 찾기 위한 Control Register를 사용합니다. Page Table을 매번 조회하는 비용을 줄이기 위해 CPU/MMU 내부에는 최근 주소 변환 결과를 저장하는 TLB가 존재합니다.
> 
> TLB는 Process별 자료구조가 아니라 CPU의 Hardware Cache이며, 현대 CPU에서는 ASID나 PCID 등을 이용해 서로 다른 Address Space의 TLB Entry를 구분할 수 있습니다.
> 
> Virtual Memory를 사용하면 Process 간 Address Space를 분리하여 메모리 격리와 보호를 제공할 수 있고, 프로그램이 실제 Physical Memory의 위치에 의존하지 않아도 됩니다. 또한 Paging과 Demand Paging을 이용해 Physical Memory를 유연하고 효율적으로 사용할 수 있습니다.

---

# 31. 전체 구조 한 장 정리

```
                Process A
                    │
             Virtual Address
                    │
                    ▼
             ┌────────────┐
             │    MMU     │
             └─────┬──────┘
                   │
                  TLB
          주소 변환 Cache
                   │
          Hit ─────┴───── Miss
           │                │
           │          Page Table A
           │              in RAM
           │                │
           └────────┬───────┘
                    ▼
             Physical Address
                    │
                    ▼
                   RAM
```

핵심 비교:

```
Page Table
→ Process Address Space별
→ RAM에 저장

TLB
→ CPU/MMU Hardware Cache
→ 여러 Address Space의 변환 정보가 존재할 수 있음
```

---

# 32. 복습 질문

### Q1. Page Table은 어디에 저장되는가?

Page Table도 데이터이므로 일반적으로 Physical Memory인 RAM에 저장된다.

### Q2. CPU는 RAM에 있는 현재 Process의 Page Table을 어떻게 찾는가?

현재 Page Table 구조를 찾기 위한 정보를 CR3 같은 CPU Control Register에 저장하고, MMU가 이를 이용해 Page Table을 참조한다.

### Q3. 모든 Process의 Page Table은 RAM에 존재하는가?

현재 살아 있는 Process의 주소 공간을 관리하기 위한 Page Table 구조는 필요하다. 다만 모든 Virtual Address에 대한 Entry를 처음부터 전부 만드는 것이 아니라 다단계 Page Table 등을 이용해 필요한 부분 위주로 구성한다.

### Q4. Process가 종료되면 Page Table은 어떻게 되는가?

운영체제가 해당 Process의 주소 공간을 정리하면서 Page Table이 사용하던 메모리도 회수한다.

### Q5. TLB는 CPU Register인가?

아니다. TLB는 CPU/MMU 내부에 존재하는 주소 변환 전용 Cache다.

### Q6. Page Table과 TLB의 가장 중요한 차이는?

Page Table은 Virtual Page와 Physical Frame의 실제 매핑 정보를 가진 Process 주소 공간별 자료구조이고, TLB는 최근 주소 변환 결과를 빠르게 저장하는 CPU Hardware Cache다.

### Q7. TLB는 Process마다 따로 존재하는가?

아니다. TLB는 CPU Hardware 자원이다. 여러 Address Space의 주소 변환 정보가 들어 있을 수 있으며 실제 구성 방식은 CPU 아키텍처에 따라 달라질 수 있다.

### Q8. Process A와 B가 같은 Virtual Address를 사용해도 TLB가 충돌하지 않는 이유는?

현대 CPU에서는 ASID나 PCID 같은 Address Space 식별자를 이용해 서로 다른 Process의 동일 Virtual Address에 대한 TLB Entry를 구분할 수 있기 때문이다.

### Q9. Process Context Switching이 TLB에 영향을 주는 이유는?

Process마다 사용하는 Address Space와 Page Table이 다르기 때문이다. 주소 공간이 바뀌면서 기존 TLB Entry가 새로운 Process에 유효하지 않을 수 있어 일부 Entry를 무효화하거나 새 주소 변환 정보를 채워야 할 수 있다.

### Q10. Process A와 B가 둘 다 `0x1000`을 사용할 수 있는 이유는?

서로 다른 Page Table을 사용하기 때문에 A의 `0x1000`과 B의 `0x1000`을 서로 다른 Physical Frame으로 매핑할 수 있다.

### Q11. Virtual Memory가 필요한 가장 중요한 이유는?

Physical Memory의 실제 배치를 Process에게 직접 노출하지 않고 독립적인 Address Space를 제공함으로써 주소 공간 추상화, Process Isolation, Memory Protection, 효율적인 Physical Memory 관리를 가능하게 하기 위해서다.

### Q12. Virtual Memory와 Disk는 같은 개념인가?

아니다. Virtual Memory는 주소 공간 추상화와 주소 변환, 보호, Paging 등을 포함하는 메모리 관리 개념이고 Disk나 Swap은 RAM에 없는 Page를 보관하는 데 활용될 수 있는 수단이다.

### Q13. Virtual Memory를 사용하면 왜 연속된 Physical Memory가 필요하지 않은가?

연속된 Virtual Page들을 Page Table을 통해 서로 다른 Physical Frame에 각각 매핑할 수 있기 때문이다.

### Q14. 같은 Process 내부 Thread들은 왜 같은 Page Table을 사용할 수 있는가?

Thread들은 같은 Process의 Virtual Address Space를 공유하기 때문이다.