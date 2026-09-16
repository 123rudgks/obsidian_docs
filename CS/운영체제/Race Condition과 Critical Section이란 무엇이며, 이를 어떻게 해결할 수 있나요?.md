## 1. 왜 동시성 문제가 발생하는가?

같은 Process 내부의 여러 Thread는 다음과 같은 자원을 공유할 수 있다.

```
Process

공유
├─ Code
├─ Data
├─ Heap
└─ 기타 Process Resource

Thread별 독립
├─ Stack
├─ Register
└─ Program Counter
```

Thread들이 Heap이나 Data에 존재하는 같은 데이터를 동시에 읽고 수정할 수 있기 때문에 동시성 문제가 발생할 수 있다.

핵심 흐름:

```
여러 Thread가 공유 데이터 접근
        ↓
실행 순서가 매번 달라질 수 있음
        ↓
결과가 달라질 수 있음
        ↓
Race Condition
        ↓
공유 자원 접근 코드 보호
        ↓
Critical Section
        ↓
Synchronization
```

---

# 2. Race Condition이란?

**Race Condition**은 여러 프로세스나 스레드가 공유 자원에 동시에 접근할 때, 실행 순서에 따라 프로그램의 결과가 달라질 수 있는 문제다.

예를 들어 Heap에 다음 값이 있다고 하자.

```
count = 0
```

Thread A와 Thread B가 동시에 다음 코드를 실행한다.

```
count++;
```

직관적으로는:

```
Thread A → +1
Thread B → +1

결과 = 2
```

를 기대한다.

하지만 `count++`는 CPU 수준에서 하나의 연산으로 끝나는 것이 아닐 수 있다.

개념적으로는:

```
1. count 값을 읽는다.
2. 1을 더한다.
3. 결과를 다시 저장한다.
```

로 나뉠 수 있다.

---

# 3. count++에서 Race Condition이 발생하는 과정

초기값:

```
count = 0
```

다음과 같은 실행 순서가 가능하다.

```
Thread A: count 읽기 → 0

Thread B: count 읽기 → 0

Thread A: 0 + 1 → 1
Thread A: count = 1 저장

Thread B: 0 + 1 → 1
Thread B: count = 1 저장
```

최종 결과:

```
count = 1
```

원래 기대한 결과는 `2`였지만 `1`이 되었다.

문제의 원인은 여러 Thread의 연산이 서로 끼어들면서 실행되었기 때문이다.

---

# 4. 왜 결과가 항상 잘못되는 것은 아닌가?

Thread의 실제 실행 순서는 Scheduling과 Context Switching 등에 따라 달라질 수 있다.

어떤 실행에서는:

```
Thread A
읽기
계산
저장

Thread B
읽기
계산
저장
```

되어 결과가 정상적으로 `2`가 될 수 있다.

하지만 다른 실행에서는:

```
A 읽기
↓
Context Switching
↓
B 읽기
B 계산
B 저장
↓
Context Switching
↓
A 계산
A 저장
```

처럼 섞일 수도 있다.

따라서 Race Condition은:

> **실행 결과가 여러 실행 흐름의 상대적인 실행 타이밍과 순서에 의존하는 문제**

라고 볼 수 있다.

---

# 5. Race Condition이 발생하기 쉬운 조건

Race Condition은 단순히 Thread가 여러 개 있다는 이유만으로 발생하는 것은 아니다.

예를 들어:

```
Thread A → 데이터 A만 사용
Thread B → 데이터 B만 사용
```

한다면 문제가 없을 수 있다.

또 동일한 데이터를 여러 Thread가 읽기만 한다면 일반적으로 문제가 되지 않는다.

```
Thread A ─┐
          ↓
        value
          ↑
Thread B ─┘

둘 다 읽기만 함
```

보통 문제가 되는 상황은 다음 요소들이 함께 존재할 때다.

```
Shared
+
Mutable
+
Concurrent Access
=
Race Condition 가능
```

즉:

> **여러 실행 흐름이 공유되는 변경 가능한 상태(shared mutable state)에 동시에 접근하고, 그중 하나 이상이 값을 수정할 때 Race Condition이 발생할 수 있다.**

---

# 6. 단일 코어에서도 Race Condition이 발생하는가?

그렇다.

Race Condition은 반드시 CPU 코어가 여러 개여야만 발생하는 것이 아니다.

단일 코어에서도 다음과 같이 Thread 실행이 번갈아 진행될 수 있다.

