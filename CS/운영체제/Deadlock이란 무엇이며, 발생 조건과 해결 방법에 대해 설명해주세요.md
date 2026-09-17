## 1. Deadlock이란?

**Deadlock(교착 상태)**은 여러 프로세스나 스레드가 서로가 가지고 있는 자원을 기다리면서, 누구도 더 이상 진행하지 못하는 상태다.

가장 단순한 예시는 다음과 같다.

```
Thread A
Lock 1 보유
↓
Lock 2 기다림

Thread B
Lock 2 보유
↓
Lock 1 기다림
```

이 경우:

```
Thread A
→ Thread B가 Lock 2를 놓기를 기다림

Thread B
→ Thread A가 Lock 1을 놓기를 기다림
```

하지만 둘 다 기다리고 있으므로 누구도 Lock을 반납하지 못한다.

```
Thread A → 대기
Thread B → 대기

↓
아무도 진행하지 못함
```

이 상태가 Deadlock이다.

---

# 2. 단순한 대기와 Deadlock의 차이

Thread가 Lock을 기다린다고 해서 항상 Deadlock인 것은 아니다.

예를 들어:

```
Thread A
Lock 획득
↓
작업
↓
Unlock

Thread B
그동안 대기
↓
A가 Unlock한 뒤 Lock 획득
```

이 경우 Thread B는 잠시 기다리지만 결국 실행할 수 있다.

반면 Deadlock은 다음과 같이 **기다림이 서로 연결되어 누구도 빠져나올 수 없는 상태**다.

```
A가 B를 기다림
B가 A를 기다림
```

즉 중요한 것은 단순히 기다리는 것이 아니라:

> **서로의 자원을 기다리면서 진행이 완전히 멈춘 상태인가**

이다.

---

# 3. Deadlock의 4가지 필요조건

Deadlock이 발생하려면 다음 네 가지 조건이 동시에 성립해야 한다.

```
Deadlock 4조건

1. Mutual Exclusion
2. Hold and Wait
3. No Preemption
4. Circular Wait
```

이 네 가지 조건을 흔히 **Coffman Conditions**라고도 한다.

---

# 4. Mutual Exclusion

**Mutual Exclusion(상호 배제)**은 하나의 자원을 한 번에 하나의 실행 주체만 사용할 수 있는 조건이다.

예:

```
Lock A

Thread 1 사용 중
↓
Thread 2 사용 불가
```

만약 여러 Thread가 하나의 자원을 동시에 자유롭게 사용할 수 있다면 해당 자원 때문에 서로 기다릴 이유가 줄어든다.

예를 들어 읽기 전용 데이터라면:

```
Thread A ─┐
Thread B ─┼→ 동시에 읽기 가능
Thread C ─┘
```

처럼 사용할 수 있다.

---

# 5. Hold and Wait

**Hold and Wait(점유 대기)**는 하나 이상의 자원을 이미 가지고 있는 상태에서 다른 자원을 추가로 기다리는 조건이다.

예:

```
Thread A

Lock 1 보유
+
Lock 2 기다림
```

즉:

```
Hold
→ 이미 자원을 가지고 있음

Wait
→ 다른 자원을 기다리고 있음
```

이다.

---

# 6. No Preemption

**No Preemption(비선점)**은 다른 프로세스나 Thread가 가지고 있는 자원을 강제로 빼앗을 수 없는 조건이다.

예를 들어 Thread A가 Mutex를 획득했다면:

```
Thread A
Lock 보유
```

다른 Thread가 단순히:

```
"그 Lock 내놔"
```

라고 해서 강제로 빼앗을 수 없다.

일반적으로 Lock을 획득한 Thread가 작업을 끝내고 직접 해제해야 한다.

```
Lock 획득
↓
Critical Section
↓
Unlock
```

---

# 7. Circular Wait

**Circular Wait(순환 대기)**는 여러 실행 주체가 원형으로 서로의 자원을 기다리는 조건이다.

가장 단순한 예:

```
Thread A
→ Thread B가 가진 자원 기다림

Thread B
→ Thread A가 가진 자원 기다림
```

조금 더 복잡하면:

