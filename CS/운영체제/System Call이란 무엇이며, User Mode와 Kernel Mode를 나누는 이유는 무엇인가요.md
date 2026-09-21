
## 1. 핵심 정의

**System Call**은 User Mode에서 실행 중인 프로그램이 Kernel의 기능을 요청하기 위한 인터페이스다.

운영체제는 일반 프로그램이 시스템 전체에 영향을 줄 수 있는 위험한 작업을 직접 수행하지 못하도록 CPU 권한을 나눈다.

```text
User Mode
→ 제한된 권한

Kernel Mode
→ 높은 권한
```

전체 흐름은 다음과 같다.

```text
Application
↓
User Mode
↓
System Call
↓
Kernel Mode
↓
Kernel이 요청 처리
↓
결과 반환
↓
User Mode
↓
Application 계속 실행
```

---

# 2. 왜 User Mode와 Kernel Mode를 나누는가?

만약 일반 프로그램이 CPU와 Hardware의 모든 기능을 자유롭게 사용할 수 있다면 다음과 같은 문제가 생길 수 있다.

```text
다른 Process의 Memory 수정
Page Table 변경
Disk 전체 변경
Interrupt 비활성화
Device 직접 제어
Kernel Memory 수정
```

프로그램 하나의 버그나 악성 코드가 시스템 전체를 망가뜨릴 수 있다.

그래서 운영체제와 CPU는 권한을 나눈다.

```text
User Mode
→ 일반 Application 실행
→ 제한된 권한

Kernel Mode
→ Kernel 실행
→ 시스템 자원 관리 권한
```

핵심 목적은 **안정성, 보안, Process Isolation, Memory Protection, Hardware 보호**다.

---

# 3. User Mode

일반적인 Application은 User Mode에서 실행된다.

예:

```text
Chrome
IntelliJ
게임
Java Program
Python Program
```

User Mode에서는 일반적인 연산은 가능하지만 시스템 전체에 영향을 주는 중요한 작업은 직접 수행할 수 없다.

예:

```text
Page Table 변경
Interrupt 제어
Device 직접 제어
중요한 CPU Control Register 변경
```

---

# 4. Kernel Mode

Kernel은 Kernel Mode에서 실행된다.

Kernel Mode에서는 높은 권한을 사용하여 다음과 같은 작업을 수행한다.

```text
Process Scheduling
Memory 관리
Page Table 관리
File System
Network Stack
Device Driver
Interrupt 처리
Page Fault 처리
```

즉 앞에서 배운 Scheduler, Page Fault Handler, File System, Device Driver 등은 대부분 Kernel 영역에서 동작한다.

---

# 5. Privileged Instruction

**Privileged Instruction**은 Kernel Mode에서만 실행할 수 있도록 제한된 CPU 명령어다.

예를 들면 개념적으로:

```text
Page Table 관련 상태 변경
Interrupt 제어
특정 Device 제어
Control Register 변경
```

User Mode 프로그램이 이런 명령을 직접 실행하려 하면 CPU가 허용하지 않고 Exception을 발생시킨다.

즉 User Mode / Kernel Mode 분리는 단순한 소프트웨어 약속이 아니라 **CPU Hardware 차원에서 강제되는 권한 분리**다.

---

# 6. 일반 프로그램도 Kernel 기능이 필요한 이유

Application도 파일을 읽고, Network를 사용하고, 새로운 Process를 만들고, Memory를 요청해야 한다.

하지만 User Mode에서는 Hardware나 Kernel 내부 구조를 직접 제어할 수 없다.

그래서 다음 구조를 사용한다.

```text
Application
"파일을 읽고 싶다"
↓
Kernel
"내가 대신 처리한다"
```

그 요청 통로가 **System Call**이다.

---

# 7. System Call이란?

System Call은:

> **User Mode 프로그램이 Kernel의 서비스를 요청하기 위한 공식적인 인터페이스**

다.

대표적인 작업:

```text
파일 열기
파일 읽기
파일 쓰기
프로세스 생성
메모리 관련 작업
네트워크 통신
```

Unix/Linux 계열의 대표적인 예:

```text
open()
read()
write()
close()
fork()
exec()
mmap()
socket()
```

---

# 8. 일반 함수 호출과 System Call의 차이

