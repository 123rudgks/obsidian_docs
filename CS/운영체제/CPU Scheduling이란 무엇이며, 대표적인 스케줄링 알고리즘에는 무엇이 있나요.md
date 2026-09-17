## 1. CPU Scheduling이란?

**CPU Scheduling**은 실행 가능한 여러 프로세스나 스레드 중 어떤 실행 대상에게 CPU를 언제, 얼마나 오래 할당할지 운영체제가 결정하는 과정이다.

CPU 코어의 수는 제한되어 있지만 실행해야 할 프로세스와 스레드는 많다.

```
Thread A
Thread B
Thread C
Thread D
    ↓
   CPU
```

따라서 운영체제는 다음과 같은 결정을 계속해야 한다.

```
"다음에는 누구를 실행할까?"
```

이 결정을 담당하는 것이 **CPU Scheduler**다.

전체 흐름은 다음과 같다.

```
Ready Queue
    ↓
Scheduler
    ↓
다음 실행 대상 선택
    ↓
Context Switching
    ↓
CPU에서 실행
```

---

# 2. Ready Queue

프로세스나 스레드의 대표적인 상태는 다음과 같다.

```
Ready
→ CPU만 받으면 실행 가능

Running
→ 현재 CPU에서 실행 중

Waiting / Blocked
→ I/O, Lock 등의 이벤트를 기다리는 중
```

Ready 상태의 실행 대상들이 CPU를 기다리는 곳을 **Ready Queue**라고 한다.

```
Ready Queue

[A][B][C][D]

      ↓
   Scheduler
      ↓
     CPU
```

Scheduler는 Ready Queue에서 다음 실행 대상을 선택한다.

---

# 3. CPU Scheduling의 목표

스케줄링은 단순히 어떤 작업이든 하나 골라 실행하는 것이 아니다.

운영체제는 여러 목표를 함께 고려해야 한다.

대표적인 목표:

- CPU를 최대한 놀리지 않기
    
- 작업을 빠르게 끝내기
    
- 사용자 요청에 빠르게 반응하기
    
- 여러 작업에 CPU를 공정하게 배분하기
    
- 특정 작업이 영원히 기다리지 않도록 하기
    
- 일정 시간 동안 최대한 많은 작업을 처리하기
    

이러한 목표는 서로 충돌할 수 있기 때문에 스케줄링에는 Trade-off가 존재한다.

---

# 4. Scheduling 성능 평가 기준

## CPU Utilization

CPU가 실제로 일을 하고 있는 비율이다.

```
CPU가 노는 시간 ↓
CPU Utilization ↑
```

일반적으로 CPU Utilization은 높을수록 좋다.

---

## Throughput

일정 시간 동안 완료한 작업의 수다.

예:

```
1초 동안 10개 작업 완료
→ Throughput = 10 jobs/sec
```

---

## Turnaround Time

프로세스가 시스템에 도착한 시점부터 완전히 종료될 때까지 걸린 전체 시간이다.

```
도착
↓
대기
↓
실행
↓
대기
↓
실행
↓
종료
```

수식:

```
Turnaround Time
= Completion Time - Arrival Time
```

---

## Waiting Time

프로세스가 **Ready Queue에서 CPU를 기다린 총 시간**이다.

CPU에서 실제 실행된 시간이나 I/O 대기 시간과는 구분해야 한다.

---

## Response Time

요청이 들어온 후 처음으로 CPU를 할당받아 반응하기까지 걸린 시간이다.

특히 사용자와 상호작용하는 시스템에서 중요하다.

```
요청
↓
Ready Queue
↓
처음 CPU 할당
```

---

# 5. 선점형과 비선점형 Scheduling

## 비선점형(Non-preemptive)

한 프로세스가 CPU를 받으면 종료하거나 스스로 CPU를 놓을 때까지 다른 프로세스가 강제로 CPU를 빼앗지 않는 방식이다.

```
A 실행
──────────────── 종료
                 ↓
                 B 실행
```

대표적인 예:

- FCFS
    
- 비선점형 SJF
    

장점:

- 구조가 단순함
    
- Context Switching이 상대적으로 적음
    

단점:

- 하나의 긴 작업이 CPU를 오래 점유하면 다른 작업의 응답성이 떨어질 수 있음
    

---

## 선점형(Preemptive)

운영체제가 실행 중인 프로세스나 스레드로부터 CPU를 강제로 회수할 수 있는 방식이다.