```
Thread A 실행
↓
Context Switching
↓
Thread B 실행
↓
Context Switching
↓
Thread A 실행
```

따라서 하나의 연산 중간에 다른 Thread가 실행되어 공유 데이터를 수정하면 Race Condition이 발생할 수 있다.

즉:

```
병렬 실행(Parallelism)이 없어도
동시성(Concurrency)이 있으면
Race Condition은 가능
```

하다.

---

# 7. Process끼리도 Race Condition이 발생할 수 있는가?

가능하다.

기본적으로 서로 다른 Process는 독립적인 가상 주소 공간을 가지므로 메모리를 바로 공유하지 않는다.

하지만 Shared Memory 등의 IPC를 사용하면 동일한 메모리에 접근할 수 있다.

```
Process A ─┐
           ↓
      Shared Memory
           ↑
Process B ─┘
```

두 Process가 같은 Shared Memory를 동시에 수정하면 Thread와 마찬가지로 Race Condition이 발생할 수 있다.

따라서 Race Condition은 Thread만의 문제가 아니라:

> **여러 실행 주체가 공유 자원에 동시에 접근하는 문제**

라고 보는 것이 정확하다.

---

# 8. Critical Section이란?

**Critical Section(임계 영역)**은 공유 자원에 접근하는 코드 중 여러 실행 흐름이 동시에 실행하면 문제가 발생할 수 있는 영역이다.

예를 들어:

```
count++;
```

가 공유 변수 `count`를 수정하고 있다면 이 부분이 Critical Section이 될 수 있다.

```
Thread A
     │
     ↓
┌──────────────────┐
│ Critical Section │
│ count++          │
└──────────────────┘
     ↑
     │
Thread B
```

Critical Section에는 여러 Thread가 동시에 들어가지 못하도록 제어해야 한다.

---

# 9. Synchronization이란?

**Synchronization(동기화)**은 여러 실행 흐름이 공유 자원에 접근하는 순서를 조정하여 올바른 결과가 나오도록 하는 것이다.

우리가 원하는 가장 기본적인 동작은 다음과 같다.

```
Thread A가 Critical Section 실행
↓
Thread B는 기다림
↓
Thread A 종료
↓
Thread B 진입
```

즉 Critical Section 접근을 제어하는 것이 Synchronization의 핵심이다.

---

# 10. Mutual Exclusion

Critical Section 문제에서 중요한 원칙 중 하나가 **Mutual Exclusion(상호 배제)**이다.

> **한 실행 흐름이 Critical Section을 실행 중이라면 다른 실행 흐름이 동시에 같은 Critical Section에 들어가지 못하도록 하는 것**

```
Thread A → Critical Section

Thread B → 대기
```

A가 Critical Section에서 빠져나오면:

```
Thread A → 종료

Thread B → Critical Section 진입
```

할 수 있다.

---

# 11. Mutex

**Mutex**는 **Mutual Exclusion**에서 나온 말이다.

한 Thread가 Mutex를 획득하면 다른 Thread는 해당 Mutex가 해제될 때까지 기다려야 한다.

```
Mutex = unlocked
```

Thread A:

```
Lock 획득
↓
Critical Section
↓
Unlock
```

그동안 Thread B:

```
Lock 요청
↓
이미 A가 소유
↓
대기
```

전체 흐름:

```
Thread A
Lock
↓
count++
↓
Unlock

Thread B
       기다림
         ↓
        Lock
         ↓
      count++
         ↓
       Unlock
```

이렇게 하면 `count++` 전체를 보호할 수 있다.

---

# 12. Mutex의 핵심: 소유권

Mutex는 일반적으로 **Lock을 획득한 실행 흐름이 Lock을 해제한다**는 소유권 개념을 가진다.

```
Thread A
→ Mutex 획득

Thread B
→ 해당 Mutex를 임의로 해제할 수 없음
```

이 특징은 Semaphore와 구분하는 데 도움이 된다.

---

# 13. Semaphore

**Semaphore**는 동시에 접근할 수 있는 자원의 개수를 숫자로 관리하는 동기화 도구다.

예를 들어 사용할 수 있는 DB Connection이 3개 있다고 하자.

```
Semaphore = 3
```

Thread A가 하나 사용:

```
Semaphore = 2
```

Thread B:

```
Semaphore = 1
```

Thread C:

```
Semaphore = 0
```

Thread D가 추가로 사용하려 하면:

```
Thread D
↓
사용 가능한 자원 없음
↓
대기
```

누군가 자원을 반환하면 다시 사용할 수 있다.

```
Connection 반환
↓
Semaphore 증가
↓
기다리던 Thread가 사용 가능
```

---

# 14. Mutex와 Semaphore 비교

처음에는 다음과 같이 구분하면 된다.

```
Mutex
→ 보통 한 번에 하나만 접근
→ 소유권 개념이 강함

Semaphore
→ 동시에 N개까지 접근 가능
→ 사용 가능한 자원의 개수를 관리
```

Semaphore 값을 0과 1만 사용하면 **Binary Semaphore**라고 한다.

Binary Semaphore와 Mutex는 비슷하게 보이지만 개념적으로는 다르다.

---

# 15. Lock이란?

**Lock**은 공유 자원에 대한 동시 접근을 막기 위한 동기화 메커니즘을 넓게 표현하는 말이다.

개념적인 패턴:

```
Lock 획득
↓
Critical Section
↓
Lock 해제
```

Mutex는 Lock의 대표적인 형태라고 볼 수 있다.

Java에서는 다음과 같은 기능들이 있다.

```
synchronized
```

또는:

```
ReentrantLock
```

등을 사용해 Critical Section을 보호할 수 있다.

---

# 16. Atomic Operation

동기화 문제를 해결하는 방법이 반드시 Lock뿐인 것은 아니다.

**Atomic Operation**은 중간 단계가 다른 실행 흐름에게 관찰되지 않도록 하나의 원자적인 작업처럼 수행되는 연산이다.

`count++`는 일반적으로 다음 단계로 나뉠 수 있다.

```
read
↓
+1
↓
write
```

그래서 Race Condition이 발생할 수 있다.

반면 특정 연산이 Atomic하게 수행되면 다른 Thread가 중간 상태에 끼어들 수 없다.

Java에서는 예를 들어:

```
AtomicInteger
```

를 사용할 수 있다.

```
count.incrementAndGet();
```

처럼 원자적인 증가 연산을 제공할 수 있다.

---

# 17. Context Switching과 Race Condition의 관계

Context Switching 자체가 Race Condition인 것은 아니다.

Context Switching은 운영체제가 여러 실행 흐름을 번갈아 실행하기 위해 사용하는 정상적인 기능이다.

하지만 Context Switching 때문에 연산 중간에 다른 Thread가 실행될 수 있다.

```
Thread A
공유 값 읽기
↓
Context Switching
↓
Thread B
공유 값 읽기
공유 값 수정
↓
Context Switching
↓
Thread A
기존 값을 기반으로 수정
```

따라서 다음 요소들이 결합되면 Race Condition이 발생할 수 있다.

```
Concurrency
+
Shared Mutable State
+
실행 순서가 보장되지 않음
↓
Race Condition
```

---

# 18. Critical Section은 작게 유지하는 것이 좋다

Race Condition을 막겠다고 너무 많은 코드를 하나의 Lock으로 보호하면 성능이 떨어질 수 있다.

나쁜 예:

```
Lock
↓
긴 계산
↓
네트워크 요청
↓
DB 요청
↓
공유 변수 수정
↓
Unlock
```

이 경우 Lock을 기다리는 다른 Thread들은 전체 과정이 끝날 때까지 기다려야 한다.

가능하다면 다음과 같이 실제 공유 자원 접근 부분만 보호하는 것이 좋다.

```
일반 계산
↓
Lock
↓
공유 데이터 수정
↓
Unlock
↓
나머지 작업
```

즉:

> **Critical Section은 안전성을 보장할 수 있는 범위에서 가능한 작게 유지하는 것이 좋다.**

---

# 19. Synchronization에도 비용이 있다

Synchronization은 Race Condition을 해결하지만 비용도 발생한다.

```
Thread A
Critical Section

Thread B
대기

Thread C
대기
```

원래 여러 Thread를 이용해 동시에 실행하려 했지만 Critical Section에서는 결국 한 줄로 실행될 수 있다.

또한:

- Lock 획득/해제 비용
    
- 대기 비용
    
- Context Switching 가능성
    
- Cache 효율 변화
    

등도 발생할 수 있다.

따라서:

```
Synchronization 부족
→ Race Condition

Synchronization 과도
→ 동시성 저하 / 성능 저하
```

라는 Trade-off가 존재한다.

---