일반 함수 호출은 같은 User Mode 내부에서 실행될 수 있다.

```text
foo()
↓
bar()
↓
baz()
```

반면 System Call은:

```text
User Mode
↓
Kernel Mode
```

로 CPU 권한 수준이 바뀌어야 한다.

따라서 일반 함수 호출과 달리 CPU가 제공하는 특별한 Kernel 진입 메커니즘을 사용한다.

---

# 9. System Call의 실제 흐름

예:

```c
read(fd, buffer, 100);
```

개념적인 흐름:

```text
1. Application이 read 요청
↓
2. System Call Number와 Argument 준비
↓
3. CPU의 System Call 진입 명령 실행
↓
4. User Mode → Kernel Mode
↓
5. Kernel의 System Call Handler 실행
↓
6. Kernel이 read 작업 수행
↓
7. 결과 반환
↓
8. Kernel Mode → User Mode
↓
9. Application 실행 재개
```

---

# 10. System Call Number

Kernel에는 여러 System Call이 있으므로 어떤 요청인지 구분해야 한다.

```text
System Call Number
↓
Kernel이 어떤 기능을 호출할지 결정
```

정확한 번호와 전달 규칙은 OS와 CPU Architecture에 따라 달라진다.

---

# 11. System Call Argument는 어떻게 전달하는가?

예:

```c
read(fd, buffer, 100);
```

Kernel은 다음 정보가 필요하다.

```text
fd
buffer 주소
읽을 크기
```

이런 값은 보통 Register 등을 통해 전달될 수 있다.

```text
Register A → System Call Number
Register B → fd
Register C → buffer address
Register D → size
```

---

# 12. Argument가 많으면 Register가 부족하지 않는가?

맞다. Register 개수에는 한계가 있다.

하지만 System Call Argument가 Register 개수에 완전히 제한되는 것은 아니다.

대표적인 방법:

```text
Memory에 Argument 구조체 저장
↓
그 구조체의 주소를 Register로 전달
```

예:

```text
User Memory

┌─────────────────┐
│ argument 1      │
│ argument 2      │
│ argument 3      │
│ argument 4      │
│ ...             │
└─────────────────┘
        ↑
        │
   Pointer를 Register로 전달
```

큰 데이터 역시 Register에 전부 넣는 것이 아니다.

예를 들어 1MB 데이터를 읽는 경우에는 1MB 데이터를 Register에 넣는 것이 아니라:

```text
buffer 주소
+
읽을 크기
```

를 전달한다.

핵심:

> **작은 값은 Register로 전달하고, 큰 데이터나 많은 정보는 Memory에 두고 Pointer를 전달할 수 있다.**

---

# 13. System Call은 항상 Context Switching을 발생시키는가?

아니다.

System Call이 발생하면 기본적으로:

```text
User Mode
↓
Kernel Mode
```

로 바뀌는 **Mode Switch**가 발생한다.

하지만 반드시 실행 Process나 Thread가 바뀌는 것은 아니다.

```text
Process A
User Mode
↓
System Call
↓
Process A
Kernel Mode
↓
처리
↓
Process A
User Mode
```

이 경우 Mode Switch는 발생하지만 Context Switching은 없다.

---

# 14. Mode Switch

Mode Switch는 CPU의 실행 권한 수준이 바뀌는 것이다.

```text
User Mode
↔
Kernel Mode
```

현재 실행 중인 Process는 그대로일 수 있다.

---

# 15. Context Switching

Context Switching은 실행 중인 Process 또는 Thread가 바뀌는 것이다.

```text
Process A
↓
Process B
```

또는:

```text
Thread A
↓
Thread B
```

따라서:

```text
Mode Switch
≠
Context Switching
```

이다.

---

# 16. System Call 때문에 Context Switching까지 발생하는 경우

Process A가 `read()`를 호출했는데 Disk I/O가 필요하다고 하자.

```text
Process A
User Mode
↓
System Call
↓
Kernel Mode
↓
Disk I/O 필요
↓
A → Waiting
```

A는 당장 실행할 수 없으므로 Scheduler가 다른 Process를 선택한다.

```text
Scheduler
↓
Process B 선택
↓
Context Switching
↓
Process B 실행
```

즉:

> **System Call 자체는 Mode Switch이고, 그 결과 현재 Process가 Block되면 Context Switching까지 발생할 수 있다.**

---

# 17. Kernel Mode에서는 다른 User Process를 실행할 수 없는가?

Single Core에서 특정 순간만 보면 맞다.

```text
Single Core

Process A의 Kernel Code 실행 중
↓
동시에 Process B의 User Code 실행 불가
```

하지만 이것은 Kernel Mode이기 때문에 특별히 B를 막는 것이 아니라 **Core 하나가 한 순간에 하나의 실행 흐름만 실행하기 때문**이다.

---

# 18. Kernel 작업이 오래 걸리면 다른 Process는 어떻게 실행하는가?

Process A가 I/O를 기다려야 한다면:

```text
A
Running
↓
Kernel Mode
↓
I/O Wait
↓
A → Waiting
```

Scheduler가 다른 Process B를 선택할 수 있다.

```text
Kernel Mode
↓
Scheduler
↓
Process B 선택
↓
Context Switching
```

B가 User Mode에서 실행을 이어가야 한다면:

```text
Process B
↓
User Mode
```

로 복귀하여 실행된다.

---

# 19. Context Switching 시 CPU Mode도 항상 User Mode로 바뀌는가?

항상 그렇지는 않다.

Context Switching은 실행 주체를 바꾸는 것이고, CPU Mode는 새로 선택된 실행 흐름이 어디서 이어져야 하는지에 따라 달라질 수 있다.

```text
A Kernel Mode
↓
Context Switch
↓
B User Mode
```

일 수도 있고,

```text
A Kernel Mode
↓
Context Switch
↓
B Kernel Mode
```

일 수도 있다.

핵심:

```text
Context Switch
≠ 무조건 Kernel → User
```

---

# 20. Multi-Core에서는?

User Mode / Kernel Mode는 시스템 전체의 단일 상태가 아니다.

각 CPU Core가 현재 어떤 권한 수준의 코드를 실행하고 있는지에 따라 다르다.

```text
Core 1 → Process A Kernel Mode
Core 2 → Process B User Mode
Core 3 → Process C User Mode
Core 4 → Process D Kernel Mode
```

즉 한 Core가 Kernel Mode라고 해서 시스템 전체가 Kernel Mode가 되는 것은 아니다.

---

# 21. System Call과 Interrupt의 차이

큰 그림:

```text
System Call
→ Software가 Kernel Service 요청

Interrupt
→ Hardware가 CPU에 Event 알림
```

더 정확하게는:

```text
System Call
→ Software가 CPU의 Kernel 진입 메커니즘을 이용해
   Kernel Service 요청

Interrupt
→ Hardware가 CPU에 처리할 Event가 있다고 알림
```

---

# 22. Interrupt

Interrupt는 Hardware가 CPU에 특정 Event가 발생했다고 알리는 메커니즘이다.

예:

```text
Keyboard 입력
Network Packet 도착
Disk I/O 완료
Timer Interrupt
```

흐름:

```text
Hardware
↓
Interrupt
↓
CPU
↓
Kernel Interrupt Handler
```

---

# 23. Interrupt가 오면 CPU는 하던 일을 멈추는가?

개념적으로는 현재 실행 상태를 보존한 뒤 Interrupt Handler로 이동한다.

```text
Process A 실행
↓
Hardware Interrupt 발생
↓
현재 실행 상태 저장
↓
Kernel Interrupt Handler 실행
↓
처리 완료
↓
원래 실행으로 복귀 가능
```

처리 후에는 원래 Process로 돌아갈 수도 있고 Scheduler 판단에 따라 다른 Process를 실행할 수도 있다.

---

# 24. CPU가 명령어 중간에서 잘리는가?

일반적인 OS 학습 수준에서는:

> **CPU가 현재 명령어를 적절한 경계까지 처리한 뒤 Interrupt를 받아들인다.**

라고 이해하면 된다.

정확한 세부 동작은 CPU Architecture에 따라 다를 수 있다.

---

# 25. Interrupt가 오면 무조건 즉시 먼저 처리하는가?

아니다.

Interrupt에는 Priority와 Masking 개념이 있다.

```text
Interrupt A → 높은 Priority
Interrupt B → 낮은 Priority
```