```
Thread A
→ B가 가진 자원 기다림

Thread B
→ C가 가진 자원 기다림

Thread C
→ A가 가진 자원 기다림
```

그래프로 보면:

```
A → B
↑   ↓
└── C
```

이처럼 기다림이 Cycle을 이루는 것이 Circular Wait다.

---

# 8. 왜 네 조건이 모두 중요한가?

Deadlock의 4조건은:

```
하나만 만족하면 Deadlock 발생
```

이라는 의미가 아니다.

정확히는:

> **네 가지 조건이 모두 동시에 성립해야 Deadlock이 발생할 수 있다.**

따라서 반대로 생각하면:

> **네 조건 중 하나만 깨뜨려도 Deadlock을 예방할 수 있다.**

이 원리가 Deadlock Prevention의 핵심이다.

---

# 9. 예제로 4조건 확인

다음 상황을 보자.

```
Thread A
Lock 1 보유
Lock 2 기다림

Thread B
Lock 2 보유
Lock 1 기다림
```

## Mutual Exclusion

Lock 1과 Lock 2를 한 번에 하나의 Thread만 사용할 수 있다.

→ 성립

## Hold and Wait

A는 Lock 1을 가진 상태에서 Lock 2를 기다리고 있다.

B는 Lock 2를 가진 상태에서 Lock 1을 기다리고 있다.

→ 성립

## No Preemption

A가 가진 Lock 1과 B가 가진 Lock 2를 상대방이 강제로 빼앗을 수 없다.

→ 성립

## Circular Wait

```
A → B의 Lock
B → A의 Lock
```

순환 대기가 존재한다.

→ 성립

네 조건이 모두 성립하므로 Deadlock이 발생할 수 있다.

---

# 10. Deadlock 처리 방법의 전체 구조

Deadlock 처리 전략은 크게 네 가지로 나눌 수 있다.

```
Deadlock 처리

1. Prevention
2. Avoidance
3. Detection
4. Recovery
```

각각의 핵심은 다음과 같다.

```
Prevention
→ Deadlock 조건 자체를 깨뜨림

Avoidance
→ 위험한 자원 할당을 미리 피함

Detection
→ Deadlock 발생 여부를 검사

Recovery
→ 발생한 Deadlock을 해소
```

---

# 11. Prevention — 예방

Prevention은 Deadlock의 4가지 필요조건 중 하나 이상이 성립하지 못하도록 설계하는 방식이다.

```
Deadlock 4조건
↓
하나 제거
↓
Deadlock 불가능
```

---

# 12. Mutual Exclusion 제거

가능하다면 자원을 여러 실행 흐름이 동시에 사용할 수 있도록 만든다.

예:

```
읽기 전용 데이터
→ 여러 Thread가 동시에 접근
```

하지만 Mutex나 프린터처럼 본질적으로 하나의 실행 주체만 사용할 수 있는 자원은 Mutual Exclusion을 제거하기 어렵다.

따라서 모든 자원에 적용 가능한 방법은 아니다.

---

# 13. Hold and Wait 제거

한 Thread가 자원을 일부만 가진 채 다른 자원을 기다리지 못하도록 할 수 있다.

예를 들어 필요한 자원을 한 번에 모두 요청하게 한다.

기존:

```
Lock A 획득
↓
Lock B 요청
```

변경:

```
Lock A와 Lock B를
둘 다 얻을 수 있을 때만 획득
```

그러면:

```
Lock A는 가지고 있고
Lock B는 기다리는 상태
```

가 발생하지 않는다.

단점:

- 필요한 자원을 미리 알아야 함
    
- 사용하지 않는 자원까지 오래 잡아둘 수 있음
    
- 자원 이용률이 낮아질 수 있음
    

---

# 14. No Preemption 제거

추가로 필요한 자원을 얻지 못하면 현재 가지고 있는 자원까지 반납하도록 할 수 있다.

예:

```
Thread A

Lock A 보유
↓
Lock B 요청
↓
획득 실패
↓
Lock A도 반납
↓
나중에 다시 시도
```

이렇게 하면 자원을 가진 채 다른 자원을 기다리는 구조를 완화할 수 있다.