```
A 실행
──────
      ↓ Time Slice 종료

B 실행
──────
```

대표적인 예:

- Round Robin
    
- SRTF
    
- 선점형 Priority Scheduling
    

장점:

- 응답성을 높이기 좋음
    
- 특정 작업의 CPU 독점을 방지할 수 있음
    

단점:

- Context Switching이 더 자주 발생할 수 있음
    

---

# 6. Timer Interrupt와 선점

선점형 Scheduling에서는 CPU를 강제로 회수할 수 있어야 한다.

예를 들어 Round Robin에서 Thread A가 CPU를 계속 놓지 않는다고 하자.

운영체제는 Timer Interrupt를 이용해 일정 시간이 지나면 Kernel로 제어를 가져올 수 있다.

```
Thread A 실행
↓
Time Quantum 종료
↓
Timer Interrupt
↓
Kernel 진입
↓
Scheduler 실행
↓
Thread B 선택
↓
Context Switching
↓
Thread B 실행
```

이 덕분에 하나의 프로그램이 CPU를 영원히 독점하는 것을 막을 수 있다.

---

# 7. FCFS

**First Come, First Served**

먼저 Ready Queue에 들어온 작업부터 실행하는 방식이다.

예:

```
도착 순서

A → B → C
```

실행 순서:

```
A → B → C
```

Queue의 FIFO와 비슷하다.

## 장점

- 구현이 단순함
    
- 직관적임
    

## 단점

긴 작업이 앞에 있으면 뒤의 짧은 작업들도 오래 기다리게 된다.

예:

```
A = 100ms
B = 1ms
C = 1ms
```

FCFS:

```
A ─────────────────────────
                          B
                           C
```

이처럼 긴 작업 때문에 짧은 작업들이 줄줄이 기다리는 현상을 **Convoy Effect**라고 한다.

---

# 8. SJF

**Shortest Job First**

다음 CPU Burst가 가장 짧은 작업부터 실행하는 방식이다.

예:

```
A = 10ms
B = 2ms
C = 4ms
```

실행:

```
B → C → A
```

SJF는 평균 Waiting Time을 줄이는 데 유리하다.

짧은 작업들을 먼저 끝내면 많은 작업들이 긴 작업 뒤에서 기다리지 않아도 되기 때문이다.

---

# 9. CPU Burst란?

프로세스는 실행되는 동안 CPU만 계속 사용하는 것이 아니다.

일반적으로 다음과 같이 CPU 사용과 I/O 대기를 반복할 수 있다.

```
CPU 사용 5ms
↓
Disk I/O 대기
↓
CPU 사용 2ms
↓
Network I/O 대기
↓
CPU 사용 8ms
```

여기서 각각의:

```
5ms
2ms
8ms
```

가 **CPU Burst**다.

즉 CPU Burst는:

> **프로세스가 CPU를 할당받아 실행하다가 I/O 대기 등으로 CPU를 놓기 전까지 연속적으로 CPU를 사용하는 구간**

이라고 볼 수 있다.

---

# 10. SJF는 CPU Burst를 어떻게 아는가?

여기서 중요한 점이 있다.

SJF 알고리즘 자체를 설명할 때는 다음 CPU Burst 시간을 알고 있다고 가정한다.

```
A 다음 CPU Burst = 10ms
B 다음 CPU Burst = 3ms
C 다음 CPU Burst = 5ms
```

그러면:

```
B → C → A
```

를 선택할 수 있다.

하지만 현실의 운영체제는 미래에 특정 프로세스가 CPU를 정확히 몇 ms 동안 사용할지 미리 알기 어렵다.

프로그램은 입력값이나 조건에 따라 실행 경로가 달라질 수 있기 때문이다.

```
if 조건 A
→ 짧은 계산

else
→ 긴 계산
```

따라서 현실에서는 과거 CPU Burst 기록을 이용해 다음 Burst 시간을 예측할 수 있다.

```
과거 Burst
4ms
5ms
4ms
6ms
5ms

↓
다음 Burst도 약 5ms일 것이라고 예측
```

고전적인 운영체제 이론에서는 exponential averaging 같은 방식으로 과거 예측값과 최근 실제 Burst를 조합하기도 한다.

핵심:

```
SJF 이론
→ 다음 CPU Burst를 안다고 가정

실제 OS
→ 미래 Burst를 정확히 알 수 없음
→ 과거 기록 등으로 예측
```

---

# 11. SRTF

