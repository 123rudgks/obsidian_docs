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