하지만 모든 자원을 중간에 강제로 회수하거나 다시 시작할 수 있는 것은 아니다.

---

# 15. Circular Wait 제거

대표적인 방법은 **Lock Ordering**이다.

모든 Lock에 획득 순서를 정한다.

예:

```
Lock A < Lock B < Lock C
```

모든 Thread가 반드시:

```
A → B → C
```

순서로 Lock을 획득하도록 한다.

그러면 다음과 같은 반대 순서가 만들어질 수 없다.

```
Thread 1
A → B

Thread 2
B → A
```

따라서 Circular Wait를 방지할 수 있다.

---

# 16. 왜 Lock 순서를 통일하면 Deadlock을 막을 수 있는가?

기존 Deadlock 상황:

```
Thread A
Lock A 보유
↓
Lock B 기다림

Thread B
Lock B 보유
↓
Lock A 기다림
```

여기서 모든 Thread가 반드시:

```
Lock A → Lock B
```

순서로만 획득하도록 한다고 하자.

Thread A가 Lock A를 가지고 있다면 Thread B는 Lock B를 먼저 획득할 수 없다.

```
Thread A
A 획득
↓
B 획득

Thread B
A부터 기다림
```

즉:

```
B를 가진 채 A를 기다리는 상태
```

가 만들어지지 않으므로 순환 구조가 생기지 않는다.

---

# 17. Avoidance — 회피

Avoidance는 Prevention과 다르다.

Prevention은 Deadlock의 조건 자체를 제거한다.

Avoidance는 Deadlock 조건 자체는 허용하지만:

> **현재 요청한 자원을 지금 할당해도 시스템이 안전한 상태를 유지할 수 있는지 확인한 뒤 자원을 할당한다.**

흐름:

```
Process가 Resource 요청
↓
지금 할당해도 안전한가?
↓
Yes
→ 할당

No
→ 대기
```

대표적인 알고리즘이 **Banker's Algorithm**이다.

---

# 18. Banker's Algorithm

Banker's Algorithm은 은행의 대출 상황에서 이름이 유래했다.

은행이 가진 돈이 한정되어 있을 때 모든 고객에게 무조건 돈을 빌려주면, 나중에 누구도 필요한 돈을 추가로 받지 못하는 상태가 될 수 있다.

따라서 은행은:

> **지금 이 요청을 승인하더라도 모든 고객이 언젠가는 필요한 자원을 받고 정상 종료할 수 있는가?**

를 판단한다.

운영체제에서도 비슷하게 다음 정보를 사용한다.

```
현재 사용 가능한 Resource
각 Process가 가지고 있는 Resource
각 Process가 앞으로 최대 얼마나 더 필요할지
```

이를 바탕으로 시스템이 Safe State인지 검사한다.

---

# 19. Safe State

**Safe State**는 모든 Process가 언젠가는 필요한 자원을 얻고 정상적으로 종료할 수 있는 실행 순서가 하나 이상 존재하는 상태다.

예:

```
P1 완료
↓
P1 Resource 반환
↓
P2 완료
↓
P2 Resource 반환
↓
P3 완료
```

이런 순서가 하나라도 존재한다면 Safe State다.

중요한 구분:

```
Safe State
→ Deadlock 없음

Unsafe State
→ Deadlock이 반드시 발생한 상태는 아님
→ 앞으로 Deadlock이 발생할 가능성이 있는 상태
```

---

# 20. Detection — 탐지

Detection은 Deadlock이 발생할 수 있도록 자원을 할당한 뒤:

> **현재 시스템에 실제 Deadlock이 발생했는지 검사하는 방식**

이다.

```
Resource 할당
↓
시스템 실행
↓
Deadlock 여부 검사
↓
발견되면 Recovery
```

탐지 방법은 자원의 인스턴스 개수에 따라 다르게 볼 수 있다.

```
자원 종류마다 인스턴스 1개
→ Wait-for Graph

자원 종류마다 인스턴스 여러 개
→ Available / Allocation / Request 기반 Detection
```

---

# 21. Resource Allocation Graph