또 특정 Interrupt를 잠시 받지 않도록 설정할 수도 있다.

```text
Interrupt Masking
```

따라서 Interrupt 발생이 곧바로 모든 작업을 무조건 중단한다는 의미는 아니다.

---

# 26. Interrupt Handler

Interrupt 종류마다 Kernel 안에 처리 코드가 있다.

```text
Keyboard Interrupt
↓
Keyboard Interrupt Handler

Network Interrupt
↓
Network Interrupt Handler

Timer Interrupt
↓
Timer Interrupt Handler
```

Interrupt Handler를 **ISR(Interrupt Service Routine)**이라고도 한다.

---

# 27. Exception

Exception은 현재 CPU가 실행 중인 명령 때문에 발생하는 사건이다.

예:

```text
Divide by Zero
Page Fault
잘못된 Memory Access
```

흐름:

```text
현재 Instruction 실행
↓
CPU가 문제 또는 특별한 상황 발견
↓
Exception
↓
Kernel Handler
```

앞에서 배운 Page Fault 역시 Exception의 한 종류다.

---

# 28. Interrupt와 Exception의 차이

```text
Interrupt
→ 현재 실행 중인 명령과 직접 관계없이
   외부 Hardware에서 발생
→ Asynchronous

Exception
→ 현재 실행 중인 Instruction 때문에 발생
→ Synchronous
```

---

# 29. Trap이란?

Trap은 교재와 CPU Architecture에 따라 용어가 조금 다르게 사용된다.

전통적인 OS 설명에서는 흔히:

> **프로그램이 특정 명령을 실행하면서 의도적으로 발생시키는 동기적인 Exception**

으로 설명한다.

```text
Application
↓
특정 Instruction 실행
↓
Trap / Exception 메커니즘
↓
Kernel 진입
```

---

# 30. Trap은 Interrupt 명령어인가?

Trap 자체를 하나의 특정 CPU 명령어라고 보면 안 된다.

Trap은 보통 **CPU가 처리하는 동기적 Exception의 종류 또는 메커니즘**을 가리킨다.

다만 Trap이나 Kernel 진입을 의도적으로 발생시키는 CPU Instruction은 존재할 수 있다.

예:

과거 x86 Linux:

```assembly
int 0x80
```

현대 x86:

```assembly
syscall
```

ARM:

```assembly
svc
```

따라서:

```text
Trap
→ 동기적 Exception을 설명하는 개념

int / syscall / svc
→ Kernel 진입을 발생시키는 실제 CPU Instruction의 예
```

라고 구분하는 것이 좋다.

---

# 31. System Call과 Trap은 왜 둘 다 나오는가?

중요한 구분:

```text
System Call
= 무엇을 요청하는가

Trap / syscall / svc 등의 메커니즘
= User Mode에서 Kernel Mode로 어떻게 들어가는가
```

프로그램이:

```text
System Call 호출
↓
Trap을 또 호출
```

하는 두 단계로 생각하면 안 된다.

정확히는:

```text
프로그램이 System Call 요청
↓
System Call을 구현하기 위해
CPU의 Kernel 진입 메커니즘 사용
↓
Kernel Mode
```

즉 **System Call은 목적, Trap이나 `syscall` Instruction은 수단**이다.

---

# 32. read()를 예로 본 System Call + Kernel 진입

```text
Application
↓
read()
↓
libc read() Wrapper
↓
System Call Number / Argument 준비
↓
syscall 등의 CPU Instruction
↓
User Mode → Kernel Mode
↓
Kernel의 read Handler
↓
File System
↓
필요하면 Device Driver
↓
Storage
```

프로그램 입장에서는 그냥 `read()`를 호출한다.

System Call을 호출한 뒤 별도로 Trap을 다시 호출하는 것이 아니다.

---

# 33. Library Function과 System Call의 차이

예:

```c
printf("hello");
```

`printf()`는 C Standard Library Function이다.

System Call 자체가 아니다.

내부적으로 실제 출력이 필요하면:

```text
printf()
↓
Library 내부 Buffer 처리
↓
write() Wrapper
↓
System Call
↓
Kernel
```

처럼 동작할 수 있다.

핵심:

```text
Library Function
→ User Space 함수

System Call
→ Kernel 기능 요청 인터페이스
```