**Shortest Remaining Time First**

SJF의 선점형 버전이라고 볼 수 있다.

현재 남아 있는 CPU Burst 시간이 가장 짧은 작업을 실행한다.

예:

```
Process A 실행 중
남은 시간 = 8ms

Process B 새로 도착
Burst = 2ms
```

SRTF에서는:

```
A 중단
↓
B 실행
↓
B 종료
↓
A 다시 실행
```

할 수 있다.

차이:

```
SJF
→ 가장 짧은 CPU Burst
→ 기본적으로 비선점

SRTF
→ 남은 CPU Burst가 가장 짧은 작업
→ 선점 가능
```

---

# 12. Starvation

SJF, SRTF, Priority Scheduling 등에서는 특정 작업이 계속 밀릴 수 있다.

예를 들어 긴 작업 A가 기다리고 있다고 하자.

```
A = 100ms
```

그런데 계속 짧은 작업이 들어온다.

```
B = 1ms
C = 2ms
D = 1ms
E = 3ms
...
```

그러면 A는 계속 선택되지 못할 수 있다.

```
A
↓
계속 대기
↓
계속 대기
↓
...
```

이처럼 실행 가능한 상태인데도 계속 우선순위에서 밀려 CPU를 받지 못하는 현상을 **Starvation(기아 상태)**이라고 한다.

---

# 13. Round Robin

**Round Robin(RR)**은 각 실행 대상에게 일정 시간씩 돌아가며 CPU를 할당한다.

예:

```
Time Quantum = 2ms
```

Ready Queue:

```
A B C
```

실행:

```
A 2ms
↓
B 2ms
↓
C 2ms
↓
A 2ms
↓
...
```

즉:

```
A → B → C → A → B → C ...
```

처럼 순환한다.

Round Robin은 사용자와 상호작용하는 시스템에서 응답성을 높이는 데 유리하다.

---

# 14. Time Quantum

Round Robin에서 한 작업이 한 번에 사용할 수 있는 CPU 시간을 **Time Quantum** 또는 **Time Slice**라고 한다.

Time Quantum 크기는 매우 중요하다.

## 너무 큰 경우

```
A ─────────────────
                  B
```

한 작업이 CPU를 오래 사용하기 때문에 FCFS와 비슷해질 수 있다.

## 너무 작은 경우

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

Context Switching이 지나치게 자주 발생한다.

따라서:

```
Quantum 너무 큼
→ 응답성 저하
→ FCFS에 가까워짐

Quantum 너무 작음
→ Context Switching Overhead 증가
```

라는 Trade-off가 존재한다.

---

# 15. Priority Scheduling

각 실행 대상에 Priority를 부여하고 우선순위가 높은 작업을 먼저 실행하는 방식이다.

예:

```
A → Priority 1
B → Priority 5
C → Priority 10
```

숫자가 클수록 우선순위가 높다고 가정하면:

```
C → B → A
```

순으로 실행할 수 있다.

Priority Scheduling은 선점형과 비선점형 모두 가능하다.

---

# 16. Priority Scheduling과 Starvation

높은 우선순위 작업이 계속 들어오면 낮은 우선순위 작업이 계속 밀릴 수 있다.

```
Low Priority A

High Priority 작업 계속 도착
↓
A 계속 대기
```

따라서 Priority Scheduling에서도 Starvation이 발생할 수 있다.

---

# 17. Aging

Starvation을 완화하는 대표적인 방법이 **Aging**이다.

오래 기다린 작업의 우선순위를 점점 높여주는 방식이다.

```
처음
A Priority = 1

10초 대기
→ Priority = 2

20초 대기
→ Priority = 3

30초 대기
→ Priority = 4
```

충분히 오래 기다리면 언젠가는 높은 우선순위를 얻어 CPU를 사용할 수 있게 한다.

---

# 18. 대표 알고리즘 비교

|알고리즘|기준|선점 여부|대표 문제|
|---|---|---|---|
|FCFS|먼저 도착|비선점|Convoy Effect|
|SJF|가장 짧은 다음 CPU Burst|비선점|Burst 예측, Starvation|
|SRTF|가장 짧은 남은 CPU Burst|선점|Starvation, Context Switching|
|Round Robin|일정 Time Quantum씩 순환|선점|Quantum 설정|
|Priority|높은 우선순위|둘 다 가능|Starvation|

---

# 19. CPU-bound와 I/O-bound

Scheduling을 이해할 때 작업의 특성도 중요하다.

