[강의 링크](https://www.youtube.com/watch?v=sZbs9A32QoI)

## 시험 범위에 포함되는 내용
| 시험 토픽             | 근거                                                             |
| ----------------- | -------------------------------------------------------------- |
| **Few-shot CoT**  | Reasoning Example을 Prompt에 제공하는 CoT                            |
| **Zero-shot CoT** | Reasoning Example 없이 Trigger만 제공                               |
| **AI Agent 동작**   | Reasoning → Planning → Action → Observation → Replanning       |
| **LLM Agent의 특성** | Reasoning, Planning, Tool, Environment Interaction, Evaluation |
# 0. 강의 전체 개요

LLM은 처음에는 주로

```text
Question
↓
Answer
```

형태의 Model이었다.

하지만 Model이 발전하면서 단순히 정답을 바로 생성하는 것을 넘어

```text
Question
↓
Intermediate Reasoning
↓
Answer
```

처럼 **복잡한 문제를 단계적으로 생각하는 능력**이 중요해졌다.

이것이 **Reasoning**이다.

그리고 Agent에서는 여기서 한 단계 더 나아간다.

```text
Reasoning
↓
무엇을 해야 할지 생각
↓
Planning
↓
Action
↓
Tool / Environment
↓
Result
↓
다시 Reasoning
```

즉 Reasoning이 실제 행동을 설계하는 **Planning**으로 확장된다.

이번 강의 전체 흐름은 다음과 같다.

```text
LLM의 Reasoning Capability 발전
↓
Step-by-step Reasoning의 효과
↓
Chain-of-Thought
↓
Zero-shot CoT
↓
Self-consistency
↓
Tree of Thoughts

↓

Reasoning만으로는
현실 문제를 끝까지 해결할 수 없음
↓
Action 필요

↓

ReAct
Reasoning + Acting
↓
Tool 사용 결과를 다시 Reasoning

↓

더 복잡한 문제
↓
Planning
↓
HuggingGPT
Plan-and-Act
↓
Task Decomposition
Execution
Replanning

↓

그런데 Reasoning은 비싸다.
↓
Token ↑
Latency ↑
Compute ↑

↓

Test-time Compute
↓
추론 시간을 더 쓰면
성능이 좋아질 수 있음

↓

Reasoning-specific Training
↓
Prompt 없이도
Reasoning하는 Model

↓

하지만 모든 문제에
긴 Reasoning이 필요한 것은 아님
↓
Efficient Reasoning

↓

문제 난이도에 따라
Reasoning Budget을
동적으로 조절
```

---

# 1. Reasoning이 왜 중요해졌는가?

초기의 Language Model은 주로

```text
다음 Token 예측
↓
문장 생성
```

에 집중했다.

최근 LLM은 단순한 문장 생성뿐 아니라

```text
Pattern 발견

Rule 추론

Math Problem Solving

Code Architecture

Complex Decision
```

같은 능력을 보여주기 시작했다.

즉 LLM이

```text
Language Generator
```

에서

```text
Intelligent Problem Solver
```

로 발전하고 있다는 것이 강의의 출발점이다.

---

# 2. Reasoning이란?

강의에서 Reasoning은 복잡한 문제를 한 번에 바로 답하기보다

> **논리적인 Intermediate Step을 거치면서 문제를 단계적으로 해결하는 과정**

으로 설명된다.

가장 간단하게 보면

```text
Problem
↓
Step 1
↓
Step 2
↓
Step 3
↓
Answer
```

이다.

---

# 3. 단순 QA와 Reasoning의 차이

단순 QA:

```text
Question
↓
Answer
```

Reasoning:

```text
Question
↓
문제 구조 파악
↓
중간 계산
↓
논리적 연결
↓
Answer
```

이다.

---

# 4. 복잡한 Math Problem

강의에서는 Reasoning Capability가 두드러지는 대표 분야로 Math를 든다.

어려운 수학 문제는 단순히

```text
문제를 보고
정답 Token을 바로 예측
```

하는 방식으로 해결하기 어렵다.

대신

```text
조건 이해
↓
필요한 관계 발견
↓
계산
↓
Intermediate Result
↓
Final Answer
```

가 필요하다.

---

# 5. Pattern과 Rule을 발견하는 능력

강의에서 소개한 추론 Benchmark의 핵심은

```text
여러 Input–Output Example
↓
공통 Rule 발견
↓
새로운 Input에 적용
```

할 수 있는지를 평가하는 것이다.

즉 단순 Memorization이 아니라

```text
Underlying Structure
```

를 찾아내는 능력이다.

---

# 6. Coding에서도 Reasoning이 중요하다

초급 Coding Task는

```text
주어진 Function
↓
빈 부분 작성
```

처럼 비교적 Local한 문제일 수 있다.

하지만 경험이 많은 Software Engineer에게 요구되는 것은

```text
System 전체 구조
↓
Module 관계
↓
Interface
↓
Dependency
↓
Architecture
```

를 설계하는 능력이다.

---

# 7. Code Generation → Software Architecture

따라서 Coding LLM 평가도 단순히

```text
Function 하나를 잘 작성하는가?
```

뿐 아니라

```text
여러 Component를
논리적으로 연결하고

큰 Software Structure를
설계할 수 있는가?
```

까지 확장되고 있다는 것이 강의의 설명이다.

---

# 8. Reasoning Model이 좋아지면 Agent가 필요 없을까?

그렇지 않다.

강의에서는 이것을 명확하게 구분한다.

LLM Reasoning Capability가 아무리 강해져도

```text
몇 분

몇 시간

며칠
```

에 걸쳐 수행해야 하는 매우 긴 Process를 하나의 Reasoning Sequence로 완벽하게 처리하기에는 한계가 있다.

---

# 9. Long-horizon Problem

예를 들어

```text
Research Project

Software Project

Factory Workflow

Robot Task

Long Report
```

등은 한 번의 Model Call로 끝나지 않는다.

```text
Plan
↓
Action
↓
Result
↓
새로운 Situation
↓
Plan 수정
↓
Action
```

을 장시간 반복해야 한다.

이러한 문제를 **Long-horizon Task** 관점에서 볼 수 있다.

강의에서도 LLM의 Reasoning 자체가 강해지더라도, 훨씬 더 길고 복잡한 프로세스를 처리하려면 궁극적으로 Agent가 필요하다고 설명한다.

---

# 10. Physical AI에서의 Reasoning 한계

강의에서는 Physical AI를 예로 든다.

Robot이 어떤 조립 Task를 수행한다고 하자.

```text
Goal
↓
아래쪽 부품 조립
↓
옆 부품 조립
↓
전체 연결
```

처럼 순서를 Reasoning해야 한다.

---

# 11. Physical AI에서는 긴 Planning이 더 어렵다

강의에서는 현재 Physical AI Model 자체가 처리할 수 있는 Planning/Reasoning Horizon에는 한계가 있다고 설명한다.

따라서 매우 긴 Task는

```text
High-level LLM
↓
Long-term Plan
↓
Physical Agent
↓
Short-term Action
```

처럼 Hierarchical하게 나눌 수 있다.

---

# 12. Hierarchical Agent 구조

개념적으로

```text
Long-horizon Planner LLM
          ↓
     High-level Plan
          ↓
 ┌────────┼────────┐
 ↓        ↓        ↓
Robot    Robot    Robot
Agent    Agent    Agent
 ↓        ↓        ↓
Short    Short    Short
Action   Action   Action
```

처럼 구성할 수 있다.

---

# 13. Agent Architecture를 보는 연습

강의에서는 Agent System을 볼 때 단순히

```text
"이 Agent 좋네."
```

에서 끝내지 말고

```text
어떤 Agent들이 있는가?

각 Agent 역할은 무엇인가?

누가 Planning하는가?

Context는 어떻게 전달되는가?

Agent들은 어떻게 Communication하는가?
```

를 생각해 보는 것이 중요하다고 설명한다.

이것이 Agent System Design 역량을 키우는 방법이다.

---

# 14. Reasoning을 하지 않는 Answer

예를 들어 간단한 Arithmetic Word Problem이 있다.

Reasoning을 하지 않는 Model:

```text
Question
↓
Answer
```

만 출력한다.

장점:

```text
빠름

Token 적음
```

하지만 중간 판단을 거치지 않기 때문에 복잡한 문제에서 틀릴 가능성이 커질 수 있다.

---

# 15. Reasoning을 사용하는 Answer

Reasoning Model은

```text
Question
↓
조건 확인
↓
Intermediate Calculation
↓
Intermediate Calculation
↓
Answer
```

처럼 문제를 단계적으로 처리한다.

---

# 16. Step-by-step Reasoning의 효과

강의의 핵심 직관은

```text
복잡한 문제
↓
바로 Answer 예측
```

보다

```text
복잡한 문제
↓
Intermediate Step
↓
Problem Space를 좁힘
↓
Answer
```

이 더 안정적일 수 있다는 것이다.

---

# 17. 어떻게 LLM에게 Reasoning을 시킬까?

여기에서 등장한 대표적인 방법이 **Chain-of-Thought(CoT)** 다.

---

# 18. Chain-of-Thought

Chain-of-Thought의 핵심은

> **Question과 Answer만 Example로 보여주는 것이 아니라 그 Answer에 도달하는 Intermediate Reasoning 과정까지 Example로 보여준다.**

는 것이다.

---

# 19. 일반 Few-shot Prompt

```text
Question A
→ Answer A

Question B
→ Answer B

New Question
→ ?
```

---

# 20. CoT Prompt

```text
Question A
↓
Reasoning Step 1
↓
Reasoning Step 2
↓
Answer A


Question B
↓
Reasoning Step 1
↓
Reasoning Step 2
↓
Answer B


New Question
↓
Model도 비슷한 Reasoning Pattern 생성
↓
Answer
```

한다.

---

# 21. CoT의 핵심 발견

Model을 다시 Training하지 않아도

```text
Reasoning Example
```

을 Prompt에 넣는 것만으로 Model이 그 Pattern을 따라 복잡한 문제를 더 잘 풀 수 있었다.

즉

```text
Parameter Update X

Prompt Change O
```

만으로 Reasoning Behavior를 유도할 수 있었다.

---

# 22. 왜 중요한 발견이었는가?

당시 거대한 LLM을 다시 Training하려면

```text
GPU

Data

Compute

Money
```

가 많이 필요했다.

하지만 Prompting Research는 기존 Model을 그대로 사용할 수 있었다.

따라서 상대적으로 적은 Resource로 많은 연구자가 실험할 수 있었다.

---

# 23. Prompt Engineering 시대

CoT 이후

```text
Model 자체를 바꾸지 않고

Prompt를 어떻게 설계하면
Capability를 더 잘 끌어낼 수 있을까?
```

라는 연구가 매우 활발해졌다.

---

# 24. Zero-shot CoT

다음으로 강의에서 소개하는 대표적인 방법은 **Zero-shot Chain-of-Thought**다.

CoT:

```text
Reasoning Example 제공
```

Zero-shot CoT:

```text
Reasoning Example 없음

"Let's think step by step"
같은 지시만 제공
```

한다.

---

# 25. Zero-shot CoT의 의미

별도의 Reasoning Demonstration을 제공하지 않아도

```text
Step-by-step으로 생각해라.
```

는 짧은 Trigger만으로 Reasoning이 유도될 수 있었다.

---

# 26. 강의의 직관

강의에서는 대규모 Human Text로 Pre-training하면서

```text
Step-by-step 설명

논리적 풀이

Reasoning 과정
```

을 이미 많이 보았을 가능성이 있으므로 특정 표현이 이러한 학습된 Pattern을 Trigger했을 가능성을 설명한다.

이는 강의에서 제시한 직관적 해석이다.

---

# 27. CoT와 Zero-shot CoT 공통 메시지

```text
LLM 내부에
어느 정도 Reasoning Capability가 이미 존재

↓

적절한 Prompt로
그 Capability를 꺼낼 수 있음
```

이다.

---

# 28. Self-consistency

다음 아이디어는

> **하나의 Reasoning Path에만 의존하지 말자.**

이다.

---

# 29. 하나의 Path가 틀릴 수 있다

CoT:

```text
Question
↓
Reasoning Path A
↓
Answer
```

만 생성하면 Path A 자체가 틀린 경우 그대로 실패한다.

---

# 30. 여러 Reasoning Path 생성

Self-consistency에서는

```text
Question
├─ Reasoning Path A → Answer 18
├─ Reasoning Path B → Answer 26
├─ Reasoning Path C → Answer 18
└─ Reasoning Path D → Answer 18
```

처럼 여러 Reasoning Sequence를 생성한다.

---

# 31. Voting

마지막 Answer를 비교한다.

```text
18 → 3번

26 → 1번
```

이라면

```text
18
```

을 최종 Answer로 선택할 수 있다.

---

# 32. Self-consistency의 직관

사람도 어려운 문제에서

```text
한 가지 방법만 생각
```

하기보다

```text
방법 A

방법 B

방법 C
```

를 고려한 뒤 결과를 비교할 수 있다.

Self-consistency는 이 아이디어를 LLM Reasoning에 적용한다.

---

# 33. 단점

여러 Reasoning Path를 생성하므로

```text
Generation 횟수 ↑

Token ↑

Compute ↑

Latency ↑
```

가 된다.

즉 Accuracy를 위해 Inference Resource를 더 사용한다.

---

# 34. Tree of Thoughts

다음 단계는 **Tree of Thoughts(ToT)** 다.

Self-consistency에서는 여러 Reasoning Path를 끝까지 모두 생성한다.

하지만 사람은 보통 모든 가능성을 끝까지 탐색하지 않는다.

---

# 35. 인간의 Search

사람은

```text
Possible Idea A
Possible Idea B
Possible Idea C
```

를 생각한 뒤

```text
A는 가능성 없음 → 중단

B는 가능성 있음 → 더 탐색

C도 가능성 있음 → 더 탐색
```

하는 식으로 판단한다.

---

# 36. Tree 구조

```text
                 Start
             /     |     \
            A      B      C
            X     / \     X
                 B1 B2
                    |
                   B2-1
```

처럼 여러 Reasoning Branch를 만들고

```text
좋지 않은 Branch
→ Pruning

유망한 Branch
→ 계속 Expansion
```

한다.

---

# 37. Self-consistency vs Tree of Thoughts

### Self-consistency

```text
여러 Path를
끝까지 모두 생성
↓
마지막에 Vote
```

### Tree of Thoughts

```text
중간 과정에서
Path를 평가
↓
불필요한 Path 제거
↓
좋은 Path만 확장
```

한다.

---

# 38. Tree of Thoughts의 목적

```text
Search Space를 넓히면서도
불필요한 Reasoning을 줄인다.
```

는 것이다.

즉 Reasoning을 하나의 **Search Problem**처럼 다루는 관점이다.

---

# 39. Reasoning 연구의 초기 발전선

```text
Direct Answer
↓
CoT
↓
한 개 Reasoning Path

↓
Self-consistency
↓
여러 Reasoning Path

↓
Tree of Thoughts
↓
Reasoning Path를
Tree로 탐색하고 Pruning
```

으로 볼 수 있다.

---

# 40. Reasoning → Planning

여기서 강의의 중심이 Agent로 이동한다.

Reasoning을 할 수 있게 되면

```text
Goal을 달성하기 위해
어떤 순서로 행동해야 할지
```

생각할 수 있다.

이것이 **Planning**이다.

---

# 41. Reasoning과 Planning의 차이

### Reasoning

```text
현재 문제를
어떻게 논리적으로 이해하고
답을 도출할까?
```

### Planning

```text
Goal을 달성하기 위해
앞으로 어떤 Action을
어떤 순서로 실행할까?
```

이다.

---

# 42. Reasoning만 하는 LLM의 한계

Reasoning Model:

```text
Question
↓
Internal Reasoning
↓
Answer
```

으로 끝난다.

하지만 현실 문제는 External Environment와 Interaction해야 한다.

---

# 43. Action이 필요한 이유

Model이 현재 Knowledge만으로 답을 모르는데

```text
계속 내부적으로 Reasoning
```

해도 새로운 Evidence가 생기지 않는다.

이럴 때는

```text
Search

Calculator

Database

Code

Other Tool
```

을 사용해야 한다.

---

# 44. Reasoning + Action

```text
Think
↓
Action
↓
새 Information
↓
Think Again
```

구조가 필요해진다.

여기에서 **ReAct**가 등장한다.

---

# 45. ReAct

**ReAct = Reasoning + Acting**

Agent가

```text
Reasoning
↓
Action
↓
Observation
↓
Reasoning
↓
Action
↓
Observation
```

을 반복한다.

---

# 46. Direct Answer의 문제

예를 들어 외부 Knowledge가 필요한 Question이 있다.

Direct LLM:

```text
Question
↓
자신의 Parameter Knowledge
↓
Answer
```

만 사용한다.

Knowledge가 잘못되어 있으면 바로 틀릴 수 있다.

---

# 47. CoT만 사용하는 경우

```text
Question
↓
Reasoning
↓
Reasoning
↓
Answer
```

을 하더라도 새로운 외부 Evidence가 없기 때문에 틀린 Knowledge를 기반으로 계속 생각할 수 있다.

---

# 48. Act-only의 문제

반대로

```text
Question
↓
바로 Search
```

만 수행해도 좋은 결과가 보장되지 않는다.

왜냐하면

```text
무엇을 Search할지

어떤 Query를 만들지
```

Reasoning하지 않았기 때문이다.

---

# 49. ReAct의 해결

```text
Question
↓
Thought:
무엇을 알아야 하지?
↓
Action:
Search(Query A)
↓
Observation:
Search Result
↓
Thought:
정보가 부족하다.
다른 Query 필요
↓
Action:
Search(Query B)
↓
Observation
↓
Thought:
이제 답을 만들 수 있다.
↓
Answer
```

한다.

강의에서는 이를 초기 Agent Framework의 대표적인 형태로 설명한다.

---

# 50. ReAct의 핵심 혁신

```text
Reasoning만
```

또는

```text
Action만
```

하는 것이 아니라

```text
Reasoning
↔
Environment Interaction
```

을 하나의 Loop로 결합했다.

---

# 51. ReAct의 Agent Loop

```text
Thought
↓
Action
↓
Observation
↓
Thought
↓
Action
↓
Observation
↓
...
```

이다.

이 구조는 오늘날 Agent의 기본 동작과 매우 유사하다.

---

# 52. Reasoning이 Action의 방향을 결정

ReAct에서 Reasoning은 단순 설명용 Text가 아니다.

다음

```text
어떤 Tool을 사용할지

어떤 Query를 만들지

다음 Action이 무엇인지
```

를 결정한다.

즉 Reasoning이 **Policy / Planning** 역할까지 수행한다.

---

# 53. HuggingGPT

강의에서는 Planning과 Tool/Model Selection을 보여주는 사례로 **HuggingGPT**를 소개한다.

핵심 아이디어:

> **하나의 LLM이 모든 Task를 직접 해결하지 말고, Task를 분해한 뒤 Hugging Face 등에 존재하는 Specialized Model들을 선택해서 조합하자.**

---

# 54. 하나의 Request 안에 여러 Task가 있을 수 있다

예를 들어 User Request가

```text
Image 이해

Text 생성

Image 생성

Question Answering
```

같은 서로 다른 Capability를 동시에 요구할 수 있다.

---

# 55. 하나의 Specialized Model로는 해결하기 어려움

각 Task에 적합한 Model은 다를 수 있다.

```text
Vision Task
→ Vision Model

Text Task
→ Language Model

Image Generation
→ Image Model
```

이다.

---

# 56. Planner가 Task를 분해

```text
User Request
↓
Planning

Stage 1
Stage 2
Stage 3
```

으로 나눈다.

---

# 57. 필요한 Model 선택

```text
Stage 1
→ Model A

Stage 2
→ Model B

Stage 3
→ Model C
```

를 선택한다.

---

# 58. 결과 연결

```text
Model A Output
↓
Model B Input

Model B Output
↓
Model C Input
```

처럼 Pipeline을 구성한다.

---

# 59. HuggingGPT의 의미

중요한 점은

```text
모든 Capability를 가진
하나의 Giant Model
```

을 만드는 것이 아니라

```text
Planner LLM
+
Specialized Models
```

을 이용해 복잡한 Request를 해결한다는 것이다.

---

# 60. Agent와 Tool의 경계

여기서 Specialized Model도 Agent 입장에서는 하나의 Tool처럼 사용할 수 있다.

```text
Planner
↓
Image Model

Translator

Classifier

Generator
```

를 선택한다.

---

# 61. ReAct의 한계

ReAct는

```text
현재 State
↓
다음 Action
```

을 반복적으로 결정하는 데 강하다.

하지만 매우 긴 Task에서는

```text
전체 방향
```

이 없는 채 Local Decision만 반복하게 될 수 있다.

---

# 62. Long Task에는 Global Planning이 필요

예를 들어

```text
보고서를 작성하라.
```

라는 Goal이 있다고 하자.

바로 Action부터 시작하면

```text
Word 만드는 법 Search

조금 작성

자료가 부족함을 발견

다시 Search

다시 돌아감
```

처럼 비효율적인 Workflow가 될 수 있다.

---

# 63. 먼저 전체 Plan을 세운다

```text
Goal:
보고서 작성
↓
1. 필요한 자료 수집

2. 자료 분석

3. Report Structure 작성

4. Section별 Content 생성

5. Word File 생성

6. Review
```

처럼 전체 구조를 먼저 만든다.

---

# 64. Plan-and-Act

강의에서는 이러한 발전을 **Plan-and-Act** 구조로 설명한다.

핵심:

```text
Planning
↓
Execution
↓
Result Evaluation
↓
필요하면 Replanning
```

이다.

---

# 65. Plan-and-Act의 핵심

복잡한 Goal을

```text
통째로 바로 실행
```

하는 것이 아니라

```text
Goal
↓
Plan
↓
Small Tasks
↓
Execute
```

한다.

---

# 66. Task Decomposition

```text
Complex Task
↓
Subtask 1
Subtask 2
Subtask 3
Subtask 4
```

로 분해한다.

이것이 Planning의 핵심 기능 중 하나다.

---

# 67. Dependency

Task 간에는 순서가 존재할 수 있다.

```text
Research
↓
Analysis
↓
Writing
↓
Formatting
```

처럼 앞 Task 결과가 뒤 Task의 Input이 된다.

Planner는 이러한 Dependency를 고려한다.

---

# 68. Execution

Plan을 만든 뒤 각 Subtask를 실행한다.

```text
Plan
↓
Task 1 Execute
↓
Task 2 Execute
↓
Task 3 Execute
```

한다.

---

# 69. Replanning

실행 결과가 예상과 다르면

```text
Original Plan
↓
Execution
↓
Unexpected Result
↓
Plan 수정
↓
Retry
```

한다.

---

# 70. ReAct vs Plan-and-Act

### ReAct

```text
현재 상황에서
다음 한 Step을 생각
↓
Action
↓
Observation
↓
다음 Step
```

Local하고 Iterative하다.

### Plan-and-Act

```text
먼저 전체 Task Structure를 만든다.
↓
각 Task 실행
↓
필요하면 전체 Plan 수정
```

한다.

---

# 71. Local Planning vs Global Planning

개념적으로 연결하면

```text
ReAct
≈ Local Planning 중심

Plan-and-Act
≈ Global Planning + Execution
```

으로 이해할 수 있다.

둘은 배타적인 것이 아니라 함께 사용할 수 있다.

---

# 72. 실제 Agent에서는 둘을 결합할 수 있다

```text
Global Planner
↓
High-level Plan
↓
Local Agent
↓
ReAct
↓
Action / Observation
↓
Global Plan 수정
```

같은 구조가 가능하다.

---

# 73. Reasoning이 Planning을 가능하게 했다

강의의 중요한 발전선:

```text
LLM이 Reasoning을 할 수 있게 됨
↓
여러 Step을 생각할 수 있음
↓
미래 Action Sequence도 구성 가능
↓
Planning
↓
Agent
```

이다.

---

# 74. Reasoning에는 Cost가 있다

Reasoning의 장점만 있는 것은 아니다.

Direct Answer:

```text
Question
↓
Answer
```

Reasoning:

```text
Question
↓
Step
↓
Step
↓
Step
↓
Step
↓
Answer
```

이다.

당연히 더 많은 Token과 Compute가 필요하다.

---

# 75. Reasoning Cost

```text
Reasoning Length ↑
↓
Token ↑

Inference Time ↑

Compute ↑

Cost ↑
```

이다.

강의에서도 깊은 Reasoning은 더 많은 시간·Token·Computing Resource를 사용한다는 점을 핵심 Trade-off로 제시한다.

---

# 76. 중요한 Optimization 문제

그래서 질문이 바뀐다.

> **무조건 오래 생각하는 것이 아니라, 제한된 Budget 안에서 어떻게 최대 성능을 얻을 것인가?**

Budget은

```text
Token

Time

Money

Compute
```

일 수 있다.

---

# 77. Train-time Compute의 전통적 관점

과거에는 주로

```text
Model Size ↑

Training Data ↑

Training Compute ↑
```

에 Resource를 투입해 Model Capability를 높였다.

즉

```text
Training을 더 많이 한다.
↓
Model이 더 좋아진다.
```

라는 관점이었다.

---

# 78. Test-time Compute

그런데 중요한 관찰이 등장한다.

```text
Model Parameter를 바꾸지 않아도

Inference 시점에
더 많은 Compute를 쓰면

Performance가 올라갈 수 있다.
```

는 것이다.

---

# 79. 오래 생각하게 한다

사람이 어려운 문제를

```text
1초 생각
```

하는 것과

```text
10분 생각
```

하는 것이 다르듯,

Model에게 더 많은 Reasoning Budget을 주면 더 좋은 결과를 얻을 수 있다는 관점이다.

---

# 80. Training Scaling과 Inference Scaling

기존:

```text
More Train-time Compute
↓
Better Model
```

추가된 관점:

```text
More Test-time Compute
↓
Better Answer
```

이다.

---

# 81. Test-time Scaling

즉 성능을 높이는 방법이

```text
Model 자체를 더 크게 만든다.
```

뿐 아니라

```text
같은 Model을
Inference 시 더 깊게 생각하게 한다.
```

까지 확장된다.

---

# 82. Resource Allocation의 변화

전체 Compute Budget이 있다고 하자.

과거:

```text
Training에 대부분 사용
↓
Inference는 짧게
```

새로운 관점:

```text
Training Resource 일부
+
Inference Reasoning Resource 증가
```

라는 Trade-off를 고려할 수 있다.

---

# 83. Agent에서 Test-time Compute가 특히 중요

Agent는

```text
Planning

Tool Selection

Evaluation

Replanning
```

등 여러 Decision을 내려야 한다.

잘못된 초기 Decision이 전체 Workflow를 망칠 수 있기 때문에 중요한 Task에서는 충분한 Reasoning Budget을 사용하는 것이 가치가 있을 수 있다.

---

# 84. Prompting에서 Reasoning-specific Model로

초기에는

```text
CoT Prompt

"Think step by step"
```

같은 Prompt로 Reasoning을 유도했다.

하지만 이후에는

> **Prompt에서 굳이 Reasoning을 요구하지 않아도 기본적으로 Reasoning Behavior를 수행하는 Model을 만들자.**

는 방향으로 발전한다.

---

# 85. Reasoning-specific Training

강의에서는 Model Training 과정 자체에서

```text
Reasoning을 사용하며
Correct Answer를 만든 Behavior
```

에 더 높은 Reward를 제공하는 방향을 설명한다.

---

# 86. 단순 Correct Answer

기존 Training Signal:

```text
Answer Correct
↓
Reward / Loss 개선
```

---

# 87. Reasoning까지 강조

Reasoning Model Training:

```text
Reasoning Process
+
Correct Answer
↓
높은 Reward
```

를 주는 식으로 Reasoning Behavior를 강화한다.

---

# 88. 반복 Training

그러면 Model은

```text
바로 Answer 생성
```

보다

```text
적절한 Reasoning
↓
Answer 생성
```

을 했을 때 더 높은 Reward를 얻는다.

Training을 반복하면 Reasoning Behavior가 강화될 수 있다.

---

# 89. Reasoning Model

결과적으로 Prompt에

```text
Let's think step by step.
```

를 명시하지 않아도 기본적으로 복잡한 문제에서 Reasoning을 수행하도록 특화된 Model을 만들 수 있다.

---

# 90. Model 성능을 결정하는 세 축

강의에서는 Model을 만들 때 중요한 요소를 크게

```text
Data

Architecture

Training Recipe
```

로 설명한다.

---

# 91. Data

```text
어떤 Data를 사용할 것인가?
```

---

# 92. Architecture

```text
Model Structure를
어떻게 만들 것인가?
```

---

# 93. Training Recipe

```text
어떤 Stage로 학습할까?

각 Stage에 Compute를 얼마나 쓸까?

Data를 어떤 비율로 Mix할까?

어떤 Reward를 사용할까?
```

등이다.

---

# 94. Training Recipe의 중요성

강의에서는 최근 Model 개발에서 특히 Training Recipe가 매우 중요한 Know-how라고 설명한다.

같은 Architecture와 비슷한 Data를 사용해도

```text
어떤 순서

어떤 Objective

어떤 Hyperparameter

어떤 Training Stage
```

를 사용하느냐에 따라 성능이 달라질 수 있다.

---

# 95. Experience가 Recipe가 된다

실제로 Model을 여러 번 Training해본 Organization은

```text
이 방식은 실패한다.

이 Data Mix가 좋다.

이 Stage에서는 이 정도 Compute가 필요하다.
```

같은 Experience를 축적한다.

이것이 쉽게 복제하기 어려운 Know-how가 된다는 것이 강의의 관점이다.

---

# 96. Reasoning Model의 새로운 문제

Reasoning을 강하게 Training하면 어떤 Question을 주어도 Model이

```text
길게 생각
```

하려 할 수 있다.

하지만 모든 Question이 어려운 것은 아니다.

---

# 97. 쉬운 문제

예:

```text
서울은 한국의 수도인가?
```

는 긴 Reasoning이 필요하지 않다.

```text
짧게 판단
↓
Answer
```

하면 충분하다.

---

# 98. 어려운 문제

복잡한 Math 또는 Planning Task는

```text
Deep Reasoning

Many Steps
```

가 필요할 수 있다.

---

# 99. 문제의 핵심

```text
Easy Question
→ Long Reasoning
```

은 Resource Waste다.

반대로

```text
Hard Question
→ Short Reasoning
```

은 Accuracy가 낮아질 수 있다.

따라서

> **Question Difficulty에 맞게 Reasoning Budget을 동적으로 조절해야 한다.**

는 문제가 등장한다.

---

# 100. Efficient Reasoning의 세 방향

강의에서는 효율적인 Reasoning을 위한 방향을 크게 세 가지로 소개한다.

```text
1. Model-based Approach

2. Reasoning Result / Context-based Approach

3. Input Prompt-based Approach
```

로 볼 수 있다.

세부 명칭보다 중요한 것은

```text
문제에 맞게
얼마나 오래 생각할지를 조절
```

한다는 공통 목적이다.

---

# 101. ① Model-based Approach

Model이 스스로

```text
이 문제는 길게 생각해야 하나?

짧게 생각해도 되나?
```

를 판단하게 한다.

---

# 102. Control Token

강의에서는 Conceptual Example로

```text
Think Token

Short Token
```

같은 **Control Token**을 설명한다.

---

# 103. Think Token

```text
Question
↓
Model 판단
↓
THINK
↓
Long Reasoning
↓
Answer
```

이다.

---

# 104. Short Token

```text
Question
↓
Model 판단
↓
SHORT
↓
Short Reasoning
↓
Answer
```

이다.

---

# 105. 어떻게 학습할까?

예를 들어

```text
THINK 선택
+
Long Reasoning
+
Correct Answer
```

이면 높은 Reward를 줄 수 있다.

반대로

```text
THINK
+
많은 Compute 사용
+
Wrong Answer
```

이면 좋지 않은 Reward를 줄 수 있다.

---

# 106. 짧은 Reasoning도 마찬가지

```text
SHORT
+
Correct Answer
```

이면 효율적으로 문제를 풀었으므로 좋은 Reward를 줄 수 있다.

하지만

```text
SHORT
+
Wrong Answer
```

이면 문제를 너무 쉽게 판단한 것이므로 좋지 않게 평가할 수 있다.

---

# 107. Model이 배우는 것

반복적으로 학습하면 Model이

```text
Simple Problem
→ Short Reasoning

Complex Problem
→ Long Reasoning
```

을 선택하도록 유도할 수 있다.

---

# 108. 목적

```text
Accuracy
+
Efficiency
```

를 동시에 얻는 것이다.

즉 단순히 최고 Accuracy만 목표로 하지 않고

```text
Reasoning Budget 대비
높은 Performance
```

를 목표로 한다.

---

# 109. ② 긴 Context를 효율적으로 다루는 접근

Long Reasoning에서는 Context 자체가 매우 길어질 수 있다.

```text
Reasoning Token
Reasoning Token
Reasoning Token
...
```

이 계속 쌓이면 비용이 커진다.

---

# 110. 긴 Context의 문제

Context가 길면

```text
Input Token ↑

Attention / Inference Cost ↑

Latency ↑
```

한다.

---

# 111. 전체 Context를 항상 다시 볼 필요가 있을까?

강의에서 소개한 아이디어는 긴 Reasoning History를 매번 전부 사용하는 대신

```text
앞부분 Reasoning
↓
Summary
↓
Summary + 새로운 Context
↓
다음 Reasoning
```

처럼 처리하는 것이다.

---

# 112. Chunk-wise Reasoning

```text
Long Context
↓
Part 1 Reasoning
↓
Summary 1

Summary 1 + Part 2
↓
Reasoning
↓
Summary 2

Summary 2 + Part 3
↓
...
```

한다.

---

# 113. 핵심은 Context Compression

이 방식의 목적은

```text
모든 과거 Token을
계속 들고 가는 것
```

대신

```text
이전 Reasoning의
중요한 결과만 압축
```

해 유지하는 것이다.

강의에서도 긴 Context를 한 번에 모두 처리하기보다 일부를 Reasoning한 뒤 요약하고, 그 요약을 다음 Reasoning에 넘기는 방식으로 Compute를 줄이는 접근을 소개한다.

---

# 114. Memory와 연결

이 방식은 Agent Memory에서

```text
Raw Experience
↓
Summary / Insight
↓
Memory
```

로 만드는 구조와도 비슷하게 볼 수 있다.

다만 이 강의에서는 Reasoning Context를 효율적으로 관리하는 관점으로 소개된다.

---

# 115. ③ Input Prompt-based Approach

세 번째는 Input Question 자체를 보고

```text
얼마나 Reasoning할지
```

결정하는 방식이다.

---

# 116. Easy Prompt

```text
서울은 한국의 수도인가?
```

처럼 단순한 Question은

```text
Reasoning Budget ↓
↓
Fast Answer
```

가 효율적이다.

---

# 117. Hard Prompt

복잡한 Problem은

```text
Reasoning Budget ↑
↓
Deep Reasoning
```

을 사용한다.

강의 후반에서도 쉬운 질문에는 연산을 최소화하고, 복잡한 질문에는 더 많은 Reasoning Step을 사용하는 것이 효율적이라고 설명한다.

---

# 118. Router

이를 System 수준에서 구현하려면 Question을 먼저 분석하는 작은 **Router**를 둘 수 있다.

```text
User Query
↓
Router
↓
Query Complexity 판단
```

한다.

---

# 119. Router가 보는 정보

강의에서는 예를 들어

```text
Question Complexity

Sentence Length

사용된 단어

Task Type
```

등을 바탕으로 판단할 수 있다고 설명한다.

---

# 120. Routing

```text
                  Query
                    ↓
                  Router
             ┌──────┴──────┐
             ↓             ↓
        Fast Path      Deep Reasoning
             ↓             ↓
       Short Answer     Long Reasoning
```

처럼 보낼 수 있다.

---

# 121. MoE와 유사한 직관

강의에서는 이러한 Routing을 **Mixture of Experts(MoE)** 구조와 비슷한 직관으로 설명한다.

MoE:

```text
Input
↓
Router
↓
Appropriate Expert
```

Reasoning Routing:

```text
Question
↓
Router
↓
Appropriate Reasoning Mode
```

이다.

---

# 122. Agent System에서의 Routing

실제 Agent에서는

```text
Simple Task
→ Fast Model

Complex Task
→ Reasoning Model
```

처럼 Model 자체를 Routing할 수도 있고,

같은 Model 안에서

```text
Fast Mode

Think Mode

Deep Mode
```

처럼 Reasoning Budget을 달리할 수도 있다.

강의에서는 상용 Agent System에서도 질문 복잡도 등에 따라 Reasoning 경로를 선택하는 작은 Router를 앞단에 두는 방식을 설명한다.

---

# 123. Reasoning Budget의 핵심 Trade-off

```text
Reasoning ↑
↓
Accuracy ↑ 가능성

하지만

Latency ↑
Cost ↑
Token ↑
```

이다.

따라서 최선의 System은

```text
Always Maximum Reasoning
```

이 아니다.

---

# 124. Adaptive Reasoning

이상적인 방향:

```text
Easy Task
→ Think Less

Hard Task
→ Think More
```

이다.

이를 **Adaptive Reasoning** 관점으로 이해할 수 있다.

---

# 125. Reasoning과 Planning의 전체 관계

```text
Reasoning
= 문제를 어떻게 풀지 생각

↓

Planning
= 그 생각을
실제 Action Sequence로 구성

↓

Execution
= Action 수행

↓

Observation
= 결과 확인

↓

Reasoning
= 결과를 다시 분석

↓

Replanning
```

한다.

---

# 126. Agent의 전체 Loop로 넣으면

```text
Goal
↓
Perception
↓
Reasoning
↓
Planning
↓
Tool / Action
↓
Observation
↓
Evaluation
↓
Reasoning
↓
Replanning
↓
Action
↓
...
```

이다.

---

# 127. Reasoning만 잘하는 Model과 좋은 Agent는 다르다

Reasoning Model:

```text
문제 내부에서
논리적으로 생각
```

한다.

Good Agent:

```text
Reasoning
+
Planning
+
Tool
+
Environment Interaction
+
Evaluation
+
Replanning
```

까지 수행한다.

---

# 128. Reasoning은 Agent의 Brain 역할

Agent의 여러 Component 중

```text
Memory
Tools
Environment
```

가 정보를 제공한다면,

Reasoning은 이 정보들을 이용해

```text
무엇을 해야 할지
```

판단한다.

---

# 129. Planning은 Brain과 Action 사이의 Bridge

```text
Goal
↓
Reasoning
↓
Planning
↓
Action
```

이다.

즉 Planning은 내부 Intelligence를 실제 Behavior로 변환한다.

---

# 130. CoT와 Planning은 같은가?

아니다.

### CoT

```text
Answer를 만들기 위한
Intermediate Reasoning
```

### Planning

```text
Goal을 달성하기 위한
Future Action Sequence
```

이다.

---

# 131. CoT와 ReAct도 다르다

### CoT

```text
Thought
↓
Thought
↓
Thought
↓
Answer
```

### ReAct

```text
Thought
↓
Action
↓
Observation
↓
Thought
↓
Action
```

이다.

---

# 132. ReAct와 Plan-and-Act도 다르다

### ReAct

현재 State에서 다음 Action을 반복적으로 정한다.

### Plan-and-Act

전체 구조를 먼저 만들고 Subtask를 실행한다.

---

# 133. Self-consistency와 ToT도 다르다

### Self-consistency

```text
여러 완성된 Reasoning Path
↓
Voting
```

### Tree of Thoughts

```text
중간 Branch 평가
↓
Pruning
↓
유망한 Branch만 계속 탐색
```

한다.

---

# 134. Train-time Compute vs Test-time Compute

### Train-time

```text
Model Parameter를
더 잘 학습시키기 위한 Compute
```

### Test-time

```text
이미 Training된 Model이
Answer를 만들 때 사용하는 Compute
```

이다.

---

# 135. Test-time Compute의 의미

Model Weight를 바꾸지 않고도

```text
더 많이 Sampling

더 긴 Reasoning

여러 Path 비교

Search

Verification
```

등을 수행하면 결과를 개선할 수 있다.

---

# 136. Test-time Compute와 Self-consistency 연결

Self-consistency는 대표적으로

```text
같은 Model
+
더 많은 Inference
```

를 사용한다.

즉 Parameter는 동일하지만 여러 Reasoning Path를 만들면서 Test-time Compute를 증가시킨다.

---

# 137. Tree of Thoughts도 Test-time Compute 사용

여러 Branch를 탐색하고 평가하므로

```text
Reasoning Search
```

에 추가 Compute를 사용한다.

---

# 138. Agent 자체도 Test-time Compute System

Agent는

```text
Search

Tool Call

Retry

Reflection

Replanning
```

을 반복한다.

따라서 강력한 Agent System은 단순 Model Inference보다 훨씬 많은 Test-time Compute를 사용할 수 있다.

---

# 139. 그러므로 Efficiency가 중요해진다

Agent의 Capability를 계속 높이기 위해

```text
Tool을 계속 호출

Reasoning 계속 수행

Agent 여러 개 호출
```

하면 Cost가 빠르게 증가할 수 있다.

따라서 Agent Engineering에서는

```text
Intelligence
vs
Cost
```

Trade-off가 중요하다.

---

# 140. 쉬운 문제에는 Agent도 과할 수 있다

예:

```text
1 + 1은?
```

에

```text
Planner Agent

Research Agent

Calculator

Verifier Agent
```

를 모두 호출하는 것은 비효율적이다.

문제 Complexity에 맞는 System Path가 필요하다.

---

# 141. 복잡한 문제는 반대

예:

```text
시장 조사 후
경쟁사를 분석하고
보고서를 작성하여
파일로 저장하라.
```

같은 Task는

```text
Deep Reasoning

Planning

Search

Tool Use

Validation
```

이 필요할 수 있다.

---

# 142. Agent System의 이상적인 방향

```text
Task
↓
Difficulty / Type 판단
↓
필요한 Compute 결정
↓
필요한 Agent 결정
↓
필요한 Tool 결정
↓
Execution
```

이다.

---

# 143. 앞 강의와 연결 — Autonomy

Introduction 강의에서 Agent의 가장 중요한 특성은 **Autonomy**였다.

이번 강의에서는 그 Autonomy를 실제로 가능하게 하는 핵심 Mechanism이 보인다.

```text
Autonomy
↓
Reasoning
↓
Planning
↓
Action Selection
```

이다.

---

# 144. 앞 강의와 연결 — Perception

Agent가 Reasoning하려면 먼저

```text
현재 State
```

를 알아야 한다.

따라서

```text
Perception
↓
Reasoning
```

이다.

---

# 145. 앞 강의와 연결 — Memory

Planning에서는 과거 경험도 필요하다.

```text
Current State
+
Past Experience
↓
Reasoning
↓
Better Plan
```

이다.

---

# 146. 앞 강의와 연결 — Tool

Reasoning만으로 부족하면

```text
Tool
```

을 호출한다.

ReAct가 바로

```text
Reasoning
+
Tool Action
```

을 결합한 대표 구조다.

---

# 147. 앞 강의와 연결 — Learning & Adaptation

Action 결과를 본 뒤

```text
이 Plan이 틀렸다.
```

고 판단하면

```text
Reasoning
↓
Replanning
```

한다.

이것이 Adaptation과 연결된다.

---

# 148. Agent의 여섯 특징이 하나로 연결

```text
Perception
↓
Memory
↓
Reasoning
↓
Planning
↓
Tool Use / Action
↓
Evaluation
↓
Learning & Adaptation
↓
다시 Reasoning
```

이다.

그리고 이 전체 Process를 인간이 매번 직접 지시하지 않는 것이 **Autonomy**다.

---

# 149. 이번 강의에서 반드시 기억할 핵심

### 1. Reasoning은 복잡한 문제를 바로 답하지 않고 Intermediate Step을 거쳐 단계적으로 해결하는 능력이다.

### 2. 최근 LLM은 단순 Language Generation을 넘어 Pattern·Rule을 발견하고 복잡한 Math와 Coding 문제를 해결하는 Reasoning Capability를 보이고 있다.

### 3. Coding에서도 단순 Function 작성뿐 아니라 Software Architecture를 설계하는 고차원 Reasoning이 중요하다.

### 4. Reasoning Model이 강해져도 며칠에 걸친 Long-horizon Task를 하나의 Model Call로 해결하기 어려우므로 Agent가 필요하다.

### 5. Physical AI에서도 긴 Process는 High-level LLM Planner와 Short-horizon Robot Agent로 분리할 수 있다.

### 6. Reasoning을 사용하지 않는 Model은 빠르게 답할 수 있지만 복잡한 문제에서는 Error가 발생하기 쉽다.

### 7. Step-by-step Reasoning을 통해 Intermediate State를 만들면서 문제를 좁혀가는 것이 Accuracy 향상에 도움이 될 수 있다.

### 8. Chain-of-Thought는 Example에 Question–Reasoning Process–Answer를 함께 제공하여 Model이 Intermediate Reasoning Pattern을 따라 하도록 유도한다.

### 9. CoT는 Model Parameter를 다시 Training하지 않고 Prompt만으로 Reasoning Capability를 끌어낼 수 있다는 점에서 중요했다.

### 10. Zero-shot CoT는 Reasoning Example 없이 “step by step으로 생각하라”는 Trigger만으로 Reasoning을 유도하는 방법이다.

### 11. CoT와 Zero-shot CoT는 LLM 내부에 이미 존재하는 Reasoning Pattern을 Prompt가 유도할 수 있음을 보여준다.

### 12. Self-consistency는 하나의 Reasoning Path만 사용하는 대신 여러 Path를 생성하고 Final Answer를 Voting한다.

### 13. 여러 Reasoning Path를 사용하므로 Self-consistency는 더 많은 Test-time Compute를 사용한다.

### 14. Tree of Thoughts는 여러 Reasoning Branch를 만들고 중간에 좋지 않은 Branch를 Pruning하며 유망한 Path를 계속 탐색한다.

### 15. Self-consistency가 여러 Path를 끝까지 생성한 뒤 Voting한다면 ToT는 중간 단계에서 Path를 선택·제거한다.

### 16. Reasoning Capability가 발전하면서 LLM은 Goal 달성을 위한 Action Sequence를 설계하는 Planning까지 수행할 수 있게 된다.

### 17. Reasoning은 문제를 어떻게 풀지 생각하는 것이고 Planning은 목표를 달성하기 위한 미래 Action Sequence를 구성하는 것이다.

### 18. Internal Reasoning만으로 새로운 External Evidence를 얻을 수 없기 때문에 현실 문제에서는 Action과 Tool Use가 필요하다.

### 19. ReAct는 Reasoning과 Acting을 하나의 반복 Loop로 결합한다.

### 20. ReAct의 기본 구조는 Thought → Action → Observation → Thought → Action이다.

### 21. CoT만 수행하면 외부 정보가 부족할 수 있고 Act-only 방식은 어떤 Action이 필요한지 충분히 생각하지 않을 수 있다.

### 22. ReAct는 먼저 무엇이 필요한지 Reasoning하고 Tool을 실행한 뒤 결과를 다시 Reasoning한다.

### 23. ReAct는 오늘날 Agent의 기본적인 Reasoning–Action–Observation Loop를 보여주는 초기 대표 구조로 이해할 수 있다.

### 24. HuggingGPT는 하나의 LLM이 모든 Task를 직접 처리하는 대신 Request를 여러 Stage로 Planning하고 필요한 Specialized Model을 선택해 연결하는 방향을 보여준다.

### 25. Specialized Model도 Planner Agent에게는 하나의 Tool처럼 사용될 수 있다.

### 26. 매우 긴 Task에서는 Local Action만 반복하기보다 전체 Goal을 먼저 Task 단위로 분해하는 Global Planning이 유용하다.

### 27. Plan-and-Act에서는 먼저 Plan을 만들고 Subtask를 실행한 뒤 결과에 따라 Replanning한다.

### 28. Task Decomposition은 복잡한 Goal을 더 작은 실행 가능한 Unit으로 나누는 과정이다.

### 29. Planner는 Subtask 간 Dependency와 Execution Order를 고려해야 한다.

### 30. ReAct는 Local Iterative Planning에 가깝고 Plan-and-Act는 전체 Plan을 먼저 만드는 Global Planning 성격이 강하다.

### 31. 실제 Agent에서는 Global Plan과 Local ReAct를 함께 사용할 수 있다.

### 32. Reasoning을 많이 수행하면 Token, Latency, Compute와 Cost가 증가한다.

### 33. 따라서 Agent에서 중요한 문제는 제한된 Reasoning Budget 안에서 Performance를 최대화하는 것이다.

### 34. 전통적인 AI Scaling은 Train-time Compute를 늘려 Model Capability를 높이는 데 집중했다.

### 35. Test-time Compute는 Training이 끝난 Model이 Answer를 생성하는 시점에 더 많은 Compute를 사용하는 것이다.

### 36. Model Parameter를 그대로 두고 Inference 단계에서 더 오래 Reasoning하거나 여러 Candidate를 탐색해도 Performance가 향상될 수 있다.

### 37. 이는 “Model을 더 크게 Training해야만 성능을 높일 수 있다”는 관점에서 벗어난 중요한 변화다.

### 38. Self-consistency와 Tree of Thoughts는 같은 Model에 더 많은 Inference Compute를 사용하는 Test-time Scaling의 예로 이해할 수 있다.

### 39. Agent의 Search·Retry·Tool Call·Reflection·Replanning 역시 많은 Test-time Compute를 사용할 수 있다.

### 40. 초기 Reasoning은 CoT와 같은 Prompt로 유도했지만 이후에는 Model Training 자체에서 Reasoning Behavior를 강화하는 방향으로 발전했다.

### 41. Reasoning 과정과 Correct Answer에 Reward를 제공하여 Reasoning을 기본 Behavior로 강화할 수 있다.

### 42. 이렇게 학습된 Reasoning-specific Model은 별도의 CoT Prompt가 없어도 복잡한 문제에서 Reasoning할 수 있다.

### 43. Model 성능에는 Data, Architecture뿐 아니라 어떤 Stage와 Objective로 Training하는지에 대한 Training Recipe도 중요하다.

### 44. 실제 Model Training Experience에서 축적된 Recipe는 중요한 조직 Know-how가 될 수 있다.

### 45. Reasoning-specific Model이 모든 질문에서 길게 생각하면 단순 Question에서도 불필요한 Cost가 발생한다.

### 46. 쉬운 문제는 짧게 Reasoning하고 어려운 문제는 더 오래 Reasoning하도록 Adaptive하게 Budget을 조절하는 것이 필요하다.

### 47. 강의에서는 Reasoning Efficiency를 높이는 방향으로 Model-based, Reasoning/Context-based, Input Prompt-based 접근을 소개한다.

### 48. Model-based 접근에서는 Model이 Control Token 등을 이용해 Long Reasoning과 Short Reasoning을 선택할 수 있다.

### 49. 문제 난이도에 맞는 Reasoning Length를 선택했을 때 높은 Reward를 주는 방식으로 학습할 수 있다.

### 50. Long Context Reasoning에서는 이전 Reasoning 전체를 계속 유지하는 대신 일부를 처리한 뒤 Summary로 압축하여 다음 Reasoning에 전달하는 방법을 생각할 수 있다.

### 51. Context Compression은 긴 Reasoning History로 인한 Token과 Compute 부담을 줄이려는 방향이다.

### 52. Input Prompt-based 접근에서는 Question의 난이도에 따라 필요한 Reasoning Budget을 조절한다.

### 53. 간단한 Factual Question은 빠르게 답하고 복잡한 Problem은 Deep Reasoning을 수행하는 것이 효율적이다.

### 54. System 앞단에 작은 Router를 두어 Query Complexity나 Task Type 등을 판단하고 적절한 Reasoning Path로 보낼 수 있다.

### 55. 이 구조는 Input에 따라 적절한 Expert를 선택하는 MoE의 Router와 유사한 직관으로 이해할 수 있다.

### 56. Agent System에서도 Fast Path와 Deep Reasoning Path를 분리할 수 있다.

### 57. Agent 성능을 높이는 최선의 방법이 항상 최대 Reasoning을 사용하는 것은 아니다.

### 58. 중요한 것은 Task Complexity에 맞는 Reasoning·Model·Tool·Agent Resource를 선택하는 것이다.

### 59. Reasoning은 Agent 내부의 Decision Intelligence를 제공하고 Planning은 그 Intelligence를 Future Action Sequence로 변환한다.

### 60. 좋은 Agent는 Reasoning만 잘하는 Model이 아니라 Reasoning → Planning → Action → Observation → Evaluation → Replanning을 반복할 수 있는 System이다.

---

# 150. 주요 개념 비교

|개념|핵심|
|---|---|
|Direct Answer|Reasoning 없이 바로 답|
|CoT|하나의 Step-by-step Reasoning|
|Zero-shot CoT|Example 없이 Reasoning Trigger|
|Self-consistency|여러 Reasoning Path 후 Voting|
|Tree of Thoughts|여러 Branch 탐색 + Pruning|
|ReAct|Reasoning + Action + Observation 반복|
|Planning|미래 Action Sequence 설계|
|Plan-and-Act|전체 Plan → Execution → Replanning|
|Test-time Compute|Inference 시 더 많은 연산 사용|
|Adaptive Reasoning|문제 난이도에 따라 Reasoning Budget 조절|

---

# 151. CoT vs Zero-shot CoT

|구분|CoT|Zero-shot CoT|
|---|---|---|
|Reasoning Example|제공|제공하지 않음|
|방법|풀이 과정을 Demonstration|Reasoning Trigger 문구|
|Parameter Update|X|X|
|목적|단계적 Reasoning 유도|단계적 Reasoning 유도|

---

# 152. CoT vs Self-consistency vs ToT

|구분|CoT|Self-consistency|Tree of Thoughts|
|---|---|---|---|
|Path 수|1개|여러 개|여러 Branch|
|중간 평가|거의 없음|없음|있음|
|선택|한 Path|Final Voting|Branch Pruning|
|Compute|상대적으로 적음|큼|큼|
|핵심|Step-by-step|여러 풀이 비교|Search|

---

# 153. Reasoning vs Planning

|구분|Reasoning|Planning|
|---|---|---|
|질문|어떻게 이해/해결할까?|무엇을 어떤 순서로 할까?|
|결과|Intermediate Thought|Action Sequence|
|대상|Problem|Goal|
|Agent에서 역할|판단|실행 구조 설계|

---

# 154. CoT vs ReAct

|구분|CoT|ReAct|
|---|---|---|
|Thought|O|O|
|External Action|X|O|
|Observation|X|O|
|Tool Use|핵심 아님|핵심 가능|
|구조|Thought → Answer|Thought → Action → Observation 반복|

---

# 155. ReAct vs Plan-and-Act

|구분|ReAct|Plan-and-Act|
|---|---|---|
|중심|다음 Action|전체 Plan|
|Planning 범위|Local|Global|
|방식|Step마다 판단|먼저 Task 분해|
|Replanning|자연스럽게 반복|Execution 결과에 따라 수행|
|Long Task|구조 부족 가능|유리|

---

# 156. Train-time vs Test-time Compute

|구분|Train-time|Test-time|
|---|---|---|
|시점|Model 학습|Model 사용|
|Parameter Update|O|X|
|성능 향상 방식|더 좋은 Model|더 좋은 Inference|
|예|더 오래 Training|Longer Reasoning, Multiple Paths|
|비용 발생|Training|Query마다 Inference|

---

# 157. 자주 헷갈리는 점 ① — Reasoning = 답변을 길게 쓰는 것인가?

아니다.

핵심은 길이가 아니라

```text
문제를 중간 논리 단계로 나누어
해결하는 것
```

이다.

---

# 158. 자주 헷갈리는 점 ② — CoT를 사용하면 Model이 Training되는가?

아니다.

CoT Prompting 자체는 Inference 시 Prompt를 통해 Reasoning Behavior를 유도한다.

Parameter는 Update되지 않는다.

---

# 159. 자주 헷갈리는 점 ③ — Zero-shot CoT는 Zero-shot이므로 Prompt가 없는가?

아니다.

Reasoning Demonstration Example이 없다는 의미다.

Reasoning을 유도하는 Instruction은 존재할 수 있다.

---

# 160. 자주 헷갈리는 점 ④ — Self-consistency는 여러 Model을 사용하는가?

반드시 아니다.

같은 Model에서 여러 Reasoning Path를 Sampling한 뒤 결과를 비교할 수 있다.

---

# 161. 자주 헷갈리는 점 ⑤ — Tree of Thoughts는 모든 Branch를 끝까지 실행하는가?

아니다.

중간에 Branch를 평가하고 불필요한 Path를 Pruning하는 것이 핵심이다.

---

# 162. 자주 헷갈리는 점 ⑥ — Reasoning Model이 좋아지면 Agent가 필요 없어지는가?

아니다.

Agent는 Reasoning뿐 아니라 Tool Use, Environment Interaction, Long-horizon Planning과 Replanning을 수행한다.

---

# 163. 자주 헷갈리는 점 ⑦ — Planning과 Reasoning은 동일한가?

아니다.

Reasoning은 문제 해결을 위한 사고이고 Planning은 Goal 달성을 위한 Action Sequence 설계다.

---

# 164. 자주 헷갈리는 점 ⑧ — ReAct는 그냥 CoT에 Search를 붙인 것인가?

구조적으로는 Reasoning과 External Action 및 Observation을 반복 Loop로 통합한다는 점이 핵심이다.

---

# 165. 자주 헷갈리는 점 ⑨ — Tool을 많이 호출하면 좋은 Agent인가?

아니다.

불필요한 Tool Call은 Cost와 Latency를 증가시킨다.

Reasoning을 통해 필요한 Action을 선택하는 것이 중요하다.

---

# 166. 자주 헷갈리는 점 ⑩ — Plan을 한번 세우면 끝까지 그대로 실행하는가?

아니다.

Environment Result에 따라 **Replanning**할 수 있어야 한다.

---

# 167. 자주 헷갈리는 점 ⑪ — Global Plan이 있으면 Local Reasoning은 필요 없는가?

아니다.

전체 방향과 실제 각 Step의 상황 대응은 별개의 문제다.

둘을 함께 사용할 수 있다.

---

# 168. 자주 헷갈리는 점 ⑫ — Test-time Compute는 Model Training을 더 하는 것인가?

아니다.

이미 Training된 Model의 Inference 시점에서 더 많은 Compute를 사용하는 것이다.

---

# 169. 자주 헷갈리는 점 ⑬ — Reasoning을 오래 할수록 무조건 좋은가?

아니다.

Compute와 Token Cost가 증가하며 간단한 문제에서는 불필요할 수 있다.

강의의 후반 핵심이 바로 **Reasoning Budget을 문제 난이도에 맞게 조절하는 것**이다.

---

# 170. 자주 헷갈리는 점 ⑭ — Reasoning-specific Model은 항상 CoT Prompt가 필요한가?

강의에서 설명한 발전 방향은 Training 자체에서 Reasoning Behavior를 강화하여 별도 Trigger가 없어도 Reasoning하도록 만드는 것이다.

---

# 171. 자주 헷갈리는 점 ⑮ — Model Performance는 Architecture와 Data만으로 결정되는가?

아니다.

강의에서는 Training Stage와 Objective, Data Mixing 등을 포함한 **Training Recipe**도 매우 중요하다고 설명한다.

---

# 172. 자주 헷갈리는 점 ⑯ — 긴 Context는 모두 그대로 유지하는 것이 좋나?

아니다.

필요한 Intermediate Result를 Summary로 압축하여 Reasoning Context를 줄이는 방향도 소개된다.

---

# 173. 자주 헷갈리는 점 ⑰ — Fast Model과 Reasoning Model 중 하나만 선택해야 하는가?

아니다.

Router를 이용해 Task Difficulty에 따라 다른 Reasoning Path 또는 Model을 선택할 수 있다.

---

# 174. 이번 강의를 문제 해결 흐름으로 보기

```text
[문제 1]

LLM이 바로 Answer를 내면
복잡한 문제에서 잘 틀린다.
↓
Step-by-step으로 생각시키자.
↓
Chain-of-Thought


[문제 2]

Reasoning Example을
매번 만들기 어렵다.
↓
짧은 Trigger로도 가능할까?
↓
Zero-shot CoT


[문제 3]

Reasoning Path 하나가
틀릴 수 있다.
↓
여러 Path를 만들어 보자.
↓
Self-consistency


[문제 4]

모든 Path를
끝까지 생성하면 비효율적이다.
↓
중간에 좋은 Path만 남기자.
↓
Tree of Thoughts


[문제 5]

내부적으로 생각만 해서는
새로운 외부 정보를 얻지 못한다.
↓
Action을 하자.
↓
ReAct


[문제 6]

복잡한 Request는
한 Tool/Model로 해결할 수 없다.
↓
Task를 분해하고
전문 Model을 조합하자.
↓
HuggingGPT


[문제 7]

Action을 하나씩만 고르면
Long Task에서 방향을 잃는다.
↓
전체 Plan부터 세우자.
↓
Plan-and-Act


[문제 8]

Reasoning이 길어질수록
Token과 Cost가 증가한다.
↓
그런데 더 오래 생각하면
성능은 좋아질 수 있다.
↓
Test-time Compute


[문제 9]

Prompt를 줘야만
Reasoning하면 불편하다.
↓
Training 자체에서
Reasoning을 강화하자.
↓
Reasoning-specific Model


[문제 10]

모든 문제를 길게 생각하면
너무 비싸다.
↓
문제 난이도에 따라
Reasoning Budget을 조절하자.
↓
Adaptive / Efficient Reasoning


[방법]

Model이 Length 선택

Long Context를 Summary

Prompt Complexity를 Router가 판단
```

---

# 175. 이번 강의 전체 논리

```text
LLM이 똑똑해졌다.
      ↓
단순히 Text를 생성하는 것을 넘어
복잡한 문제를 생각할 수 있다.
      ↓
Reasoning


그런데
바로 Answer를 내면
복잡한 문제에서 틀린다.
      ↓
Step-by-step
      ↓
CoT


Reasoning Example 없이도?
      ↓
Zero-shot CoT


Reasoning Path 하나는 위험
      ↓
여러 Path
      ↓
Self-consistency


모든 Path를
끝까지 볼 필요는 없다.
      ↓
Tree of Thoughts
      ↓
Search + Pruning


하지만
생각만 해서
현실 문제를 해결할 수 없다.
      ↓
Action 필요
      ↓
ReAct


Thought
↓
Action
↓
Observation
↓
Thought
↓
...


그리고 문제는 더 복잡하다.
      ↓
여러 Specialized Model이 필요
      ↓
Planning
      ↓
HuggingGPT


Long-horizon Task
      ↓
먼저 전체 구조를 만들자.
      ↓
Plan-and-Act

Plan
↓
Execute
↓
Evaluate
↓
Replan


Reasoning과 Planning이 좋아졌다.
      ↓
그런데 너무 비싸다.
      ↓
Token
Latency
Compute
Cost


그런데
Inference 때 더 오래 생각하면
성능이 좋아지기도 한다.
      ↓
Test-time Compute


그럼
무조건 오래 생각해야 하나?
      ↓
아니다.


Easy Task
→ Short Reasoning

Hard Task
→ Deep Reasoning


이를 위해
      ↓
Control Token

Context Summarization

Prompt-based Router


최종 목표
      ↓
제한된 Compute Budget 안에서
최대한 좋은 Reasoning과 Planning을
수행하는 Agent
```

---

# 176. 이번 강의 핵심 한 문장

> **AI Agent의 Reasoning은 복잡한 문제를 Intermediate Step으로 나누어 논리적으로 해결하는 능력으로, 초기에는 Chain-of-Thought와 Zero-shot CoT를 통해 Prompt만으로 단계적 사고를 유도했고 Self-consistency와 Tree of Thoughts를 통해 여러 Reasoning Path를 탐색하는 방향으로 발전했으며, 이러한 Reasoning이 External Action과 결합되면서 ReAct의 Thought–Action–Observation Loop가 등장하고 더 복잡한 Long-horizon Task에서는 HuggingGPT나 Plan-and-Act처럼 Goal을 Subtask로 분해하고 실행·평가·Replanning하는 Planning 구조로 확장되었다. 이후에는 Inference 시 더 많은 Compute를 사용하는 Test-time Scaling과 Training 단계에서 Reasoning 자체를 강화하는 Reasoning-specific Model이 발전했지만 긴 Reasoning은 Token·Latency·Compute Cost를 증가시키므로, 쉬운 문제는 짧게 생각하고 어려운 문제는 깊게 생각하도록 Control Token·Context Compression·Query Router 등을 이용해 Reasoning Budget을 동적으로 조절하는 것이 중요한 Agent Engineering 문제다.**

가장 짧게 기억하면 다음과 같다.

```text
Reasoning
= 문제를 단계적으로 생각한다.

CoT
= Reasoning Example을 보여준다.

Zero-shot CoT
= Example 없이
  Step-by-step Reasoning을 유도한다.

Self-consistency
= 여러 Reasoning Path를 만들고
  답을 Voting한다.

Tree of Thoughts
= 여러 Path를 탐색하되
  안 좋은 Path는 중간에 버린다.

ReAct
= 생각 → 행동 → 관찰을 반복한다.

Planning
= Goal까지 필요한
  Action Sequence를 설계한다.

Plan-and-Act
= 먼저 전체 Plan을 만들고
  실행한 뒤 필요하면 다시 계획한다.

Test-time Compute
= Model을 다시 학습하지 않고
  답할 때 더 많이 생각하게 한다.

Efficient Reasoning
= 쉬운 문제는 짧게,
  어려운 문제는 오래 생각한다.

Agent에서 Reasoning의 역할
= 무엇을 해야 할지 판단한다.

Planning의 역할
= 그 판단을 실제 행동 순서로 바꾼다.
```