---

# 34. read()도 함수 아닌가?

프로그래머가 호출하는 `read()`는 libc 같은 User-space Wrapper 함수일 수 있다.

```text
Application
↓
libc read()
↓
System Call Instruction
↓
Kernel read Handler
```

따라서 실무에서는 `read()`를 System Call이라고 부르기도 하지만 엄밀하게는:

```text
User-space Wrapper
↓
실제 Kernel System Call
```

구조일 수 있다.

---

# 35. System Call과 Interrupt가 함께 동작하는 예

Process A가 파일을 읽는 상황:

```text
Process A
User Mode
↓
read()
↓
System Call
↓
Kernel Mode
↓
Disk I/O 요청
↓
A → Waiting
↓
Scheduler
↓
Context Switching
↓
Process B
User Mode 실행
```

Disk 작업이 끝나면:

```text
Disk
↓
Interrupt 발생
↓
CPU
↓
Kernel Mode
↓
Disk Interrupt Handler
↓
A의 I/O 완료 처리
↓
A: Waiting → Ready
```

그 후 Scheduler가 A를 다시 선택하면:

```text
Process A
↓
User Mode
↓
read() 결과 받고 실행 계속
```

한다.

---

# 36. Kernel이 System Call Argument를 검사하는 이유

User Program이 넘긴 값을 Kernel이 무조건 신뢰하면 안 된다.

예를 들어 잘못된 Pointer나 권한 없는 Resource를 전달할 수 있다.

Kernel은 다음을 검사할 수 있다.

```text
User Pointer가 유효한가?
이 Process가 접근 가능한 Memory인가?
File Descriptor가 정상인가?
요청 권한이 있는가?
```

즉 System Call Interface는 단순한 함수 호출 경계가 아니라 **Security Boundary**이기도 하다.

---

# 37. User Mode / Kernel Mode와 Virtual Memory의 연결

Page Table에는 접근 권한 정보도 들어갈 수 있다.

```text
User 접근 가능
Kernel만 접근 가능
```

User Mode Process가 Kernel-only Page에 접근하면:

```text
User Mode
↓
Kernel-only Page 접근
↓
Protection Fault
↓
Kernel 개입
```

즉 다음 요소들이 함께 동작한다.

```text
CPU Privilege Level
+
Page Table Permission
+
System Call Interface
```

---

# 38. 핵심 개념 비교

| 개념 | 의미 |
|---|---|
| User Mode | 일반 Application이 실행되는 제한된 권한 상태 |
| Kernel Mode | Kernel이 시스템 자원을 관리하는 높은 권한 상태 |
| System Call | User Program이 Kernel Service를 요청하는 인터페이스 |
| Privileged Instruction | Kernel Mode에서만 실행 가능한 중요 CPU 명령 |
| Mode Switch | User Mode ↔ Kernel Mode 권한 전환 |
| Context Switching | 실행 Process/Thread가 다른 Process/Thread로 변경 |
| Interrupt | 외부 Hardware가 CPU에 Event를 알림 |
| Exception | 현재 Instruction 실행으로 인해 발생하는 동기적 사건 |
| Trap | 전통적으로 의도적인 동기적 Exception을 설명하는 용어 |
| ISR | Interrupt를 처리하는 Kernel의 Interrupt Service Routine |

---

# 39. Mode Switch와 Context Switching 비교

| 구분 | Mode Switch | Context Switching |
|---|---|---|
| 무엇이 바뀌나 | CPU 권한 수준 | 실행 Process / Thread |
| 예 | User → Kernel | Process A → Process B |
| System Call 시 | 일반적으로 발생 | 반드시 발생하지 않음 |
| 같은 Process 유지 가능? | 가능 | 불가능 |
| 발생 이유 | Kernel 기능 수행 | CPU 실행 대상 변경 |

---

# 40. System Call / Interrupt / Exception / Trap 비교

| 개념 | 발생 주체 | 동기성 | 예 |
|---|---|---|---|
| System Call | Software가 Kernel Service 요청 | 동기적 | read(), write() |
| Interrupt | 외부 Hardware Event | 비동기적 | Timer, Disk, Network |
| Exception | 현재 Instruction 실행 | 동기적 | Page Fault, Divide by Zero |
| Trap | 의도적 Exception으로 설명되는 경우 | 동기적 | Debug, 전통적 System Call 설명 |