## CPU-bound

CPU 계산을 오래 수행하는 작업이다.

예:

- 영상 인코딩
    
- 압축
    
- 복잡한 수치 계산
    

패턴:

```
CPU 사용
────────────────
```

---

## I/O-bound

CPU는 짧게 사용하고 I/O를 자주 기다리는 작업이다.

예:

- 네트워크 요청
    
- DB 조회
    
- 파일 읽기
    
- 사용자 입력
    

패턴:

```
CPU
↓
I/O Wait
↓
CPU
↓
I/O Wait
```

---

# 20. I/O-bound 작업을 적절히 빠르게 실행하면 좋은 이유

예를 들어 I/O-bound Thread A가:

```
CPU 5ms
↓
Disk I/O 시작
↓
Waiting
```

한다고 하자.

A가 빨리 CPU를 받아 I/O를 시작하면, 이후 A가 I/O를 기다리는 동안 CPU는 다른 작업 B를 실행할 수 있다.

```
A
↓
I/O 시작

B
↓
CPU 사용

동시에
A의 I/O 진행
```

즉 CPU와 I/O 장치를 동시에 활용할 수 있어 전체 시스템의 자원 활용도를 높일 수 있다.

---

# 21. Scheduler와 Context Switching

이전 질문에서 배운 내용과 직접 연결된다.

```
Ready Queue
     ↓
Scheduler
     ↓
"다음은 B"
     ↓
Context Switching
     ↓
A의 Context 저장
     ↓
B의 Context 복원
     ↓
CPU에서 B 실행
```

핵심:

```
Scheduler
→ 다음 실행 대상을 선택

Context Switching
→ 실제 CPU 실행 대상을 변경
```

---

# 22. 실제 운영체제의 Scheduling

현대 운영체제는 단순히 하나의 고전적인 알고리즘만 사용하는 경우가 드물다.

실제로는 다음과 같은 여러 요소를 함께 고려할 수 있다.

- 공정성
    
- 응답성
    
- 우선순위
    
- CPU 사용 패턴
    
- 실시간 요구
    
- CPU-bound / I/O-bound 특성
    

따라서 FCFS, SJF, RR 등의 알고리즘은 실제 운영체제 Scheduling을 이해하기 위한 기본 원리라고 보는 것이 좋다.

---

# 23. 핵심 개념 정리

## CPU Scheduling

**Ready 상태의 여러 실행 대상 중 어떤 작업에게 CPU를 할당할지 결정하는 것**

## Ready Queue

**CPU만 받으면 실행할 수 있는 작업들이 대기하는 Queue**

## Preemptive Scheduling

**운영체제가 실행 중인 작업으로부터 CPU를 강제로 회수할 수 있는 방식**

## Non-preemptive Scheduling

**작업이 종료되거나 스스로 CPU를 놓을 때까지 CPU를 유지하는 방식**

## FCFS

**먼저 들어온 작업부터 실행**

## SJF

**다음 CPU Burst가 가장 짧은 작업부터 실행**

## SRTF

**현재 남은 CPU Burst가 가장 짧은 작업부터 실행**

## Round Robin

**일정 Time Quantum씩 순환하면서 CPU 할당**

## Priority Scheduling

**우선순위가 높은 작업부터 실행**

## Starvation

**실행 가능한데도 계속 다른 작업에 밀려 CPU를 받지 못하는 상태**

## Aging

**오래 기다릴수록 우선순위를 높여 Starvation을 완화하는 방법**

---

# 24. 면접 답변

> CPU Scheduling은 Ready 상태에 있는 여러 프로세스나 스레드 중 어떤 실행 대상에게 CPU를 할당할지 결정하는 운영체제의 기능입니다.
> 
> 대표적인 알고리즘으로는 먼저 도착한 작업부터 실행하는 FCFS, 다음 CPU Burst가 가장 짧은 작업을 선택하는 SJF와 그 선점형 버전인 SRTF, 일정한 Time Quantum만큼 CPU를 순환해서 할당하는 Round Robin, 그리고 우선순위에 따라 실행하는 Priority Scheduling 등이 있습니다.
> 
> 비선점형 Scheduling은 한 작업이 CPU를 얻으면 종료하거나 대기 상태가 될 때까지 CPU를 사용하는 방식이고, 선점형 Scheduling은 Timer Interrupt 등을 이용하여 운영체제가 실행 중인 작업에서 CPU를 회수할 수 있는 방식입니다.
> 
> 각 알고리즘은 Waiting Time, Response Time, Throughput, 공정성 등의 측면에서 Trade-off가 있습니다. 예를 들어 Round Robin은 응답성을 높일 수 있지만 Time Quantum이 너무 작으면 Context Switching Overhead가 증가할 수 있습니다.
> 
> 또한 SJF는 평균 Waiting Time을 줄이는 데 유리하지만 미래의 CPU Burst를 정확히 알기 어렵기 때문에 실제로는 과거 실행 기록 등을 통해 Burst 시간을 예측해야 한다는 한계가 있습니다.

