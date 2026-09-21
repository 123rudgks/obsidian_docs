## 1. Page Fault란?

**Page Fault**는 프로세스가 어떤 Virtual Page에 접근했을 때, 해당 Page를 현재 정상적으로 사용할 수 없어 CPU가 Kernel에게 처리를 요청하는 예외(Exception)다.

가장 대표적인 경우는:

> **프로세스가 접근한 Page가 현재 RAM에 올라와 있지 않은 경우**

다.

```text
Process가 Virtual Address 접근
        ↓
      MMU
        ↓
 Page Table 확인
        ↓
Page가 RAM에 없음
        ↓
   Page Fault 발생
        ↓
     Kernel 개입
        ↓
필요한 Page를 RAM에 적재
        ↓
 Page Table 갱신
        ↓
문제가 났던 명령어 재실행
```

---

# 2. Page Fault가 왜 발생하는가?

Virtual Memory에서는 **Demand Paging**을 사용할 수 있다.

Demand Paging은 프로그램의 모든 Page를 처음부터 RAM에 올리지 않고, 실제로 필요할 때만 RAM에 적재하는 방식이다.

```text
Virtual Address Space

Page 0 → RAM
Page 1 → RAM
Page 2 → 없음
Page 3 → 없음
Page 4 → RAM
Page 5 → 없음
```

이 상태에서 Process가 Page 3에 접근하면:

```text
Process
↓
Page 3 접근
↓
Page Table 확인
↓
Page 3이 RAM에 없음
↓
Page Fault
```

가 발생한다.

---

# 3. Page Fault는 항상 오류인가?

아니다.

Demand Paging에서는 필요한 Page를 처음 접근할 때 Page Fault가 정상적으로 발생할 수 있다.

```text
프로그램이 Page 10을 처음 접근
↓
아직 RAM에 없음
↓
Page Fault
↓
OS가 RAM에 적재
↓
정상 실행
```

반면 접근 자체가 잘못된 경우도 있다.

```text
잘못된 Virtual Address 접근
↓
Page Fault / Protection Fault
↓
Kernel 검사
↓
유효하지 않은 접근
↓
Process 오류 처리 또는 종료
```

Unix 계열에서는 이런 잘못된 접근이 `Segmentation Fault`로 이어질 수 있다.

---

# 4. Page Table은 어떻게 RAM에 없는 Page를 구분하는가?

Page Table Entry에는 주소 매핑 외에도 여러 상태 정보가 들어갈 수 있다.

```text
Page Table Entry

├─ Physical Frame Number
├─ Present / Valid Bit
├─ Read / Write Permission
├─ User / Kernel Permission
├─ Dirty Bit
├─ Accessed Bit
└─ 기타 상태 정보
```

중요한 값 중 하나가 **Present Bit**이다.

```text
Present = 1
→ 현재 RAM에 Page가 존재

Present = 0
→ 현재 RAM에서 직접 사용할 수 없음
```

Process가 `Present = 0`인 Page에 접근하면 Page Fault가 발생할 수 있다.

---

# 5. Page Fault는 누가 처리하는가?

**Kernel이 처리한다.**

```text
Process 실행
↓
Memory Access
↓
MMU가 문제 발견
↓
Page Fault Exception
↓
Kernel Mode 진입
↓
Page Fault Handler 실행
```

애플리케이션이 직접 Page Fault를 해결하는 것은 아니다.

---

# 6. Page Fault 처리 과정

## 1) Process가 Virtual Address에 접근

```text
Process A
↓
Virtual Address 접근
```

## 2) MMU가 Page Table 확인

```text
Virtual Page
↓
Page Table
```

그런데:

```text
Present = 0
```

## 3) CPU가 Page Fault Exception 발생

```text
CPU
↓
Page Fault Exception
↓
Kernel로 제어 이동
```

## 4) Kernel이 접근이 유효한지 확인