먼저 **Resource Allocation Graph**를 이해하면 Wait-for Graph를 이해하기 쉽다.

Process:

```
P1
P2
```

Resource:

```
R1
R2
```

화살표의 의미:

```
Process → Resource
→ Process가 해당 Resource를 요청하고 기다림

Resource → Process
→ 해당 Resource가 Process에게 할당되어 있음
```

예를 들어:

```
P1이 R1을 보유
P1은 R2를 기다림

P2가 R2를 보유
P2는 R1을 기다림
```

이라면:

```
R1 → P1 → R2
R2 → P2 → R1
```

그래프로 보면 Cycle이 생긴다.

```
P1 → R2
↑      ↓
R1 ← P2
```

자원 인스턴스가 하나뿐인 경우 이러한 Cycle은 Deadlock과 직접 연결된다.

---

# 22. Wait-for Graph란?

**Wait-for Graph**는:

> **어떤 Process가 어떤 Process를 기다리고 있는지를 직접 표현한 그래프**

다.

Resource Allocation Graph에서 Resource Node를 제거해서 Process 간 대기 관계만 표현한다고 이해하면 쉽다.

예:

```
Resource Allocation Graph

P1 → R2 → P2
P2 → R1 → P1
```

Resource Node를 제거하면:

```
Wait-for Graph

P1 → P2
P2 → P1
```

이 된다.

---

# 23. Wait-for Graph의 화살표 의미

```
P1 → P2
```

는:

> **P1이 P2가 보유한 자원이 해제되기를 기다리고 있다**

는 의미다.

예:

```
P1 → P2
P2 → P3
P3 → P1
```

그러면:

```
P1은 P2를 기다림
P2는 P3를 기다림
P3는 P1을 기다림
```

즉:

```
P1 → P2 → P3 → P1
```

이라는 Cycle이 생긴다.

자원 인스턴스가 하나뿐인 경우 이런 Cycle은 Deadlock을 의미한다.

---

# 24. 자원 인스턴스가 하나일 때 Detection

자원 종류마다 인스턴스가 하나뿐이라면 Wait-for Graph에서 Cycle을 찾으면 된다.

```
Wait-for Graph

P1 → P2
P2 → P3
P3 → P1
```

Cycle:

```
P1 → P2 → P3 → P1
```

결과:

```
Cycle 있음
→ Deadlock

Cycle 없음
→ Deadlock 없음
```

단일 인스턴스 환경에서는 이 방식이 직관적이다.

---

# 25. 자원 인스턴스가 여러 개면 왜 Cycle만 봐서는 안 되는가?

같은 종류의 자원이 여러 개 존재할 수 있다.

예:

```
DB Connection = 3개
```

이런 경우 현재 어떤 Process가 자원을 기다리고 있더라도 다른 Process가 하나를 반환하면 대기가 풀릴 수 있다.

예:

```
Connection 총 3개

P1 → 1개 보유
P2 → 1개 보유
P3 → 1개 보유
```

현재:

```
Available = 0
```

이라고 하자.

P1이 Connection을 하나 더 필요로 한다.

```
P1
Allocation = 1
Request = 1
```

지금 당장은 실행할 수 없다.

하지만 P2가 추가 자원 없이 종료할 수 있다면:

```
P2 완료
↓
Connection 1개 반환
↓
Available = 1
↓
P1이 자원 획득 가능
↓
P1 완료
```

할 수 있다.

따라서 Multiple Instance 환경에서는 단순 Cycle 여부만으로 Deadlock을 판단하기 어렵다.

---

# 26. Multiple Instance Detection에 사용하는 정보

대표적으로 다음 세 정보를 사용한다.

## Available

현재 사용 가능한 각 자원의 수.

```
Available
→ 현재 누구에게도 할당되지 않은 자원
```

## Allocation

각 Process가 현재 보유하고 있는 자원의 수.

```
Allocation[P1]
→ P1이 현재 가진 자원
```

## Request

각 Process가 현재 추가로 요청하고 있는 자원의 수.

```
Request[P1]
→ P1이 현재 추가로 필요한 자원
```

---

# 27. Multiple Instance Detection 예제