# 20. Lock을 잘못 사용하면 Deadlock이 발생한다

Synchronization을 위해 여러 Lock을 사용하다 보면 또 다른 문제가 발생할 수 있다.

```
Thread A
Lock 1 획득
↓
Lock 2 기다림


Thread B
Lock 2 획득
↓
Lock 1 기다림
```

이제:

```
A는 B가 Lock 2를 놓기를 기다림

B는 A가 Lock 1을 놓기를 기다림
```

둘 다 진행할 수 없다.

이 상태를 **Deadlock(교착 상태)**이라고 한다.

따라서 흐름은 다음과 같이 이어진다.

```
Shared Resource
↓
Race Condition
↓
Synchronization 필요
↓
Lock
↓
Lock 관리 실패
↓
Deadlock 가능
```

---

# 21. Race Condition과 Critical Section 비교

|개념|의미|
|---|---|
|Race Condition|실행 순서에 따라 공유 데이터의 결과가 달라질 수 있는 문제|
|Critical Section|공유 자원에 접근하여 동시에 실행하면 문제가 될 수 있는 코드 영역|
|Synchronization|여러 실행 흐름의 공유 자원 접근 순서를 조정하는 것|
|Mutual Exclusion|한 번에 하나의 실행 흐름만 Critical Section에 진입하도록 하는 성질|
|Mutex|하나의 공유 자원에 대한 상호 배제에 주로 사용|
|Semaphore|동시에 접근 가능한 자원의 개수를 관리|
|Atomic Operation|중간 상태가 끼어들지 못하도록 원자적으로 수행되는 연산|
|Deadlock|실행 주체들이 서로 필요한 자원을 기다리며 진행하지 못하는 상태|

---

# 22. 이전 질문과의 연결

지금까지 배운 OS 개념을 이어보면 다음과 같다.

```
Process
↓
여러 Thread

Thread들이
Code / Data / Heap 공유
↓
공유 데이터 접근

Scheduler가 CPU를 나누어 할당
↓
Context Switching

실행 순서가 예측하기 어려움
↓
Race Condition

문제가 발생할 수 있는 코드
↓
Critical Section

접근 순서 제어
↓
Synchronization

├─ Mutex
├─ Semaphore
├─ Lock
└─ Atomic Operation

Lock 관리 실패
↓
Deadlock
```

---

# 23. 핵심 개념 정리

## Race Condition

**공유되는 변경 가능한 자원에 여러 실행 흐름이 접근할 때 실행 순서에 따라 결과가 달라질 수 있는 문제**

## Critical Section

**공유 자원을 읽거나 수정하는 코드 중 동시에 실행하면 문제가 발생할 수 있는 영역**

## Synchronization

**여러 실행 흐름의 공유 자원 접근 순서를 제어하는 것**

## Mutual Exclusion

**Critical Section에 한 번에 하나의 실행 흐름만 들어갈 수 있도록 하는 것**

## Mutex

**하나의 실행 흐름이 Lock을 소유하여 다른 실행 흐름의 접근을 막는 동기화 도구**

## Semaphore

**동시에 사용할 수 있는 자원의 개수를 숫자로 관리하는 동기화 도구**

## Atomic Operation

**중간 단계로 분리되어 다른 실행 흐름에게 관찰되지 않는 원자적 연산**

---

# 24. 면접 답변

> Race Condition은 여러 프로세스나 스레드가 공유 자원에 동시에 접근할 때 실행 순서에 따라 결과가 달라질 수 있는 문제입니다.
> 
> 예를 들어 두 Thread가 하나의 공유 변수를 동시에 증가시키는 경우, `count++`가 실제로는 값을 읽고 수정하고 다시 저장하는 여러 단계로 수행될 수 있기 때문에 각 Thread의 실행 순서가 섞이면서 증가 결과가 유실될 수 있습니다.
> 
> 이처럼 공유 자원에 접근하는 코드 중 동시에 실행되면 문제가 발생할 수 있는 영역을 Critical Section이라고 합니다.
> 
> 이를 해결하기 위해 여러 실행 흐름의 접근 순서를 제어하는 Synchronization이 필요하며, 대표적으로 Mutex, Semaphore, Lock, Atomic Operation 등을 사용할 수 있습니다. Mutex는 주로 하나의 실행 흐름만 Critical Section에 접근하도록 만들고, Semaphore는 동시에 사용할 수 있는 자원의 개수를 관리하는 데 사용할 수 있습니다.
> 
> 다만 Synchronization을 과도하게 사용하면 Thread들이 Lock을 기다리면서 동시성이 떨어지고 Context Switching 등의 비용이 증가할 수 있으며, 여러 Lock을 잘못 관리하면 Deadlock이 발생할 수 있기 때문에 Critical Section을 필요한 범위만큼 적절하게 설정하는 것이 중요합니다.