Kernel은 해당 Virtual Address 접근이 Process에게 허용된 정상적인 접근인지 확인한다.

```text
정상적인 Heap 영역?
정상적인 Stack 영역?
Memory-mapped File 영역?
접근 권한이 있는가?
```

유효하지 않다면:

```text
Invalid Access
↓
Process에 오류 전달
↓
필요하면 종료
```

유효하다면 필요한 Page를 RAM에 적재하는 과정을 진행한다.

---

# 7. 필요한 Page는 어디에서 가져오는가?

Page의 종류에 따라 다음과 같은 곳에서 내용을 가져올 수 있다.

```text
Executable File
Memory-mapped File
Swap Area
Zero-filled Anonymous Memory
```

기초적으로는 다음처럼 이해해도 된다.

```text
Disk / Swap
↓
필요한 Page 찾기
```

---

# 8. 빈 Physical Frame 확보

RAM에 빈 Frame이 있다면 필요한 Page를 그 Frame에 적재한다.

```text
RAM

Frame 0 → 사용 중
Frame 1 → 사용 중
Frame 2 → 비어 있음
Frame 3 → 사용 중
```

```text
필요한 Page
↓
Frame 2에 적재
```

---

# 9. 빈 Frame이 없다면?

RAM이 모두 사용 중이면 **Page Replacement**가 필요하다.

```text
RAM

Frame 0 → Page A
Frame 1 → Page B
Frame 2 → Page C
Frame 3 → Page D
```

새 Page E가 필요하다면:

```text
Replacement Algorithm
↓
Victim Page 선택
↓
기존 Page 제거
↓
Page E 적재
```

이것이 Page Replacement다.

---

# 10. Page Replacement Algorithm

대표적인 알고리즘:

```text
FIFO
LRU
Clock
```

## FIFO

가장 오래 전에 RAM에 들어온 Page를 제거한다.

## LRU

**Least Recently Used**

가장 오랫동안 사용되지 않은 Page를 제거한다.

## Clock

Reference Bit 등을 이용해 LRU를 근사하는 대표적인 방식이다.

기초 수준에서는:

```text
RAM이 꽉 참
↓
Victim Page 선택
↓
Page Replacement
```

흐름을 이해하는 것이 중요하다.

---

# 11. Dirty Page란?

RAM에 올라온 후 수정된 Page를 **Dirty Page**라고 한다.

```text
Disk의 Page A
↓
RAM에 적재
↓
Process가 Page A 수정
↓
RAM 내용 ≠ Disk 내용
↓
Dirty Page
```

---

# 12. Dirty Page를 제거할 때

Dirty Page는 그냥 버리면 수정 내용이 사라질 수 있다.

```text
Dirty Page
↓
필요하면 Disk / Swap 등에 Write Back
↓
Frame 재사용
```

반면 Clean Page는 backing storage와 동일한 상태라면 다시 읽어올 수 있기 때문에 별도 Write-back 없이 제거 가능한 경우가 많다.

---

# 13. 전체 Page Fault 처리 흐름

```text
Process가 Virtual Address 접근
        ↓
      TLB 확인
        ↓
     TLB Miss
        ↓
   Page Table 확인
        ↓
  Page가 RAM에 있는가?
     /           \
   Yes            No
    │              │
주소 변환       Page Fault
                   ↓
              Kernel 진입
                   ↓
          접근이 유효한가?
             /          \
           No            Yes
           │              │
       오류 처리       Page 위치 확인
                          ↓
                    빈 Frame 있는가?
                     /          \
                   Yes           No
                    │             │
                    │        Victim Page 선택
                    │             ↓
                    │         Dirty인가?
                    │         /       \
                    │       Yes        No
                    │        │          │
                    │     Write Back    │
                    │        └────┬─────┘
                    │             ↓
                    └──────→ Frame 확보
                                ↓
                        필요한 Page 적재
                                ↓
                        Page Table 갱신
                                ↓
                         필요 시 TLB 갱신
                                ↓
                     Fault 난 명령어 재실행
```