자원 R이 총 3개 있다고 하자.

|Process|Allocation|Request|
|---|---|---|
|P1|1|1|
|P2|1|0|
|P3|1|1|

현재 세 자원이 모두 할당되어 있으므로:

```
Available = 0
```

---

# 28. 완료 가능한 Process를 찾는다

P1:

```
Request = 1
Available = 0

→ 지금 완료 불가능
```

P2:

```
Request = 0
Available = 0

→ 추가 자원 없이 완료 가능
```

따라서 P2가 완료한다고 가정할 수 있다.

P2가 끝나면 자신이 가지고 있던 자원 1개를 반환한다.

```
Available
= 기존 0
+ P2 Allocation 1
= 1
```

---

# 29. 다시 검사한다

이제:

```
Available = 1
```

P1:

```
Request = 1
≤ Available = 1

→ 완료 가능
```

P1이 완료하면 P1이 보유한 자원을 반환한다.

그 결과 Available이 더 증가하고 P3도 완료할 수 있게 된다.

```
P2
↓
P1
↓
P3
```

모든 Process가 완료 가능한 순서가 존재하므로 현재 Deadlock은 아니다.

---

# 30. Deadlock인 Multiple Instance 예제

자원 R이 총 2개라고 하자.

|Process|Allocation|Request|
|---|---|---|
|P1|1|1|
|P2|1|1|

현재:

```
Available = 0
```

P1:

```
Request 1 > Available 0
→ 완료 불가능
```

P2:

```
Request 1 > Available 0
→ 완료 불가능
```

누구도 먼저 끝날 수 없다.

```
P1
→ 자원 하나 더 필요

P2
→ 자원 하나 더 필요
```

하지만 사용 가능한 자원은 없다.

```
아무 Process도 완료 불가
↓
Resource도 반환되지 않음
↓
Deadlock
```

---

# 31. Detection Algorithm의 핵심 원리

핵심 질문은 하나다.

> **현재 Available Resource만으로 완료할 수 있는 Process가 있는가?**

있다면:

```
완료 가능한 Process 선택
↓
완료했다고 가정
↓
그 Process의 Allocation 반환
↓
Available 증가
↓
다시 검사
```

를 반복한다.

```
완료 가능한 Process 찾기
        ↓
Resource 반환했다고 가정
        ↓
Available 증가
        ↓
다시 완료 가능한 Process 찾기
        ↓
반복
```

마지막에:

```
모든 Process 완료 가능
→ Deadlock 아님
```

반대로:

```
아직 끝나지 않은 Process가 있는데
어느 Process도 현재 Available로
Request를 만족할 수 없음

→ Deadlock 상태의 Process가 남음
```

으로 판단할 수 있다.

---

# 32. Wait-for Graph와 자원 상태 기반 Detection 비교

|상황|탐지 방법|
|---|---|
|자원 종류마다 인스턴스가 하나|Wait-for Graph의 Cycle 검사|
|자원 종류마다 인스턴스가 여러 개|Available / Allocation / Request 기반 Detection|

정리:

```
Single Instance

Wait-for Graph
↓
Cycle 검사
↓
Cycle = Deadlock
```

```
Multiple Instances

Available
Allocation
Request
↓
완료 가능한 Process 찾기
↓
Resource 반환했다고 가정
↓
반복
↓
끝까지 완료할 수 없는 Process 확인
```

---

# 33. Detection과 Banker's Algorithm의 차이

둘은 계산 방식이 비슷해 보일 수 있다.

하지만 목적과 시점이 다르다.

## Banker's Algorithm

Deadlock **Avoidance**에 사용한다.

```
자원을 할당하기 전

"이 자원을 지금 줘도
Safe State를 유지할 수 있는가?"
```

를 검사한다.

즉 Deadlock 위험 상태에 들어가는 것을 미리 피한다.

## Detection Algorithm

이미 자원이 할당된 상태에서:

```
"현재 실제로 Deadlock에 빠진 Process가 있는가?"
```

를 검사한다.

핵심:

```
Banker's Algorithm
→ 앞으로 안전한지 검사
→ Avoidance

Detection Algorithm
→ 현재 Deadlock인지 검사
→ Detection
```