---

# 25. 전체 구조 한 장 정리

```
같은 Process의 여러 Thread
        ↓
Code / Data / Heap 공유
        ↓
Shared Mutable State
        ↓
동시에 접근
        ↓
Race Condition 가능
        ↓
Critical Section 식별
        ↓
Synchronization

├─ Mutex
│   → 한 번에 하나
│
├─ Semaphore
│   → 동시에 N개까지
│
├─ Lock
│   → 공유 자원 접근 제어
│
└─ Atomic Operation
    → 원자적 연산

        ↓

잘못 설계하면
├─ 성능 저하
└─ Deadlock 가능
```

---

# 26. 복습 질문

### Q1. Race Condition이란 무엇인가?

여러 실행 흐름이 공유되는 변경 가능한 상태에 접근할 때 실행 순서에 따라 결과가 달라질 수 있는 문제다.

### Q2. `count++`는 한 줄인데 왜 Race Condition이 발생할 수 있는가?

소스 코드에서는 한 줄이지만 CPU 수준에서는 값을 읽고, 증가시키고, 다시 저장하는 여러 연산으로 나뉠 수 있기 때문이다.

### Q3. Critical Section이란 무엇인가?

공유 자원에 접근하는 코드 중 여러 실행 흐름이 동시에 실행하면 잘못된 결과가 발생할 수 있는 영역이다.

### Q4. Race Condition과 Critical Section의 관계는?

Race Condition은 문제 현상이고, Critical Section은 그 문제가 발생할 수 있어 동시 접근을 제어해야 하는 코드 영역이다.

### Q5. Synchronization이 필요한 이유는?

여러 Thread가 공유 자원에 동시에 접근할 때 올바른 결과를 보장하도록 실행 순서를 조정하기 위해서다.

### Q6. Mutex란 무엇인가?

한 실행 흐름이 Lock을 획득하면 해당 Lock이 해제될 때까지 다른 실행 흐름이 같은 Critical Section에 접근하지 못하도록 하는 상호 배제 도구다.

### Q7. Mutex와 Semaphore의 가장 기본적인 차이는?

Mutex는 주로 한 번에 하나의 실행 흐름만 접근하도록 하는 데 사용되고, Semaphore는 동시에 사용할 수 있는 자원의 개수를 숫자로 관리한다.

### Q8. Binary Semaphore와 Mutex는 완전히 같은가?

동작이 비슷할 수 있지만 개념적으로는 다르다. Mutex는 Lock의 소유권 개념이 강하고, Semaphore는 사용 가능한 자원의 개수를 관리하는 개념이다.

### Q9. Atomic Operation이란 무엇인가?

다른 실행 흐름이 중간 상태에 끼어들 수 없도록 하나의 원자적인 작업처럼 수행되는 연산이다.

### Q10. 단일 코어에서도 Race Condition이 발생할 수 있는가?

가능하다. Context Switching으로 여러 Thread의 명령어 실행 순서가 섞일 수 있기 때문이다.

### Q11. 서로 다른 Process 사이에서도 Race Condition이 발생할 수 있는가?

가능하다. Shared Memory처럼 두 Process가 동일한 공유 자원에 접근한다면 Race Condition이 발생할 수 있다.

### Q12. 모든 코드를 Mutex로 감싸면 왜 안 되는가?

Race Condition은 줄일 수 있지만 여러 Thread가 Lock을 기다리면서 동시성이 크게 떨어지고 Lock 관리 비용과 Context Switching 비용이 증가할 수 있기 때문이다.

### Q13. Critical Section을 가능한 작게 만드는 이유는?

Lock을 보유하는 시간을 줄여 다른 Thread의 대기 시간을 줄이고 동시성을 높이기 위해서다.

### Q14. Synchronization과 Deadlock은 어떻게 연결되는가?

공유 자원을 보호하기 위해 여러 Lock을 사용하다가 각 Thread가 서로가 가진 Lock을 기다리는 구조가 만들어지면 Deadlock이 발생할 수 있다.