---

# 14. TLB Miss와 Page Fault의 차이

## TLB Miss

TLB에 해당 주소의 변환 정보가 없다는 뜻이다.

```text
TLB
→ Page 5 정보 없음
```

Page Table에 정상적인 Mapping이 있다면:

```text
Page Table
→ Page 5 → Frame 20
→ Present = 1
```

다음처럼 처리된다.

```text
TLB Miss
↓
Page Table 조회
↓
Frame 20 확인
↓
TLB 갱신
↓
정상 실행
```

Page Fault는 발생하지 않는다.

---

# 15. Page Fault

Page Table을 확인했는데 현재 접근을 정상 처리할 수 없다면 Page Fault가 발생한다.

대표적인 경우:

```text
Page가 RAM에 없음
```

또는:

```text
Write 권한 없는 Page에 Write
User Mode에서 Kernel-only Page 접근
잘못된 Mapping 접근
```

이다.

핵심 차이:

```text
TLB Miss
→ 주소 변환 정보가 TLB Cache에 없음

Page Fault
→ 현재 메모리 접근을 정상 처리할 수 없어
   Kernel 처리가 필요
```

---

# 16. TLB Miss와 Page Fault 흐름 비교

```text
Virtual Address
↓
TLB 확인
↓
Miss
↓
Page Table 확인
```

Page가 RAM에 있다면:

```text
Present = 1
↓
TLB 갱신
↓
정상 실행
```

→ TLB Miss만 발생

Page가 RAM에 없다면:

```text
Present = 0
↓
Page Fault
↓
Kernel 개입
```

→ Page Fault 발생

---

# 17. Page Fault 처리 중 Process 상태

Storage I/O가 필요하면 해당 Process는 Waiting / Blocked 상태가 될 수 있다.

```text
Process A
↓
Page Fault
↓
Disk에서 Page 읽기 요청
↓
A → Waiting
```

그동안 Scheduler는 다른 Process를 실행할 수 있다.

```text
Process A
Waiting

Process B
↓
CPU 실행
```

Page 적재가 끝나면:

```text
Disk I/O 완료
↓
Process A
Waiting → Ready
↓
나중에 CPU 획득
↓
실행 재개
```

한다.

---

# 18. 왜 Fault가 난 명령어를 다시 실행하는가?

예:

```text
LOAD [Virtual Address X]
```

처음 실행할 때 Page가 RAM에 없으면:

```text
LOAD
↓
Page Fault
```

가 발생한다.

Kernel이 Page를 적재하고 Page Table을 갱신한 뒤 같은 명령어를 다시 실행하면 정상적으로 처리할 수 있다.

```text
Page Fault
↓
Page 적재
↓
같은 명령 재실행
↓
성공
```

---

# 19. Minor Page Fault

Page Fault라고 해서 항상 Storage I/O가 필요한 것은 아니다.

**Minor Page Fault**는 일반적으로 필요한 데이터가 이미 RAM에 있어 Storage에서 새로 읽을 필요 없이 해결할 수 있는 Page Fault다.

```text
Page Fault
↓
필요한 데이터가 이미 RAM에 있음
↓
Page Table Mapping 조정
↓
실행 재개
```

```text
Minor Page Fault
→ 일반적으로 Storage I/O 없음
```

---

# 20. Major Page Fault

필요한 Page가 RAM에 없어서 실제 Storage I/O가 필요하다면 **Major Page Fault**라고 한다.

```text
Page Fault
↓
Disk / Storage I/O
↓
RAM에 Page 적재
```

일반적으로 Minor Page Fault보다 훨씬 비싸다.

```text
Minor Fault
→ Storage I/O 없음

Major Fault
→ Storage I/O 필요
```

---

# 21. Page Fault가 비싼 이유

Major Page Fault는 다음과 같은 과정을 포함할 수 있다.