---

# 34. Recovery — 복구

Deadlock을 탐지했다면 이를 해소해야 한다.

대표적인 방법은 다음과 같다.

---

# 35. Process / Thread 종료

Deadlock에 포함된 Process나 Thread 중 하나를 강제로 종료한다.

예:

```
A ↔ B Deadlock

B 종료
↓
B가 가진 Resource 해제
↓
A 진행 가능
```

장점:

- 확실하게 Deadlock을 깰 수 있음
    

단점:

- 종료된 작업의 진행 내용이 사라질 수 있음
    
- 데이터 일관성 문제가 생길 수 있음
    

---

# 36. Resource 회수

일부 Resource를 강제로 회수해서 다른 Process에게 줄 수도 있다.

```
P2의 Resource 회수
↓
P1에게 할당
↓
P1 완료
↓
Deadlock 해소
```

하지만 모든 Resource를 안전하게 강제 회수할 수 있는 것은 아니다.

---

# 37. Rollback

Process를 이전의 안전한 상태로 되돌린 후 다시 실행할 수도 있다.

```
Process
현재 상태
↓
Rollback
↓
이전 Checkpoint
↓
다시 실행
```

DB Transaction 등의 개념과 연결해서 이해할 수 있다.

---

# 38. Deadlock과 Starvation의 차이

## Deadlock

```
A가 B 기다림
B가 A 기다림

→ 모두 진행 불가
```

## Starvation

```
A는 실행 가능

하지만
B, C, D가 계속 먼저 실행

→ A만 계속 기회를 받지 못함
```

즉:

```
Deadlock
→ 서로 기다려서 관련된 실행 주체들이 진행하지 못함

Starvation
→ 다른 작업들은 계속 진행하지만 특정 작업만 계속 밀림
```

---

# 39. Deadlock과 Livelock의 차이

## Deadlock

실행 주체들이 서로 기다리며 멈춰 있다.

```
A 대기
B 대기
```

## Livelock

상태는 계속 바뀌지만 실질적인 작업 진행은 없다.

예를 들어 좁은 복도에서 두 사람이 서로 비키려고 계속 같은 방향으로 움직인다고 생각할 수 있다.

```
A 오른쪽
B 오른쪽

↓
다시 막힘

A 왼쪽
B 왼쪽

↓
다시 막힘
```

즉:

```
Deadlock
→ 아무도 움직이지 않음

Livelock
→ 움직이지만 진전이 없음

Starvation
→ 일부 실행 주체만 계속 기회를 얻지 못함
```

---

# 40. Java 코드에서의 Deadlock 예시

```
Object lockA = new Object();
Object lockB = new Object();

Thread t1 = new Thread(() -> {
    synchronized (lockA) {
        synchronized (lockB) {
            // 작업
        }
    }
});

Thread t2 = new Thread(() -> {
    synchronized (lockB) {
        synchronized (lockA) {
            // 작업
        }
    }
});
```

Lock 획득 순서:

```
Thread 1
A → B

Thread 2
B → A
```

실행이 다음과 같이 되면:

```
T1: A 획득
T2: B 획득

T1: B 기다림
T2: A 기다림

→ Deadlock
```

이 발생할 수 있다.

---

# 41. Lock Ordering으로 해결

두 Thread 모두 같은 순서로 Lock을 획득하게 만들 수 있다.

```
Thread t1 = new Thread(() -> {
    synchronized (lockA) {
        synchronized (lockB) {
            // 작업
        }
    }
});

Thread t2 = new Thread(() -> {
    synchronized (lockA) {
        synchronized (lockB) {
            // 작업
        }
    }
});
```

이제 둘 다:

```
A → B
```

순서로만 획득한다.

따라서:

```
B를 가진 채
A를 기다리는 Thread
```

가 만들어질 수 없으므로 Circular Wait를 제거할 수 있다.

---

# 42. 지금까지 배운 OS 흐름과 연결