주의:

> **Trap이라는 용어의 정확한 범위는 교재나 CPU Architecture에 따라 다를 수 있으므로, 특정 명령어 하나라고 외우지 않는 것이 좋다.**

---

# 41. 전체 구조 한 장 정리

```text
Application
   │
   │ User Mode
   ▼
System Call
   │
   ▼
CPU의 Kernel 진입 메커니즘
(syscall / svc / trap 계열 등)
   │
   ▼
=========================
       Kernel Mode
=========================
   │
   ├─ Process 관리
   ├─ Memory 관리
   ├─ File System
   ├─ Network
   └─ Device
   │
   ▼
작업 완료?
├─ Yes
│   ↓
│ User Mode 복귀
│
└─ No, I/O 대기
    ↓
 Process Waiting
    ↓
 Scheduler
    ↓
 Context Switching
    ↓
 다른 Process 실행
```

Kernel로 들어오는 대표적인 사건:

```text
System Call
→ Software가 의도적으로 Kernel Service 요청

Interrupt
→ Hardware가 비동기적으로 Event 알림

Exception
→ 현재 Instruction 실행 때문에 발생

Trap
→ 전통적으로 의도적인 동기적 Exception을 설명하는 용어
```

---

# 42. 면접 답변

> System Call은 User Mode에서 실행 중인 애플리케이션이 Kernel의 기능을 요청하기 위한 인터페이스입니다.
>
> 일반 애플리케이션은 시스템의 안정성과 보안을 위해 제한된 권한의 User Mode에서 실행되며, Page Table 변경이나 Device 제어 같은 Privileged Operation을 직접 수행할 수 없습니다. 이런 작업은 높은 권한을 가진 Kernel Mode에서 Kernel이 수행합니다.
>
> 애플리케이션이 파일 읽기, 프로세스 생성, 네트워크 통신 같은 기능이 필요하면 System Call을 통해 Kernel에 요청합니다. System Call Number와 Argument는 Register 등을 통해 전달할 수 있고, 큰 데이터나 많은 정보는 Memory에 저장한 뒤 Pointer를 전달할 수 있습니다.
>
> CPU는 `syscall`, `svc` 또는 Architecture에 따라 Trap과 같은 Kernel 진입 메커니즘을 이용해 User Mode에서 Kernel Mode로 전환합니다. System Call과 Trap은 별개의 요청을 두 번 하는 것이 아니라, System Call은 Kernel 서비스를 요청하는 개념이고 Trap이나 System Call Instruction은 실제 Kernel Mode에 진입하기 위한 수단입니다.
>
> User Mode에서 Kernel Mode로 전환되는 것은 Mode Switch이며, 반드시 Context Switching이 발생하는 것은 아닙니다. 같은 Process가 Kernel 작업을 수행한 뒤 다시 User Mode로 돌아갈 수 있습니다. 다만 `read()`처럼 I/O 대기가 필요한 경우에는 현재 Process가 Waiting 상태가 되고 Scheduler가 다른 Process를 선택하면서 Context Switching이 발생할 수 있습니다.
>
> Interrupt는 System Call과 다르게 Hardware가 CPU에 비동기적으로 Event를 알리는 메커니즘입니다. 예를 들어 Disk I/O가 완료되면 Disk가 Interrupt를 발생시키고 Kernel의 Interrupt Handler가 이를 처리할 수 있습니다.

---

# 43. 복습 질문

### Q1. User Mode와 Kernel Mode를 나누는 이유는?
일반 프로그램이 시스템 전체에 영향을 주는 중요한 자원을 직접 제어하지 못하도록 하여 안정성, 보안, Memory Protection, Process Isolation을 보장하기 위해서다.

### Q2. System Call이란?
User Mode 프로그램이 Kernel의 기능을 요청하기 위한 공식적인 인터페이스다.

### Q3. System Call Argument는 항상 Register에만 저장되는가?
아니다. 작은 값은 Register로 전달할 수 있지만 큰 데이터나 많은 정보는 Memory에 저장하고 Pointer를 Register로 전달할 수 있다.