```text
CPU Exception
↓
Kernel 진입
↓
Page 위치 확인
↓
Frame 확보
↓
필요하면 Victim Page Write-back
↓
Storage I/O
↓
Page Table 갱신
↓
Scheduling / Context Switching
↓
명령 재실행
```

특히 Storage I/O가 발생하면 일반적인 메모리 접근보다 훨씬 큰 비용이 든다.

---

# 22. Page Fault는 나쁜 것인가?

Page Fault 자체는 Demand Paging에서 정상적으로 발생할 수 있다.

문제는:

> **Page Fault가 지나치게 자주 발생하는 경우**

다.

---

# 23. Thrashing

RAM이 너무 부족하면 필요한 Page를 계속 넣고 빼는 상황이 발생할 수 있다.

```text
Page A 필요
↓
Page B 제거

잠시 후
Page B 필요
↓
Page A 제거

다시
Page A 필요
↓
Page B 제거
```

결국:

```text
Page In
Page Out
Page In
Page Out
...
```

이 반복된다.

이런 상태를 **Thrashing**이라고 한다.

> Page Fault와 Page Replacement가 지나치게 반복되어 시스템이 실제 작업보다 Page 교체에 대부분의 시간을 소비하는 상태

다.

---

# 24. Working Set

프로그램은 특정 시점에 전체 Page를 골고루 사용하는 것이 아니라 일부 Page를 집중적으로 사용하는 경향이 있다.

```text
전체 Page

1 2 3 4 5 6 7 8 9 10

현재 자주 사용하는 Page

3 4 5 6
```

이런 자주 사용하는 Page 집합을 **Working Set**이라고 볼 수 있다.

Working Set이 RAM에 충분히 유지되면:

```text
Page Fault 감소
↓
성능 향상
```

반대로 Working Set을 유지할 만큼 Frame이 부족하면:

```text
Page Fault 증가
↓
Page Replacement 증가
↓
Thrashing 가능
```

하다.

---

# 25. Page Fault와 Segmentation Fault의 차이

## Page Fault

CPU/MMU 수준에서 발생하는 메모리 접근 예외다.

정상적으로 처리 가능한 경우도 있다.

```text
Page가 RAM에 없음
↓
Page Fault
↓
OS가 적재
↓
정상 실행
```

## Segmentation Fault

Process가 허용되지 않은 메모리에 접근했을 때 OS가 Process에 전달하는 오류다.

```text
Process
↓
잘못된 주소 접근
↓
CPU Memory Exception
↓
Kernel 검사
↓
유효하지 않은 접근
↓
SIGSEGV 등 전달
↓
Segmentation Fault
```

즉:

```text
Page Fault
→ Hardware / OS 수준의 메모리 접근 예외

Segmentation Fault
→ 잘못된 메모리 접근에 대한
   OS 차원의 오류 처리 결과 중 하나
```

다.

---

# 26. Page Fault와 Context Switching의 연결

Page Fault 처리에 Storage I/O가 필요하면:

```text
Process A
Running
↓
Page Fault
↓
Storage I/O 필요
↓
Waiting
```

이 된다.

그동안 CPU는 다른 Process에게 할당될 수 있다.

```text
Scheduler
↓
Process B 선택
↓
Context Switching
↓
Process B 실행
```

Page가 준비되면:

```text
Process A
Waiting
↓
Ready
↓
Scheduler에게 다시 선택 가능
```

해진다.

따라서:

```text
Virtual Memory
↓
Page Fault
↓
I/O
↓
Waiting
↓
Scheduler
↓
Context Switching
```

으로 이전 개념들과 연결된다.

---

# 27. Page Fault를 정확히 이해하기

기초적으로는:

```text
Page Fault
= Page가 RAM에 없음
```

이라고 이해해도 되지만 정확히는 더 넓다.

Page Fault는:

> **CPU가 Page 기반 메모리 접근을 정상적으로 완료할 수 없어 Kernel 처리가 필요한 상황**