```
Process
↓
Thread
↓
공유 자원 사용
↓
Race Condition 가능
↓
Critical Section
↓
Synchronization
↓
Mutex / Lock
↓
여러 Lock 사용
↓
Lock 획득 순서 꼬임
↓
Circular Wait
↓
Deadlock
```

즉 Deadlock은 Synchronization과 별개의 개념이 아니라:

> **공유 자원을 안전하게 보호하기 위해 Lock을 사용하면서 새롭게 발생할 수 있는 문제**

라고 이해하면 된다.

---

# 43. 핵심 개념 정리

## Deadlock

**여러 실행 주체가 서로가 가지고 있는 자원을 기다리면서 누구도 진행하지 못하는 상태**

## Mutual Exclusion

**하나의 자원을 한 번에 하나의 실행 주체만 사용할 수 있는 조건**

## Hold and Wait

**이미 자원을 보유한 상태에서 다른 자원을 기다리는 조건**

## No Preemption

**다른 실행 주체가 가진 자원을 강제로 빼앗을 수 없는 조건**

## Circular Wait

**실행 주체들이 원형으로 서로의 자원을 기다리는 조건**

## Prevention

**Deadlock의 필요조건 중 하나 이상이 성립하지 못하도록 설계하는 방법**

## Avoidance

**자원을 할당하기 전에 시스템이 Safe State를 유지하는지 검사하여 위험한 할당을 피하는 방법**

## Detection

**이미 자원이 할당된 상태에서 실제 Deadlock 발생 여부를 검사하는 방법**

## Recovery

**발생한 Deadlock을 Process 종료, Resource 회수, Rollback 등으로 해소하는 방법**

## Wait-for Graph

**어떤 Process가 어떤 Process를 기다리고 있는지 직접 표현한 그래프**

---

# 44. 면접 답변

> Deadlock은 여러 프로세스나 스레드가 서로가 가지고 있는 자원을 기다리면서 어느 쪽도 더 이상 진행할 수 없는 상태입니다.
> 
> Deadlock이 발생하기 위해서는 네 가지 필요조건이 동시에 성립해야 합니다. 하나의 자원을 한 번에 하나의 실행 주체만 사용할 수 있는 Mutual Exclusion, 이미 자원을 가진 상태에서 다른 자원을 기다리는 Hold and Wait, 다른 실행 주체가 가진 자원을 강제로 빼앗을 수 없는 No Preemption, 그리고 서로의 자원을 순환해서 기다리는 Circular Wait입니다.
> 
> Deadlock을 처리하는 방법으로는 네 가지 조건 중 하나를 제거하는 Prevention, 자원을 할당하기 전에 시스템이 안전한 상태인지 확인하는 Avoidance, 발생 여부를 확인하는 Detection, 발생 후 Process 종료나 자원 회수 등으로 해소하는 Recovery가 있습니다.
> 
> Detection의 경우 자원 종류마다 인스턴스가 하나라면 Wait-for Graph에서 Cycle이 존재하는지 검사할 수 있습니다. 반면 같은 종류의 자원이 여러 개라면 단순한 Cycle 검사만으로는 부족하므로 Available, Allocation, Request 정보를 이용해 현재 자원으로 완료 가능한 Process를 반복적으로 제거하면서 끝까지 완료할 수 없는 Process가 남는지 검사합니다.
> 
> 실제 Lock을 사용할 때는 모든 Thread가 Lock을 동일한 순서로 획득하도록 설계하여 Circular Wait를 제거하는 방식으로 Deadlock을 예방할 수 있습니다.

---

# 45. 전체 구조 한 장 정리

```
공유 자원
↓
Synchronization
↓
Lock 사용
↓
여러 Lock 필요
↓
서로의 Lock 기다림
↓
Deadlock

Deadlock 4조건
├─ Mutual Exclusion
├─ Hold and Wait
├─ No Preemption
└─ Circular Wait

대응 방법
├─ Prevention
│   → 조건 하나 제거
│
├─ Avoidance
│   → Safe State 유지
│   → Banker's Algorithm
│
├─ Detection
│   ├─ Single Instance
│   │   → Wait-for Graph
│   │   → Cycle 검사
│   │
│   └─ Multiple Instances
│       → Available
│       → Allocation
│       → Request
│       → 완료 가능 Process 반복 제거
│
└─ Recovery
    ├─ Process 종료
    ├─ Resource 회수
    └─ Rollback
```