### Q4. `read(fd, buffer, 100)`에서 실제 데이터도 Register로 전달하는가?
아니다. Buffer의 Memory Address와 읽을 크기 등의 정보만 전달하고 실제 데이터는 Memory를 통해 주고받는다.

### Q5. System Call이 발생하면 항상 Context Switching도 발생하는가?
아니다. System Call은 기본적으로 User Mode → Kernel Mode의 Mode Switch다. 같은 Process가 Kernel 작업을 수행하고 다시 User Mode로 돌아오면 Context Switching은 없다.

### Q6. 언제 System Call이 Context Switching으로 이어질 수 있는가?
I/O 대기처럼 현재 Process가 더 이상 즉시 실행할 수 없는 상황에서 Waiting 상태가 되고 Scheduler가 다른 Process를 선택할 때다.

### Q7. 한 CPU Core가 Kernel Mode일 때 다른 User Process는 동시에 실행할 수 있는가?
같은 Core에서는 동시에 실행할 수 없다. 하지만 이는 Kernel Mode 때문이라기보다 Core 하나가 한 순간에 하나의 실행 흐름만 실행하기 때문이다.

### Q8. Multi-Core CPU에서 한 Core가 Kernel Mode이면 다른 Core도 Kernel Mode인가?
아니다. 각 Core는 독립적으로 User Mode 또는 Kernel Mode 코드를 실행할 수 있다.

### Q9. Context Switching이 발생하면 항상 User Mode로 바뀌는가?
아니다. 새로 선택된 Process나 Thread가 어디에서 실행을 이어가야 하는지에 따라 User Mode일 수도 있고 Kernel Mode일 수도 있다.

### Q10. Interrupt란?
Hardware가 CPU에 처리해야 할 Event가 발생했다고 알리는 메커니즘이다.

### Q11. System Call과 Interrupt의 가장 큰 차이는?
System Call은 Software가 의도적으로 Kernel Service를 요청하는 것이고, Interrupt는 Hardware가 CPU에 비동기적으로 Event를 알리는 것이다.

### Q12. Interrupt가 발생하면 CPU는 무조건 즉시 현재 작업을 중단하는가?
아니다. Interrupt Priority, Masking, CPU Architecture 등의 규칙에 따라 적절한 시점에 처리된다.

### Q13. Interrupt Handler란?
Interrupt가 발생했을 때 해당 Event를 처리하는 Kernel의 코드이며 ISR이라고도 한다.

### Q14. Exception이란?
현재 실행 중인 Instruction 때문에 CPU가 발생시키는 동기적인 Event다. Page Fault와 Divide by Zero 등이 대표적인 예다.

### Q15. Trap은 무엇인가?
전통적인 설명에서는 프로그램이 특정 Instruction을 실행해 의도적으로 발생시키는 동기적인 Exception을 의미한다. 다만 정확한 용어 범위는 교재와 Architecture에 따라 다를 수 있다.

### Q16. Trap은 특정 CPU 명령어 이름인가?
아니다. Trap은 개념적으로 CPU가 처리하는 동기적인 Exception 종류 또는 메커니즘을 가리킨다. `int`, `syscall`, `svc` 같은 명령이 Kernel 진입을 발생시키는 실제 Instruction의 예다.

### Q17. 프로그램은 System Call을 호출한 뒤 Trap을 별도로 다시 호출하는가?
아니다. 프로그램은 System Call을 요청하고, 그 요청을 Kernel에 전달하기 위해 CPU의 Trap 또는 System Call Instruction 같은 Kernel 진입 메커니즘이 사용되는 것이다.

### Q18. System Call과 Trap을 한 문장으로 구분하면?
System Call은 **무엇을 요청하는가**, Trap이나 `syscall` Instruction은 **Kernel Mode로 어떻게 들어가는가**에 해당한다.

### Q19. Library Function과 System Call은 같은가?
아니다. Library Function은 User Space에서 실행되는 함수이고, 내부적으로 필요한 경우 System Call을 사용할 수 있다.

### Q20. Page Table Permission과 User/Kernel Mode는 어떻게 연결되는가?
Page Table에는 User가 접근할 수 있는 Page와 Kernel만 접근 가능한 Page를 구분하는 권한 정보가 있을 수 있으며, CPU의 Privilege Level과 함께 Memory Protection을 구현한다.