이다.

대표적인 경우:

```text
Page Fault
├─ Not Present
└─ Protection Violation
```

---

# 28. 핵심 개념 비교

| 개념 | 의미 |
|---|---|
| TLB Miss | 주소 변환 정보가 TLB에 없음 |
| Page Fault | 현재 메모리 접근을 정상 처리할 수 없어 Kernel 처리가 필요 |
| Demand Paging | 필요한 Page를 필요할 때 RAM에 적재 |
| Page Replacement | RAM이 꽉 찼을 때 기존 Page를 제거하고 새 Page 적재 |
| Dirty Page | RAM에서 수정되어 backing storage와 내용이 달라진 Page |
| Minor Page Fault | 일반적으로 Storage I/O 없이 처리 가능한 Page Fault |
| Major Page Fault | Storage I/O가 필요한 Page Fault |
| Thrashing | Page Fault와 Page 교체가 지나치게 반복되는 상태 |

---

# 29. 전체 구조 한 장 정리

```text
Process
↓
Virtual Address 접근
↓
TLB 조회

TLB Hit
→ 바로 주소 변환

TLB Miss
↓
Page Table 조회
↓
Present?

Yes
→ TLB 갱신
→ RAM 접근

No
→ Page Fault
     ↓
   Kernel
     ↓
유효한 접근인가?
 ├─ No
 │   → 오류 처리
 │
 └─ Yes
     ↓
필요한 Page 찾기
     ↓
빈 Frame?
 ├─ Yes
 │
 └─ No
     ↓
Page Replacement
     ↓
Victim Page가 Dirty?
 ├─ Yes → Write Back
 └─ No  → 바로 제거
     ↓
새 Page 적재
     ↓
Page Table 갱신
     ↓
Process Ready
     ↓
Fault 난 명령 재실행
```

---

# 30. 면접 답변

> Page Fault는 프로세스가 Virtual Address에 접근했을 때 해당 Page를 현재 정상적으로 사용할 수 없어 CPU가 Kernel에게 처리를 요청하는 예외입니다. Demand Paging 환경에서는 필요한 Page가 현재 RAM에 존재하지 않을 때 대표적으로 발생합니다.
>
> Page Fault가 발생하면 CPU는 Kernel Mode로 전환되고 운영체제의 Page Fault Handler가 해당 접근이 유효한지 확인합니다. 정상적인 접근이라면 필요한 Page를 저장장치 등에서 찾아 Physical Memory의 빈 Frame에 적재합니다.
>
> 빈 Frame이 없다면 Page Replacement Algorithm을 이용해 기존 Page 중 하나를 선택해 제거하며, 해당 Page가 Dirty Page라면 변경 내용을 먼저 저장장치에 기록해야 할 수 있습니다.
>
> 이후 운영체제는 Page Table을 갱신하고 프로세스를 다시 실행 가능한 상태로 만든 뒤, Page Fault가 발생했던 명령어를 다시 실행합니다.
>
> TLB Miss와 Page Fault는 다른 개념입니다. TLB Miss는 단순히 주소 변환 정보가 TLB에 없는 것이며 Page Table에 유효한 Mapping이 존재하면 바로 해결할 수 있습니다. 반면 Page Fault는 Kernel의 처리가 필요한 메모리 접근 예외입니다.
>
> Page Fault 자체는 Demand Paging에서 정상적으로 발생할 수 있지만, 지나치게 자주 발생하면 Page 교체에 많은 시간을 사용하게 되어 Thrashing이 발생하고 성능이 크게 저하될 수 있습니다.

---

# 31. 메모리 파트 전체 연결

```text
Virtual Memory
↓
Virtual Address
↓
Paging
↓
Page Table
↓
TLB
↓
Demand Paging
↓
필요한 Page가 RAM에 없음
↓
Page Fault
↓
Page Replacement
↓
Page Fault가 너무 많음
↓
Thrashing
```