---

# 25. 전체 구조 한 장 정리

```
Ready Queue
    ↓
Scheduler
    ↓
Scheduling Policy

├─ FCFS
│   → 먼저 온 작업
│   → Convoy Effect
│
├─ SJF
│   → 가장 짧은 CPU Burst
│   → Burst 예측 필요
│   → Starvation 가능
│
├─ SRTF
│   → 가장 짧은 Remaining Time
│   → 선점
│
├─ Round Robin
│   → 일정 Time Quantum
│   → 응답성
│   → Context Switching 비용
│
└─ Priority
    → 높은 Priority
    → Starvation
    → Aging으로 완화

        ↓

다음 실행 대상 결정
        ↓
Context Switching
        ↓
CPU 실행
```

---

# 26. 복습 질문

### Q1. CPU Scheduling이 필요한 이유는 무엇인가?

CPU 코어 수보다 실행해야 할 프로세스와 스레드가 많기 때문에 어떤 실행 대상에게 CPU를 줄지 결정해야 하기 때문이다.

### Q2. Ready 상태와 Waiting 상태의 차이는?

Ready는 CPU만 받으면 즉시 실행할 수 있는 상태이고, Waiting은 I/O나 Lock 등의 이벤트가 완료되기 전까지 CPU를 받아도 실행할 수 없는 상태다.

### Q3. 선점형과 비선점형 Scheduling의 차이는?

선점형에서는 운영체제가 실행 중인 작업으로부터 CPU를 강제로 회수할 수 있지만, 비선점형에서는 작업이 종료하거나 스스로 CPU를 놓을 때까지 CPU를 유지한다.

### Q4. FCFS의 대표적인 문제는 무엇인가?

긴 작업이 앞에 위치하면 뒤의 짧은 작업들이 오래 기다리는 Convoy Effect가 발생할 수 있다.

### Q5. SJF는 CPU Burst를 기반으로 선택하는데 왜 실제 시스템에서 그대로 사용하기 어려운가?

SJF 자체는 다음 CPU Burst 시간을 안다고 가정하지만 실제 운영체제는 미래 CPU Burst를 정확히 알 수 없기 때문이다. 따라서 과거 Burst 기록 등을 통해 예측해야 한다.

### Q6. SJF와 SRTF의 차이는?

SJF는 가장 짧은 CPU Burst를 가진 작업을 선택하는 비선점형 방식이고, SRTF는 현재 남은 CPU Burst가 가장 짧은 작업을 선택하며 필요하면 실행 중인 작업을 선점할 수 있다.

### Q7. Round Robin의 Time Quantum이 너무 작으면 어떻게 되는가?

CPU가 실행 대상을 지나치게 자주 바꾸게 되어 Context Switching Overhead가 증가한다.

### Q8. Time Quantum이 너무 크면 어떻게 되는가?

한 작업이 CPU를 오래 사용하므로 응답성이 떨어지고 Round Robin이 FCFS와 비슷하게 동작하게 된다.

### Q9. Starvation이란?

실행 가능한 상태인데도 계속 다른 작업에 우선순위를 빼앗겨 CPU를 받지 못하는 상태다.

### Q10. Aging은 무엇인가?

오랫동안 기다린 작업의 우선순위를 점점 높여 언젠가는 CPU를 받을 수 있도록 하여 Starvation을 완화하는 방법이다.

### Q11. CPU-bound와 I/O-bound의 차이는?

CPU-bound는 CPU 연산을 오래 수행하는 작업이고, I/O-bound는 CPU를 짧게 사용한 뒤 I/O 대기를 자주 반복하는 작업이다.

### Q12. Scheduler와 Context Switching의 차이는?

Scheduler는 다음 실행 대상을 선택하고, Context Switching은 선택된 대상으로 실제 CPU 실행 상태를 변경한다.