---

# 46. 복습 질문

### Q1. Deadlock이란 무엇인가?

여러 Process나 Thread가 서로가 가지고 있는 자원을 기다리면서 누구도 더 이상 진행하지 못하는 상태다.

### Q2. 단순한 Lock 대기와 Deadlock의 차이는?

단순한 대기는 Lock을 가진 Thread가 작업을 끝내고 자원을 반납하면 해소된다. Deadlock은 서로가 상대방의 자원을 기다려 누구도 먼저 자원을 반납할 수 없는 상태다.

### Q3. Deadlock의 4가지 필요조건은?

Mutual Exclusion, Hold and Wait, No Preemption, Circular Wait이다.

### Q4. 네 가지 조건이 모두 필요한 이유는?

Deadlock은 네 조건이 동시에 성립해야 발생할 수 있기 때문이다. 따라서 하나만 깨뜨려도 Deadlock을 예방할 수 있다.

### Q5. Lock Ordering이 Deadlock을 막는 원리는?

모든 Thread가 같은 순서로 Lock을 획득하도록 하면 서로 반대 방향으로 Lock을 잡고 기다리는 구조가 만들어지지 않아 Circular Wait를 제거할 수 있다.

### Q6. Prevention과 Avoidance의 차이는?

Prevention은 Deadlock 필요조건 자체를 하나 이상 제거하고, Avoidance는 조건은 허용하지만 자원 할당 전에 시스템이 Safe State를 유지하는지 검사한다.

### Q7. Safe State란?

모든 Process가 필요한 자원을 얻고 정상적으로 종료할 수 있는 실행 순서가 하나 이상 존재하는 상태다.

### Q8. Unsafe State는 이미 Deadlock이라는 뜻인가?

아니다. Unsafe State는 앞으로 Deadlock이 발생할 가능성이 있는 상태이지, 반드시 현재 Deadlock이라는 의미는 아니다.

### Q9. Wait-for Graph란?

Process 간 대기 관계를 직접 표현한 그래프다. `P1 → P2`는 P1이 P2가 보유한 자원이 해제되기를 기다리고 있다는 뜻이다.

### Q10. 자원 인스턴스가 하나일 때 Deadlock은 어떻게 탐지하는가?

Wait-for Graph를 만들고 Cycle이 존재하는지 검사한다. Single Instance 환경에서는 Cycle이 존재하면 Deadlock이다.

### Q11. 자원 인스턴스가 여러 개일 때 왜 Cycle만 보면 안 되는가?

동일한 종류의 자원이 여러 개라면 다른 Process가 자원을 하나 반납하면서 대기가 풀릴 수 있기 때문에 단순 순환 관계만으로 실제 Deadlock을 확정하기 어렵다.

### Q12. Multiple Instance에서는 Deadlock을 어떻게 탐지하는가?

Available, Allocation, Request를 보고 현재 자원으로 완료 가능한 Process를 찾은 뒤 해당 Process가 자원을 반환했다고 가정한다. 이를 반복했는데도 완료할 수 없는 Process가 남으면 Deadlock 상태로 볼 수 있다.

### Q13. Banker's Algorithm과 Detection Algorithm의 차이는?

Banker's Algorithm은 자원을 할당하기 전에 앞으로도 안전한 상태인지 검사하는 Avoidance 방식이고, Detection Algorithm은 이미 자원이 할당된 상태에서 현재 실제 Deadlock이 발생했는지 검사한다.

### Q14. Deadlock과 Starvation의 차이는?

Deadlock에서는 서로 자원을 기다려 관련 실행 주체들이 모두 진행하지 못한다. Starvation에서는 다른 작업들은 계속 실행되지만 특정 작업만 계속 기회를 얻지 못한다.

### Q15. Deadlock과 Livelock의 차이는?

Deadlock은 실행 주체들이 기다리며 멈춘 상태이고, Livelock은 상태는 계속 바뀌지만 실질적인 작업 진행은 없는 상태다