---

# 32. 복습 질문

### Q1. Page Fault란 무엇인가?

프로세스가 Virtual Address에 접근했을 때 현재 해당 접근을 정상적으로 처리할 수 없어 CPU가 Kernel에게 처리를 요청하는 메모리 예외다.

### Q2. Demand Paging에서 Page Fault가 발생하는 대표적인 이유는?

필요한 Page가 현재 RAM에 올라와 있지 않기 때문이다.

### Q3. Page Fault는 항상 비정상적인 오류인가?

아니다. Demand Paging에서는 필요한 Page를 처음 접근할 때 정상적으로 Page Fault가 발생할 수 있다.

### Q4. TLB Miss와 Page Fault의 차이는?

TLB Miss는 단지 주소 변환 결과가 TLB Cache에 없다는 뜻이다. Page Table에 유효한 Mapping이 존재하면 바로 해결할 수 있다. Page Fault는 현재 메모리 접근을 정상 처리할 수 없어 Kernel 처리가 필요한 상황이다.

### Q5. Page Fault가 발생하면 Kernel은 가장 먼저 무엇을 확인하는가?

해당 Virtual Address 접근이 Process에게 허용된 유효한 접근인지 확인한다.

### Q6. RAM에 빈 Frame이 없다면 어떻게 하는가?

Page Replacement Algorithm을 이용해 기존 Page 중 하나를 Victim으로 선택하고 해당 Frame을 재사용한다.

### Q7. Dirty Page란 무엇인가?

RAM에 올라온 후 Process가 내용을 수정하여 backing storage의 원본과 내용이 달라진 Page다.

### Q8. Dirty Page를 제거할 때 추가 비용이 필요한 이유는?

그냥 버리면 수정 내용이 사라질 수 있으므로 필요하면 변경 내용을 저장장치에 Write-back한 뒤 제거해야 하기 때문이다.

### Q9. Minor Page Fault와 Major Page Fault의 차이는?

Minor Page Fault는 일반적으로 Storage I/O 없이 해결할 수 있고, Major Page Fault는 필요한 Page를 Storage에서 읽어와야 하므로 I/O가 발생한다.

### Q10. Page Fault 처리 중 Storage I/O가 필요하면 Process 상태는 어떻게 되는가?

해당 Process는 Waiting / Blocked 상태가 될 수 있고, 그동안 Scheduler가 다른 Process를 실행할 수 있다.

### Q11. Page Fault 처리 후 왜 같은 명령어를 다시 실행하는가?

처음에는 필요한 Page가 없어 명령이 실패했지만 Page를 RAM에 적재하고 Page Table을 갱신한 뒤에는 같은 명령어가 정상적으로 수행될 수 있기 때문이다.

### Q12. Thrashing이란 무엇인가?

Page Fault와 Page Replacement가 지나치게 자주 발생해 시스템이 실제 프로그램 실행보다 Page를 넣고 빼는 데 대부분의 시간을 소비하는 상태다.

### Q13. Working Set과 Thrashing은 어떤 관계가 있는가?

Process의 Working Set을 유지할 만큼 충분한 Frame이 없으면 필요한 Page들이 계속 교체되면서 Page Fault가 증가하고 Thrashing이 발생할 수 있다.

### Q14. Page Fault와 Segmentation Fault는 같은가?

아니다. Page Fault는 CPU/MMU 수준의 메모리 접근 예외이며 정상적으로 처리될 수도 있다. Segmentation Fault는 허용되지 않은 메모리 접근을 OS가 Process에 오류로 전달한 결과 중 하나다.

### Q15. Page Fault가 Context Switching과 연결되는 이유는?

Page Fault 처리에 Storage I/O가 필요하면 현재 Process가 Waiting 상태가 되고, CPU를 놀리지 않기 위해 Scheduler가 다른 Process를 선택하면서 Context Switching이 발생할 수 있기 때문이다.
