[강의 링크](https://www.youtube.com/watch?v=fD8sbY3I1s4)

## 시험 범위에 포함되는 내용
| 시험 토픽                 | 근거                                                                |
| --------------------- | ----------------------------------------------------------------- |
| **RAG**               | External Memory로 RAG 및 Agentic RAG 설명                             |
| **Tool Learning**     | Toolformer, API Documentation 기반 Tool Learning, RL Tool Selection |
| **AI Agent의 Tool 활용** | 언제/어떤/어떻게 Tool을 사용할지 결정                                           |
| **AI Agent 동작**       | Observation/Task → Tool-use Policy → Tool Call                    |
| **다중 에이전트 시스템**       | Multi-Agent RAG, Agent별 Tool/Memory 전문화                           |
# 0. 강의 전체 개요

앞 차시에서는 Multi-Agent System을 다음과 같이 봤다.

```text
Single Agent의 한계
↓
Multiple Agents
↓
역할 분담
협력
경쟁
조직화
↓
Collective Intelligence
```

이번에는 질문을 바꾼다.

> **Agent들이 아무리 잘 조직되어 있어도, LLM 자체가 모르는 정보나 직접 수행할 수 없는 작업은 어떻게 해결할까?**

답은 두 가지다.

```text
Memory
+
Tool
```

이다.

전체 흐름은 다음과 같다.

```text
LLM
↓
Parameter 안의 Knowledge만 사용

하지만
├─ 최신 정보 부족
├─ Private Data 접근 불가
├─ 모든 계산을 정확히 수행하지 못함
├─ 모든 Software를 직접 실행할 수 없음
└─ 경험을 지속적으로 기억하기 어려움
↓
External Memory + Tool


[Memory]
RAG
↓
External Knowledge Retrieval
↓
Grounded Response
↓
Agentic RAG
↓
Search → Evaluate → Retry
↓
Long-term / Short-term Memory
↓
Multi-Agent RAG


[Tool]
Calculator
Search
Database
Code Executor
Slack
Notion
API
...
↓
언제 Tool을 쓸까?
어떤 Tool을 쓸까?
어떤 Argument로 호출할까?
↓
Tool-use Policy
↓
Toolformer
API Documentation 기반 학습
RL-based Tool Selection


하지만
Tool마다 API 규격이 다름
↓
Integration Complexity
↓
MCP


더 나아가
Agent ↔ Agent Communication
↓
A2A-type Protocol


Physical AI
↓
Robot뿐 아니라
Agent Communication
Payment
Simulation
Digital Twin 등
전체 Ecosystem 필요
```

---

# 1. 강의 시작 Q&A — Digital Twin과 Physical AI

강의는 본론 전에 Digital Twin 관련 질문을 다룬다.

질문:

```text
Digital Twin이 범용화되면
Physical AI 발전도 빨라질까?
```

강사는 **빨라질 가능성이 높다**고 설명한다.

그 이유를 크게 두 가지로 제시한다.

---

# 2. Physical AI가 해결해야 할 문제 ① Data

Physical AI Model을 Training하려면 Robot Experience Data가 필요하다.

하지만 실제 Robot을 사람이 하나하나 조종해서 Data를 모으는 것은

```text
느림

비쌈

위험

Scale하기 어려움
```

이라는 문제가 있다.

---

# 3. Human Video → Synthetic Physical Data

강의에서는 미래 방향으로

```text
Human Behavior Video
↓
Virtual Environment
↓
High-quality Synthetic Data
↓
Physical AI Training
```

같은 구조를 언급한다.

즉 실제 Robot Teleoperation만으로 Data를 모으지 않고 인간의 행동 Video를 Virtual World에서 확장·증강하는 기술이 중요하다는 관점이다.

---

# 4. Physical AI가 해결해야 할 문제 ② Evaluation

LLM은 Software 안에서 동작한다.

따라서

```text
Math Benchmark

Code Benchmark

QA Dataset
```

등으로 비교적 쉽게 평가할 수 있다.

---

# 5. Physical AI는 실제 Action을 평가해야 한다

Robot Model은 결국 현실에서 행동한다.

따라서

```text
물체를 제대로 잡았는가?

사람을 다치게 하지 않았는가?

기계를 깨뜨리지 않았는가?
```

등을 평가해야 한다.

실제 Robot을 매번 현장에 배치해 평가하면 너무 느리고 위험하다.

---

# 6. Virtual Evaluation

그래서

```text
Trained Physical AI
↓
Virtual World / Simulator
↓
Action 실행
↓
Performance Evaluation
```

이 중요하다.

---

# 7. Digital Twin

강의에서는

```text
Synthetic Data Generation
+
Virtual Evaluation
```

두 문제를 해결하는 중요한 기반으로 정교한 **Digital Twin**을 설명한다.

즉 Physical AI 발전에서 Digital Twin은

```text
Training Data Generator

Evaluation Environment
```

두 역할을 할 수 있다.

---

# 8. 다시 본론 — 왜 Tool이 필요한가?

Multi-Agent를 사용한다고 해도 각 Agent의 Backbone은 결국 LLM일 수 있다.

LLM은 아무리 강력해도 Training Data에 크게 의존한다.

---

# 9. LLM Parameter Knowledge의 시간적 한계

Pre-training은 실시간으로 이루어지지 않는다.

따라서

```text
Training Cutoff 이후 정보
```

를 Parameter에서 직접 알 수 없다.

예:

```text
오늘 날씨

현재 대통령

최근 News

최신 Product
```

등이다.

---

# 10. 예전 해결법과 현재 해결법

과거의 직관:

```text
더 최신 Data를 모으자.
↓
Model을 다시 Training하자.
```

하지만 이 방법에는 한계가 있다.

```text
Training이 너무 느림
Cost가 큼
실시간 Update 불가능
```

하다.

---

# 11. 현재의 방향

```text
LLM이 모든 Knowledge를
Parameter에 가지고 있을 필요가 없다.
```

대신 필요할 때

```text
Search

Database

API
```

를 사용하게 한다.

---

# 12. Web Search 예

User:

```text
오늘 날씨가 어때?
```

Agent:

```text
Internal Knowledge로 답하면 안 됨
↓
Web / Weather Tool 필요
↓
실시간 정보 Retrieval
↓
Response
```

한다.

---

# 13. Tool은 LLM의 구조적 한계를 보완한다

강의의 핵심 관점은

> **LLM 자체를 완벽하게 만들기보다, 자신이 잘하지 못하는 일을 적절한 Tool에 위임할 수 있는 Agent를 만드는 것이 더 현실적이다.**

이다.

---

# 14. Tool의 종류

Tool은 매우 다양하다.

예:

```text
Calculator

Python Executor

Database

Search Engine

Notion

Slack

External API

Specialized Model
```

등이다.

---

# 15. Agent의 Paradigm Shift

전통 LLM:

```text
Input
↓
Stored Parameters
↓
Output
```

Agent:

```text
Input / Environment
↓
LLM
↓
External Information 수집
Tool 사용
Environment Interaction
↓
Action
```

이다.

즉 강의에서는 AI가

```text
Passive Model
↓
Active Agent
```

로 전환되는 것으로 설명한다.

---

# 16. Tool 사용은 LLM의 자연스러운 진화

LLM 자체가 아무리 똑똑해도

```text
모든 계산

모든 최신 Knowledge

모든 Software Operation
```

을 Model 안에서 직접 처리할 필요는 없다.

오히려

```text
무엇을 직접 할지

무엇을 Tool에 맡길지
```

판단하는 것이 Agent Intelligence의 중요한 부분이 된다.

---

# 17. AI Agent를 바라보는 세 가지 질문

강의에서는 Agent의 지능을 세 가지 축으로 다시 정리한다.

```text
1. 기억과 지식

2. 행동과 능동성

3. 학습과 적응
```

---

# 18. ① 기억과 지식

질문:

> **Agent는 최신 정보와 Private Knowledge를 어떻게 얻을 것인가?**

방법 A:

```text
Model Parameter Update
```

방법 B:

```text
External Memory / Search / RAG
```

이다.

---

# 19. ② 행동과 능동성

질문:

> **Agent는 실제 Task를 어떻게 수행할 것인가?**

예를 들어 복잡한 계산이 필요하다.

방법 A:

```text
수학을 매우 잘하는 LLM을 만든다.
```

방법 B:

```text
Calculator를
언제 호출해야 하는지 아는 LLM을 만든다.
```

이다.

---

# 20. ③ 학습과 적응

질문:

> **Agent는 Experience를 이용해 어떻게 더 좋아질 것인가?**

```text
Memory

Tool-use Experience

Success / Failure

RL
```

등을 이용할 수 있다.

---

# 21. Memory가 필요한 이유

기본 LLM의 Memory는 크게 보면 Parameter 안에 들어 있는 **Internal Knowledge**다.

하지만 이 Memory에는 세 가지 대표적 한계가 있다.

```text
최신 정보 부족

잘못된 정보 가능

Private / Enterprise Data 없음
```

---

# 22. Private Knowledge 문제

예:

```text
내 개인 Document

회사 Internal Data

Private Medical Record

Company Policy
```

등은 Public Pre-training Data에 없을 수 있다.

따라서 Base LLM만으로는 활용할 수 없다.

---

# 23. 그래서 RAG

가장 대표적인 External Memory 방법이

**Retrieval-Augmented Generation(RAG)** 이다.

```text
Query
↓
External Search
↓
Relevant Information
↓
LLM Context
↓
Response
```

한다.

---

# 24. RAG의 핵심

강의 표현을 구조화하면

```text
Search
+
Augment
+
Generate
```

이다.

즉 답변을 생성하기 전에 외부 Evidence를 가져온다.

---

# 25. 의료 분야 예

과거 General LLM은 Medical Question에서

```text
잘못된 약 이름

근거 없는 정보
```

등을 생성할 수 있었다.

---

# 26. External Medical Literature

의료 Paper/Database를 Search해서 관련 Evidence를 가져오고

```text
Question
↓
Relevant Medical Papers
↓
LLM
↓
Evidence-based Answer
```

를 만든다.

---

# 27. RAG가 제공하는 것

단순 Knowledge 보완 외에도

```text
Evidence

Citation

Grounding

Hallucination 감소 가능성
```

을 제공한다.

단, Retrieval 자체가 틀리면 결과도 잘못될 수 있다는 점은 뒤에서 다시 문제가 된다.

---

# 28. 기본 RAG의 세 단계

강의에서는 일반적인 RAG Pipeline을 대략 세 모듈로 설명한다.

```text
1. Retrieval

2. Augmentation

3. Generation
```

---

# 29. Retrieval

```text
Query
↓
Vector DB / Search
↓
Relevant Documents
```

를 가져온다.

---

# 30. Augmentation

검색된 내용을 Model Input에 어떻게 넣을지 결정한다.

```text
User Query
+
Retrieved Context
↓
Prompt
```

이다.

---

# 31. Generation

```text
Augmented Prompt
↓
LLM
↓
Grounded Response
```

한다.

---

# 32. RAG의 각 단계 자체가 연구 주제

예를 들어 Retrieval만 보더라도

```text
어떤 Vector DB를 쓸까?

어떤 Embedding Model을 쓸까?

Embedding Model 하나인가 여러 개인가?

Document를 어떤 Chunk로 자를까?

Context를 얼마나 유지할까?
```

등이 모두 별도의 연구/Engineering 문제다.

---

# 33. Naive RAG의 문제

기본적인 RAG는 흔히

```text
Query
↓
한 번 Search
↓
Top-K
↓
Answer
```

으로 끝난다.

---

# 34. 문제 ① Retrieval Quality를 보장할 수 없다

잘못된 Medical Paper를 가져왔다고 하자.

```text
Wrong Retrieval
↓
Wrong Evidence
↓
LLM
↓
Wrong Answer
```

가 된다.

즉

```text
RAG
≠
자동 Accuracy 보장
```

이다.

---

# 35. 문제 ② Context를 지속적으로 기억하지 못한다

복잡한 문제는 여러 단계의 Search가 필요하다.

하지만 Naive RAG는 한 번 Search한 결과만 이용하기 때문에

```text
Multi-step Reasoning

Complex Research

Iterative Search
```

에 약할 수 있다.

---

# 36. 그래서 Agentic RAG

RAG Pipeline 자체에 Agent Capability를 넣는다.

```text
Query
↓
Search
↓
Result Evaluation
↓
충분한가?
├─ Yes → Answer
└─ No
    ↓
    Query 수정
    ↓
    다시 Search
```

한다.

---

# 37. Agentic RAG의 핵심

기본 RAG:

```text
Search Flow가 고정
```

Agentic RAG:

```text
Search Flow를
Agent가 능동적으로 결정
```

한다.

---

# 38. Agentic RAG가 결정하는 것

```text
검색해야 하는가?

어떤 Search Tool을 쓸까?

검색 결과가 좋은가?

다시 검색할까?

Query를 바꿀까?

다른 Database도 볼까?
```

등이다.

---

# 39. Self-evaluation

검색 결과를 받은 뒤 Agent가 스스로 평가한다.

```text
Retrieved Evidence
↓
이 Evidence가 Query를
실제로 해결하는가?
```

를 본다.

부족하면 다시 Retrieval한다.

---

# 40. Retrieval System도 Agent가 된다

따라서 RAG는 단순한 Tool에서

```text
Active Knowledge Acquisition System
```

으로 발전할 수 있다.

즉 Agent가 **지식을 능동적으로 수집**한다.

---

# 41. Agent System의 경쟁력은 Model만이 아니다

강의에서는 현재 AI Company의 차이를 단순히

```text
누가 Base Model을 더 잘 Training하는가?
```

만으로 설명하기 어렵다고 강조한다.

중요해지는 것은

```text
Model을 둘러싼
Agent Architecture
```

다.

---

# 42. Agent Harness의 경험 격차

복잡한 Agent System은

```text
몇 번 실패하는가?

어떤 Routing이 좋은가?

어떤 Retry가 필요한가?

어떤 Tool이 안정적인가?
```

같은 Experience가 많이 축적되어야 한다.

이 경험은 단순 Model Weight보다 Copy하기 어려운 경쟁력이 될 수 있다는 것이 강의의 관점이다.

---

# 43. Agentic RAG + Memory

Agentic RAG에는 자연스럽게 **Memory Module**을 추가할 수 있다.

예:

```text
자주 들어오는 Query

과거 Search Result

유용했던 Evidence

실패했던 Search Strategy
```

등을 기억한다.

---

# 44. 왜 모든 Memory를 저장하면 안 되는가?

모든 정보를 영원히 저장하면

```text
Memory Size ↑

Noise ↑

Retrieval Difficulty ↑
```

가 된다.

따라서

```text
무엇을 기억할까?

무엇을 잊을까?
```

가 중요하다.

---

# 45. Short-term vs Long-term Memory

강의에서는 가장 단순한 Memory Architecture로

```text
Short-term Memory

Long-term Memory
```

를 구분하는 방식을 언급한다.

---

# 46. Short-term Memory

최근 Interaction이나 일시적으로 필요한 Context를 유지한다.

```text
Recent Query

Current Task State

Recent Retrieval
```

등이다.

---

# 47. Long-term Memory

장기적으로 반복해서 가치가 있는 정보를 저장한다.

```text
자주 반복되는 Query

Important User Preference

Successful Search Strategy
```

등이다.

---

# 48. Forgetting도 Memory의 일부

좋은 Memory System은

```text
Remember
```

만 하는 System이 아니다.

```text
Forget
```

도 할 수 있어야 한다.

시간, 중요도, 사용 빈도 등에 따라 Memory를 제거하거나 보존할 수 있다.

---

# 49. Planning + RAG

Agentic RAG에서는 Retrieval 자체에도 Planning이 필요하다.

예:

```text
"나보다 세 살 많은 사람의
식습관 정보를 찾아 줘."
```

라고 하자.

---

# 50. 단순 Vector Search로 바로 해결할 수 없는 Query

먼저

```text
User Age + 3
```

계산이 필요하다.

따라서 Agent는

```text
1. Calculator 호출

2. Target Age 계산

3. 그 Age를 이용해
   Vector DB/Search 수행

4. Evidence 반환
```

처럼 Plan을 만들 수 있다.

---

# 51. Tool Combination

즉 Agentic RAG는 Search만 사용하는 것이 아니다.

```text
Calculator
↓
Database
↓
Web Search
↓
Reranking
```

처럼 여러 Tool을 조합할 수 있다.

---

# 52. Planning 실패 시 Self-correction

Plan이 잘못되면

```text
Plan
↓
Tool Execution
↓
Bad Result
↓
Self-evaluation
↓
Plan 수정
↓
다시 Tool
```

할 수 있다.

---

# 53. Agentic RAG도 Agent의 단점을 그대로 가진다

장점:

```text
Search Quality ↑

Complex Query 대응

Multi-step Reasoning
```

하지만 단점:

```text
Debugging 어려움

Latency 증가

Token Cost 증가

Tool Loop 가능

Architecture 복잡
```

등이다.

---

# 54. Multi-Agent RAG

RAG 자체도 Multi-Agent로 만들 수 있다.

```text
Top-level Agent
↓
Task Decomposition
↓
Sub Agents
```

한다.

---

# 55. Hierarchical Retrieval Agent

예:

```text
Coordinator Agent
├─ Computer News Agent
├─ General Web Search Agent
└─ Collaboration Tool Agent
```

처럼 각 Agent가 서로 다른 Retrieval Source나 Tool에 전문화될 수 있다.

---

# 56. Parallel Search

각 Sub-agent가 동시에 Search한다.

```text
Agent A → Source A

Agent B → Source B

Agent C → Source C
```

그 결과를 Top-level Agent가 합친다.

---

# 57. Multi-Agent RAG의 기대 효과

```text
Source Diversity ↑

Parallelism ↑

Domain Specialization ↑

Retrieval Accuracy ↑
```

를 기대할 수 있다.

---

# 58. 이제 Tool Use의 핵심 문제

Agent가 Tool을 최적으로 사용하려면 세 가지를 결정해야 한다.

```text
1. 언제 Tool을 호출할 것인가?

2. 어떤 Tool을 사용할 것인가?

3. Tool을 어떻게 정확히 호출할 것인가?
```

---

# 59. 첫 번째 문제 — When

```text
지금 LLM Knowledge만으로
답해도 되는가?

Tool이 필요한가?
```

를 판단한다.

---

# 60. 두 번째 문제 — Which

Tool이 여러 개라면

```text
Calculator?

Search?

Database?

Python?

Translator?
```

중 무엇을 사용할지 선택한다.

---

# 61. 세 번째 문제 — How

Tool을 선택했다고 끝나지 않는다.

```text
Function Name

Parameter

Argument Type

Argument Value

API Format
```

를 정확히 맞춰야 한다.

---

# 62. Tool-use Policy

이 세 판단을 모두 합치면

```text
Observation / Task
↓
Tool-use Policy
↓
When
Which
How
↓
Tool Call
```

이 된다.

강의에서는 이러한 Policy가 RL 등으로 학습될 수 있다고 설명한다.

---

# 63. Toolformer

강의에서는 Tool Use 연구의 초기 대표 사례로 **Toolformer**를 소개한다.

핵심 질문:

> **Human이 일일이 Tool 사용법을 Labeling하지 않아도 LLM이 Tool을 사용할 시점을 스스로 학습할 수 있는가?**

---

# 64. Toolformer의 핵심 Idea

LLM이 Text를 생성하면서

```text
이 부분은
내가 직접 답하는 것보다
Tool을 사용하면 더 낫지 않을까?
```

를 판단한다.

---

# 65. Calculator 예

Model이 먼저 문제를 직접 풀어본다.

그다음

```text
이 계산 부분에서
Calculator를 사용했으면
더 정확했을까?
```

를 평가한다.

---

# 66. Tool Call을 넣어 본다

```text
Original Generation
vs
Generation + Calculator Result
```

를 비교한다.

Tool 사용 후 결과가 더 좋아지면

```text
이 위치에서는
Calculator 호출이 유용하다.
```

는 Training Sample을 만들 수 있다.

---

# 67. Self-supervised Tool-use Data

즉 사람이

```text
"여기서 Calculator를 호출해."
```

라고 모두 Labeling하지 않아도

```text
LLM
+
Actual Tool
+
Performance Comparison
```

으로 Tool-use Training Data를 만들 수 있다.

---

# 68. 언제 호출할지 학습

Tool은 항상 시작 부분에서 사용하는 것이 아니다.

어떤 Task는

```text
Beginning
```

에 Search가 필요할 수 있고,

어떤 Task는

```text
Middle
```

에 Calculator가 필요할 수 있고,

어떤 Task는

```text
End
```

에서 Validation Tool이 필요할 수 있다.

Model은 이런 Pattern을 학습할 수 있다.

---

# 69. Toolformer의 의미

과거:

```text
Model을 더 똑똑하게 만들기 위해
모든 Knowledge를 Parameter에 넣는다.
```

Tool-use Agent:

```text
필요한 Knowledge/Capability가 있으면
적절한 Tool을 호출한다.
```

이다.

---

# 70. Knowledge를 소유하는 능력보다 Access하는 능력

강의의 중요한 관점이다.

```text
모든 정보를 기억
```

하는 것보다

```text
필요한 정보를
언제 어디서 가져올지 앎
```

이 더 중요한 Intelligence가 될 수 있다.

---

# 71. 사람의 개입을 줄이는 것이 중요하다

새로운 Capability를 추가할 때마다 사람이 수천 개 Training Example을 만들어야 하면 Scale하기 어렵다.

따라서 Agent/Tool Learning에서도

```text
Human Intervention ↓
Automatic Data Generation ↑
```

가 중요하다.

---

# 72. 다행히 API Documentation은 이미 존재한다

많은 Software는 이미 사람을 위한 API Documentation을 가지고 있다.

예:

```text
AWS

Search API

Database API

Cloud Services
```

등이다.

---

# 73. API Documentation을 Training Data로 사용

```text
Existing API Docs
↓
Function Description

Arguments

Usage Examples
↓
Tool-use Dataset
↓
LLM Training
```

을 할 수 있다.

---

# 74. 대규모 Tool Learning

Tool을 몇 개 직접 Labeling하는 대신 Web에 존재하는 API Documentation을 대규모로 수집해

```text
Human Intervention을 줄이면서

많은 Tool을 학습
```

하려는 방향이 등장한다.

---

# 75. Tool Learning의 목표

```text
API 하나를 암기
```

가 아니라

```text
Documentation을 읽고
새 API를 이해해서 사용하는 능력
```

을 학습하는 것이 중요하다.

---

# 76. Tool Selection과 RL

같은 역할을 수행할 수 있는 Tool이 여러 개일 수 있다.

예:

```text
Search Tool A

Search Tool B

Search Tool C
```

가 있다.

---

# 77. 어떤 Tool이 더 좋은가?

Task마다 다를 수 있다.

```text
Latency

Accuracy

Cost

Coverage
```

가 다르기 때문이다.

---

# 78. Reward를 이용한 Tool Selection

Agent가 Tool을 사용하고

```text
정답성

속도

Cost

Task Success
```

등을 Reward로 받는다.

---

# 79. Reinforcement Learning

```text
State / Task
↓
Tool A 선택
↓
Reward

State / Task
↓
Tool B 선택
↓
Reward
```

를 반복하면

```text
이 상황에서는
A가 더 좋다.
```

는 Policy를 학습할 수 있다.

---

# 80. Tool-use 연구의 발전선

강의 내용을 흐름으로 정리하면

```text
1.
Tool을 쓰면
LLM 성능이 좋아진다.

↓ Toolformer 등

2.
언제 Tool을 써야 할지
학습한다.

↓

3.
많은 API Documentation을 이용해
Tool 사용을 Scale한다.

↓

4.
여러 Tool 중
무엇을 사용할지 RL로 선택한다.

↓

5.
Tool Interface 자체를
표준화한다.
```

이다.

---

# 81. 그런데 Tool Integration 문제가 생긴다

세상에는 너무 많은 Tool이 존재한다.

각 회사가 만든 API는

```text
Function Name

Authentication

Arguments

Response Format
```

이 모두 다르다.

---

# 82. Tool A와 Tool B

동일한 기능이라도

```text
Tool A API

Tool B API
```

호출 규칙이 다를 수 있다.

Model이 A 사용법을 배웠다고 B를 바로 사용할 수 있는 것은 아니다.

---

# 83. Tool Version Update 문제

또 Tool API가 Update되면

```text
기존 Training / Integration
↓
Break
```

할 수 있다.

따라서 Tool 수가 늘어날수록 Maintenance Complexity가 매우 커진다.

---

# 84. 모든 Tool을 Training하는 것은 비효율적

```text
Tool 10개
→ 가능

Tool 100개
→ 복잡

Tool 1,000,000개
→ 사실상 불가능
```

하다.

그래서 공통 Interface가 필요해진다.

---

# 85. MCP

강의에서는 이를 해결하는 대표 방향으로 **MCP(Model Context Protocol)** 를 다시 소개한다.

핵심:

> **서로 다른 LLM과 Tool이 공통 Protocol을 사용하도록 하여 Tool Integration을 표준화한다.**

---

# 86. MCP 이전

```text
LLM A ↔ Tool X Custom Connector

LLM A ↔ Tool Y Custom Connector

LLM B ↔ Tool X Custom Connector
...
```

가 필요할 수 있다.

---

# 87. MCP 이후의 Idea

```text
LLM / Agent
↓
MCP
↓
MCP-compatible Tools
```

형태로 통일한다.

Tool과 Model이 모두 동일한 Protocol을 이해하면 개별 Pair마다 새 Integration을 만들 필요가 줄어든다.

---

# 88. MCP의 목적

```text
Tool Reusability ↑

Integration Efficiency ↑

Compatibility ↑

Maintenance Cost ↓
```

이다.

---

# 89. Model이나 Tool이 Update되어도

공통 Protocol 자체가 유지된다면

```text
Model Version Update

Tool Version Update
```

가 발생해도 Connection을 유지하기 쉬워진다.

---

# 90. MCP를 세부 구현보다 Concept으로 이해

강의에서는

```text
Server

Client

Transport
```

같은 MCP 구현 Detail보다

> **Tool이 많아질수록 공통된 Communication 규격이 필요해졌고, 그 문제를 해결하기 위해 MCP가 등장했다.**

라는 발전 배경을 중요하게 본다.

---

# 91. Tool Protocol에서 Agent Protocol로

MCP는 주로

```text
Agent
↔
Tool
```

Communication을 표준화한다.

그런데 Multi-Agent System에서는

```text
Agent
↔
Agent
```

Communication도 중요하다.

---

# 92. Agent-to-Agent Communication

Future System에서는

```text
Main Agent
↓
Sub Agent
↓
Another Agent
↓
Robot Agent
```

처럼 여러 Agent가 Hierarchical/Network 구조를 가질 수 있다.

---

# 93. A2A 방향

강의에서는 Agent 간 Communication을 표준화하려는 **Agent-to-Agent(A2A)** Protocol 방향을 언급한다.

핵심은

```text
Tool 사용 표준화
↓
MCP

Agent 협업 표준화
↓
A2A-type Protocol
```

이다.

---

# 94. 왜 A2A가 중요한가?

Agent마다

```text
Capability

State

Permission

Task

Message Format
```

이 다를 수 있다.

따라서 Agent가 많아지면

```text
누가 누구에게

어떤 형식으로

어떤 Task를

어떤 권한으로
```

전달할지 표준화가 필요해진다.

---

# 95. Physical AI에서는 더 중요하다

강의에서는 Physical AI의 미래에서

```text
Human

Agent

Robot

Sub-agent
```

가 복잡하게 연결될 수 있다고 설명한다.

---

# 96. Robot 자체도 Agent를 가질 수 있다

예:

```text
Factory Orchestrator Agent
↓
Robot-level Agent
↓
Robot Controller
```

처럼 여러 Layer가 존재할 수 있다.

---

# 97. Multi-Agent Physical AI

```text
Central Agent
├─ Human Worker Agent
├─ Robot Agent A
├─ Robot Agent B
└─ Logistics Agent
```

처럼 구성될 수도 있다.

이때 Agent 간 Communication Protocol이 중요해진다.

---

# 98. Agent Ecosystem의 또 다른 문제 — Payment

강의 후반에서는 Agent가 실제 경제 활동까지 수행하려면 **Payment** 문제도 해결해야 한다고 설명한다.

---

# 99. Agent가 구매해야 하는 상황

예:

```text
Smart Refrigerator Agent
↓
Milk 부족 감지
↓
Shopping Service 호출
↓
Milk 주문
```

한다.

---

# 100. 여기서 Payment가 필요

Agent가 실제로 구매하려면

```text
Payment Authorization

Identity

Permission

Financial Regulation
```

등이 필요하다.

---

# 101. Crypto Payment에 대한 강의의 전망

강의에서는 Agent 기반 Automated Payment에서 Cryptocurrency 계열이 활용될 가능성을 하나의 미래 Use Case로 언급한다.

다만 어떤 Cryptocurrency가 사용될지는 별개 문제로 두며, 이는 강사의 전망이다.

---

# 102. 더 중요한 것은 Protocol과 Regulation

핵심 문제는

```text
어떤 Asset을 사용할까?
```

보다

```text
Agent가 어떤 권한으로
Payment할 수 있는가?

어떤 Regulation을 따라야 하는가?
```

다.

---

# 103. Physical AI는 Robot Model만의 문제가 아니다

강의의 마지막 메시지다.

Physical AI가 완성되려면

```text
Robot Skill
```

뿐 아니라

```text
Simulation

Digital Twin

Agent Communication

Tool Protocol

Payment

Security

Organization
```

같은 주변 Ecosystem도 발전해야 한다.

---

# 104. Memory와 Tool을 하나의 구조로 보면

```text
             User / Environment
                    ↓
                  Agent
                    ↓
       ┌────────────┴────────────┐
       ↓                         ↓
     Memory                    Tools
       ↓                         ↓
Past Experience          Search / API /
Documents                Calculator /
User Context             DB / Software
       ↓                         ↓
       └────────────┬────────────┘
                    ↓
               Planning
                    ↓
                 Action
                    ↓
             Self-evaluation
                    ↓
          Memory Update / Retry
```

이다.

---

# 105. Internal Memory vs External Memory

### Internal

```text
Model Parameters
```

장점:

```text
빠르게 사용
```

단점:

```text
최신화 어려움

Private Data 반영 어려움

Training 필요
```

---

### External

```text
RAG DB

Memory Store

Search Engine
```

장점:

```text
Update 쉬움

Private Knowledge 사용 가능

Parameter Update 불필요
```

이다.

---

# 106. Knowledge vs Memory

강의에서는 완전히 엄격하게 분리하지 않지만 구조적으로 다음처럼 이해할 수 있다.

### Knowledge Retrieval

```text
External Documents
↓
RAG
```

### Experience Memory

```text
Past Agent Interactions
↓
Memory
```

둘 다 Model 밖의 Non-parametric Information을 이용한다.

---

# 107. RAG와 Tool의 관계

Search/Retrieval 자체도 하나의 Tool이다.

즉

```text
Tool
├─ RAG
├─ Calculator
├─ Python
├─ API
└─ Software
```

라고 볼 수 있다.

---

# 108. Memory와 Tool의 관계

Memory도 넓은 Agent System 관점에서는 Agent가 사용할 수 있는 External Resource다.

즉 Agent는

```text
Memory에서 과거를 가져오고

Tool로 현재 Environment를 조작
```

한다.

---

# 109. Agentic RAG와 일반 RAG

|구분|Naive RAG|Agentic RAG|
|---|---|---|
|Search 횟수|보통 1회|필요에 따라 반복|
|Query 수정|고정|Agent가 수정|
|Result 평가|제한적|Self-evaluation|
|Tool 종류|Retriever 중심|여러 Tool 가능|
|Planning|거의 없음|있음|
|Complexity|낮음|높음|
|Debugging|쉬운 편|어려움|

---

# 110. Memory의 중요한 문제

```text
무엇을 기억할까?

얼마나 오래 기억할까?

무엇을 잊을까?

어떻게 검색할까?
```

이다.

Memory Store가 커진다고 Agent가 자동으로 더 똑똑해지는 것은 아니다.

---

# 111. Tool의 중요한 문제

Tool 개수가 많아진다고 Agent가 자동으로 더 유능해지는 것도 아니다.

핵심은

```text
When

Which

How
```

이다.

---

# 112. Tool Use의 세 가지 질문

```text
When
= 언제 Tool을 호출할까?

Which
= 어떤 Tool을 선택할까?

How
= 어떻게 올바른 API Call을 만들까?
```

이 세 가지가 Tool Agent의 핵심이다.

---

# 113. Toolformer가 해결하려던 문제

주로

```text
When
```

과

```text
Tool 사용 자체의 유용성
```

을 자동으로 학습하는 방향이다.

---

# 114. API Documentation 기반 Tool Learning

주로

```text
How
```

를 Scale한다.

즉 수많은 Tool의 사용법을 사람이 하나씩 Labeling하지 않고 Existing Documentation을 활용한다.

---

# 115. RL-based Tool Selection

주로

```text
Which
```

를 학습한다.

여러 Tool 가운데 현재 Task에서 Reward가 높은 Tool을 선택한다.

---

# 116. MCP가 해결하는 문제

MCP는 Tool Intelligence 자체를 학습하는 Algorithm이 아니다.

해결하려는 문제는

```text
Tool마다 다른 Integration 규격
```

이다.

즉

```text
Tool Learning
= Agent가 Tool을 잘 사용하는 능력

MCP
= Tool을 Agent와
  공통 방식으로 연결하는 규격
```

이다.

---

# 117. A2A와 MCP 구분

```text
MCP
= Agent ↔ Tool

A2A
= Agent ↔ Agent
```

로 강의 흐름을 기억하면 된다.

---

# 118. Multi-Agent에서 Tool Specialization

각 Agent에 서로 다른 Tool을 줄 수 있다.

```text
Research Agent
→ Web / Paper Search

Coding Agent
→ Python / IDE

Data Agent
→ SQL / DB

Collaboration Agent
→ Slack / Notion
```

이다.

---

# 119. 왜 Tool을 Agent별로 분리할까?

모든 Agent에 모든 Tool을 주면

```text
Tool Selection Complexity ↑

Security Risk ↑

Prompt Complexity ↑
```

할 수 있다.

Role별 필요한 Tool만 주면 전문화가 가능하다.

---

# 120. Hierarchical Tool Permission

```text
Top Agent
↓
Sub Agent A
↓
Allowed Tools A

Sub Agent B
↓
Allowed Tools B
```

처럼 권한을 다르게 할 수도 있다.

앞 Multi-Agent 강의의 Organization/Permission 문제와 연결된다.

---

# 121. Memory도 Agent별로 분리할 수 있다

```text
Shared Memory

Private Agent Memory

Role-specific Memory
```

등으로 설계할 수 있다.

---

# 122. Shared Memory

모든 Agent가 공통으로 접근한다.

장점:

```text
Knowledge Sharing
```

단점:

```text
Noise

Wrong Information Propagation

Privacy
```

문제가 있다.

---

# 123. Private Memory

특정 Agent만 사용하는 Memory다.

예:

```text
Medical Agent
→ Medical Case Memory

Coding Agent
→ Code Debugging Memory
```

처럼 Role Specialization에 사용할 수 있다.

---

# 124. Memory와 Hallucination

좋은 External Evidence를 Memory/RAG에서 가져오면 Hallucination을 줄일 수 있다.

하지만

```text
Memory 자체가 틀림

Wrong Retrieval
```

이면 오히려 Hallucination을 강화할 수도 있다.

즉 Memory도 Quality Control이 중요하다.

---

# 125. Agentic RAG의 Self-evaluation

강의가 특히 강조한 흐름은

```text
Retrieve
↓
Evaluate
↓
Bad?
↓
Retrieve Again
```

이다.

즉 Agent가 Retrieval 결과 자체를 검증한다.

---

# 126. Tool Use의 Self-evaluation

마찬가지로

```text
LLM 직접 계산
vs
Calculator 사용
```

결과를 비교하여

```text
Tool이 실제로 도움이 되었는가?
```

를 판단할 수 있다.

---

# 127. 결국 Agent는 Meta-decision을 한다

단순 Problem Solving을 넘어

```text
내가 직접 할까?

Memory를 볼까?

Search할까?

Calculator를 쓸까?

다른 Agent에게 맡길까?
```

를 판단한다.

이 **Meta-level Decision**이 Agent의 핵심 Intelligence다.

---

# 128. Agent의 Intelligence를 다시 세 축으로 정리

### Memory / Knowledge

```text
무엇을 알고 있고
무엇을 가져올 수 있는가?
```

### Action / Tool

```text
무엇을 실제로 할 수 있는가?
```

### Learning / Adaptation

```text
경험으로 어떻게
더 좋아질 수 있는가?
```

이다.

---

# 129. 기존 LLM과 Agent 비교

|구분|Traditional LLM|Agent|
|---|---|---|
|Knowledge|Parameter 중심|Parameter + External Memory|
|최신 정보|제한적|Search 가능|
|Private Data|알기 어려움|RAG/DB 접근|
|행동|Text Generation|Tool/API 실행|
|Experience|지속 기억 제한|Memory 가능|
|Planning|단일 Response 중심|Multi-step|
|Adaptation|Training 필요|Memory/RL/Tool 경험 활용|

---

# 130. Tool-rich Agent와 Model Scaling

과거:

```text
더 큰 Model
↓
더 많은 Capability
```

현재:

```text
Reasonable Base Model
+
Strong Tool Ecosystem
+
Good Routing
+
Memory
↓
Strong Agent
```

이라는 접근도 중요해진다.

---

# 131. Model을 모든 분야의 Expert로 만들 필요가 없다

예:

```text
복잡한 산술 계산
```

을 LLM Weight 안에서 완벽하게 학습시키는 대신

```text
Calculator를
정확하게 사용하는 능력
```

을 학습할 수 있다.

---

# 132. 인간 Intelligence와 비슷한 관점

사람도

```text
모든 전화번호를 암기

모든 수식을 직접 계산

모든 최신 News 기억
```

하지 않는다.

대신

```text
Search Engine

Calculator

Book

Computer
```

를 사용한다.

Agent도 같은 방향으로 발전한다.

---

# 133. Agent 시대의 중요한 능력

강의의 내용을 한 문장으로 압축하면

> **무엇을 알고 있는가뿐 아니라, 모를 때 어디에서 무엇을 찾아 어떻게 사용할지를 아는 것이 중요하다.**

이다.

---

# 134. 자주 헷갈리는 점 ① — Memory는 LLM Parameter인가?

이번 강의에서 주요하게 다루는 Memory는 Model Parameter 밖에 있는 External Memory다.

예:

```text
RAG DB

Past Experience Store
```

등이다.

---

# 135. 자주 헷갈리는 점 ② — 최신 Knowledge를 넣으려면 Fine-tuning해야 하는가?

반드시 아니다.

Search/RAG를 이용하면 Model Parameter를 바꾸지 않고 최신 정보를 사용할 수 있다.

---

# 136. 자주 헷갈리는 점 ③ — RAG를 사용하면 답은 항상 정확한가?

아니다.

잘못된 Document가 검색되면 오히려 잘못된 Evidence에 근거한 답을 만들 수 있다.

---

# 137. 자주 헷갈리는 점 ④ — Agentic RAG는 RAG Model을 더 크게 만든 것인가?

아니다.

검색 과정 자체에 Agent의

```text
Planning

Evaluation

Retry
```

Capability를 추가한 것이다.

---

# 138. 자주 헷갈리는 점 ⑤ — Agentic RAG에서 항상 여러 번 검색하는가?

아니다.

필요하면 반복하는 것이 핵심이다.

한 번의 Retrieval로 충분하면 바로 답할 수도 있다.

---

# 139. 자주 헷갈리는 점 ⑥ — Memory는 모든 것을 저장하는 것이 좋은가?

아니다.

망각과 Selection이 필요하다.

---

# 140. 자주 헷갈리는 점 ⑦ — Short-term / Long-term Memory는 LSTM의 Cell State 같은 것인가?

이 강의에서는 Agent System의 External Memory Architecture 개념으로 다룬다.

RNN/LSTM 내부 State와는 다른 층위다.

---

# 141. 자주 헷갈리는 점 ⑧ — Tool은 Search만 의미하는가?

아니다.

```text
Calculator

Python

DB

Notion

Slack

Search

External API
```

등 매우 넓다.

---

# 142. 자주 헷갈리는 점 ⑨ — Tool이 많으면 무조건 좋은가?

아니다.

Tool Selection과 API 호출 Complexity가 증가한다.

---

# 143. 자주 헷갈리는 점 ⑩ — Tool Call에서 가장 중요한 것은 Function Name만 고르는 것인가?

아니다.

```text
언제

어떤 Tool

어떤 Argument
```

가 모두 맞아야 한다.

---

# 144. 자주 헷갈리는 점 ⑪ — Toolformer는 새로운 Tool을 만든 연구인가?

강의의 핵심은 Tool 자체를 만드는 것이 아니라 **LLM이 외부 Tool을 언제 호출하면 유용한지 스스로 학습**하는 방향이다.

---

# 145. 자주 헷갈리는 점 ⑫ — Tool Use Training Data는 반드시 Human이 직접 만들어야 하는가?

아니다.

Toolformer 계열 Idea에서는 Model과 Tool 실행 결과를 이용해 Tool-use Sample을 자동으로 생성·Filtering할 수 있다.

---

# 146. 자주 헷갈리는 점 ⑬ — API Documentation은 사람에게만 필요한가?

아니다.

LLM이 API Usage를 배우거나 Runtime에 Tool Description을 이해하는 데도 활용할 수 있다.

---

# 147. 자주 헷갈리는 점 ⑭ — RL은 Tool 자체를 실행하는 Algorithm인가?

아니다.

Reward를 통해

```text
어떤 Tool을 선택하면
좋은 결과를 얻는가?
```

라는 Policy를 학습하는 데 사용할 수 있다.

---

# 148. 자주 헷갈리는 점 ⑮ — MCP는 Tool Selection Algorithm인가?

아니다.

Tool Integration을 표준화하는 Protocol이다.

---

# 149. 자주 헷갈리는 점 ⑯ — MCP를 사용하면 Agent가 자동으로 적절한 Tool을 선택하는가?

아니다.

Agent의 Tool Selection Capability는 별도로 필요하다.

MCP는 Connection 형식을 표준화한다.

---

# 150. 자주 헷갈리는 점 ⑰ — MCP와 A2A는 같은가?

아니다.

강의 흐름에서는

```text
MCP
→ Agent–Tool

A2A
→ Agent–Agent
```

로 구분한다.

---

# 151. 자주 헷갈리는 점 ⑱ — Agent Memory와 RAG는 완전히 다른가?

사용 목적은 다를 수 있지만 둘 다

```text
External Information
↓
Retrieval
↓
Context
```

라는 구조를 공유한다.

---

# 152. 자주 헷갈리는 점 ⑲ — Physical AI 발전은 Robot Model만 좋아지면 되는가?

강의에서는 아니라고 설명한다.

Digital Twin, Simulation, Agent Communication, Payment 같은 주변 Infrastructure도 필요하다.

---

# 153. 자주 헷갈리는 점 ⑳ — Agent가 Payment를 한다는 것은 현재 일반적인 완성 기술인가?

강의에서는 미래 Agent Ecosystem에서 해결해야 할 문제와 가능성으로 제시한다.

특히 Cryptocurrency 활용 부분은 강사의 전망으로 이해해야 한다.

---

# 154. 앞 차시와 연결

## 1차시 — Multi-Agent System

```text
여러 Agent를
어떻게 조직할까?
↓
Agent
Environment
Interaction
Organization
```

---

## 2차시 — Memory & Tool

이번에는 각 Agent에게

```text
무엇을 기억하게 할까?

무슨 Tool을 줄까?

어떻게 Tool을 선택하게 할까?
```

를 다룬다.

---

# 155. Multi-Agent + Memory + Tool

```text
                   Coordinator
                        ↓
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
    Research Agent   Data Agent   Coding Agent
          ↓             ↓             ↓
       Web Tool        DB Tool       Python
          ↓             ↓             ↓
          └─────────────┼─────────────┘
                        ↓
                    Shared Memory
                        ↓
                    Final Result
```

이런 구조를 생각할 수 있다.

---

# 156. Agent 모델 과정의 현재 발전선

```text
Single LLM
↓
Single Agent
↓
Multi-Agent
↓
Role / Organization
↓
Memory
↓
External Knowledge
↓
Tools
↓
External Action
↓
Agentic RAG
↓
Multi-Agent RAG
↓
Tool Learning
↓
Tool Routing
↓
MCP
↓
Agent-to-Agent Protocol
↓
Large Agent Ecosystem
```

---

# 157. 이번 강의에서 반드시 기억할 핵심

### 1. Multi-Agent System에서도 LLM 자체의 Knowledge와 Capability 한계는 남기 때문에 Memory와 Tool이 중요하다.

### 2. LLM은 Training Data에 의존하기 때문에 최신 정보와 Private Information을 Parameter만으로 다루기 어렵다.

### 3. 최신 정보를 얻는 대표적인 방법은 Web Search와 External Retrieval이다.

### 4. Agent는 LLM 자체가 모든 일을 수행하는 것이 아니라 자신보다 적합한 Tool을 선택해 문제를 해결할 수 있다.

### 5. Tool에는 Calculator, Python Executor, Database, Search Engine, Notion, Slack 등 매우 다양한 Software가 포함될 수 있다.

### 6. 강의에서는 Agent Intelligence를 기억과 지식, 행동과 능동성, 학습과 적응의 세 축으로 볼 수 있다고 설명한다.

### 7. External Memory를 이용하면 Model Parameter Update 없이 최신 정보와 Private Data를 사용할 수 있다.

### 8. RAG는 Query와 관련된 External Evidence를 검색하여 LLM Context에 추가한다.

### 9. RAG는 Hallucination을 줄이고 Evidence 기반 Response를 가능하게 할 수 있다.

### 10. 하지만 잘못된 Evidence를 Retrieval하면 RAG의 Answer 역시 틀릴 수 있다.

### 11. 기본 RAG Pipeline은 Retrieval → Augmentation → Generation으로 볼 수 있다.

### 12. Vector DB 구성, Embedding Model, Chunking 등 RAG의 각 단계 자체가 중요한 Engineering/Research 문제다.

### 13. Naive RAG는 흔히 한 번의 Retrieval만 수행하기 때문에 복잡한 Multi-step Task에 한계가 있다.

### 14. Agentic RAG에서는 Agent가 검색 여부, Query, Search Tool, Retrieval Result Quality 등을 능동적으로 판단한다.

### 15. 검색 결과가 부족하면 Self-evaluation 후 다시 Search할 수 있다.

### 16. Agentic RAG는 단순 Information Retrieval에서 Active Knowledge Acquisition으로 발전한 구조라고 볼 수 있다.

### 17. Agentic RAG에 Memory Module을 추가하여 과거 Query, Search Result, Successful Strategy 등을 기억할 수 있다.

### 18. Agent Memory에서는 모든 정보를 저장하기보다 무엇을 보존하고 무엇을 잊을지를 결정하는 것이 중요하다.

### 19. 강의에서는 Short-term Memory와 Long-term Memory로 나누는 단순한 Architecture를 예로 든다.

### 20. 복잡한 RAG Query를 해결하기 위해 Calculator와 Vector DB 등 여러 Tool을 순차적으로 사용할 수 있다.

### 21. Agentic RAG에서는 Planning이 잘못되면 Self-evaluation을 통해 Plan을 수정할 수 있다.

### 22. Agentic System은 정확성을 높일 수 있지만 Debugging, Latency, Token Cost 같은 Agent 특유의 문제도 그대로 가진다.

### 23. Multi-Agent RAG에서는 상위 Agent가 Task를 하위 Retrieval Agent들에게 분배할 수 있다.

### 24. 각 하위 Agent는 특정 Source나 Tool에 전문화될 수 있다.

### 25. 여러 Retrieval Agent를 Parallel하게 사용해 Efficiency와 Source Diversity를 높이는 구조를 생각할 수 있다.

### 26. Tool Use에는 언제 Tool을 호출할지, 어떤 Tool을 사용할지, API를 어떻게 정확히 호출할지라는 세 가지 핵심 문제가 있다.

### 27. 이 세 Decision을 합치면 Agent의 Tool-use Policy가 된다.

### 28. Toolformer는 LLM이 Human의 직접 개입을 최소화하면서 Tool을 사용할 적절한 위치를 학습하는 초기 대표 연구로 강의에서 소개된다.

### 29. Toolformer 계열 Idea에서는 Model이 직접 생성한 결과와 Tool 사용 결과를 비교하여 Tool 사용이 도움이 되는 Sample을 만들 수 있다.

### 30. Model은 Task에 따라 Tool을 시작·중간·마지막 등 서로 다른 시점에 호출해야 할 수 있다.

### 31. Tool Use의 중요한 의미는 모든 Knowledge를 Parameter에 넣는 대신 필요할 때 External Capability를 호출할 수 있다는 것이다.

### 32. Agent 시대에는 모든 Knowledge를 소유하는 능력보다 필요한 Knowledge/Capability에 적절히 접근하는 능력이 중요해질 수 있다.

### 33. 새로운 Tool Capability를 학습할 때 Human Labeling을 최소화하는 것이 Scalability 측면에서 중요하다.

### 34. Web에는 이미 수많은 API Documentation이 존재하므로 이를 Tool Learning Data로 사용할 수 있다.

### 35. API Documentation에는 Function Purpose, Usage, Argument 등에 대한 정보가 포함되어 있어 LLM의 Tool-use Learning에 활용할 수 있다.

### 36. 같은 기능을 수행하는 Tool이 여러 개 있다면 RL의 Reward Signal을 사용해 상황별로 더 좋은 Tool을 선택하도록 학습할 수 있다.

### 37. Tool Use 연구는 Tool의 유용성 증명 → 호출 시점 학습 → 대규모 API Learning → Tool Selection → Interface Standardization 방향으로 발전했다.

### 38. 세상의 Tool은 서로 다른 API 규격을 사용하기 때문에 Agent Integration Complexity가 매우 커진다.

### 39. Tool API가 변경되면 기존 Integration이 깨질 수 있다는 Maintenance 문제도 있다.

### 40. 모든 Tool의 개별 사용법을 Model에 직접 Training하는 것은 비효율적이다.

### 41. MCP는 다양한 Model과 Tool 사이에 공통된 Communication Protocol을 제공하여 Integration을 표준화하려는 방식이다.

### 42. MCP의 목적은 Tool Reusability, Compatibility, Integration Efficiency를 높이는 것이다.

### 43. MCP의 핵심은 세부 Client/Server 구현보다 Tool마다 다른 호출 방식을 공통 규격으로 묶는 데 있다.

### 44. Tool 사용 능력과 MCP는 다른 개념이다. Tool Learning은 Tool을 언제 어떻게 사용할지 학습하고, MCP는 연결 규격을 표준화한다.

### 45. Multi-Agent System이 커지면서 Agent와 Agent 사이 Communication Protocol도 중요해진다.

### 46. 강의에서는 Agent-to-Agent Communication을 표준화하는 A2A 계열 Protocol의 필요성을 언급한다.

### 47. 강의 흐름에서는 MCP를 Agent↔Tool, A2A를 Agent↔Agent Protocol로 기억하면 된다.

### 48. Physical AI에서는 Agent가 Human, Robot, Sub-agent 등 여러 Actor와 Communication할 가능성이 있다.

### 49. Physical AI 발전에는 Robot Capability만 아니라 Digital Twin, Simulation, Agent Communication 등의 Infrastructure도 중요하다.

### 50. Agent가 실제 상품 구매 등을 수행하려면 Payment와 Permission/Regulation 문제도 해결되어야 한다.

### 51. 강의에서는 Cryptocurrency 기반 Automated Payment 가능성을 하나의 미래 Use Case로 언급한다.

### 52. 하지만 어떤 Cryptocurrency가 사용될지는 강의에서 확정적으로 다루지 않으며 미래 전망 수준이다.

### 53. Physical AI 시대에는 Robot 자체만이 아니라 Robot을 둘러싼 Agent Ecosystem 전체를 설계해야 한다.

---

# 158. 이번 강의 전체 논리

```text
Multi-Agent를 만들었다.
      ↓
그런데
각 Agent의 LLM은
여전히 모든 것을 알 수 없다.
      ↓
최신 정보 부족
Private Data 부족
실행 능력 부족
      ↓

Memory
+
Tool


먼저 Memory
      ↓
LLM Parameter 안에
모든 Knowledge를 넣을까?
      ↓
비효율적
      ↓
External Memory
      ↓
RAG


Query
↓
Relevant Evidence 검색
↓
Context에 추가
↓
Grounded Response


하지만
한 번 검색한 결과가
틀릴 수도 있다.
      ↓
Agentic RAG


Search
↓
Evaluate
↓
Bad?
↓
Search Again


그리고
과거 Experience도 기억
      ↓
Short-term Memory
Long-term Memory


Query가 복잡하다.
      ↓
Search 하나로 부족
      ↓
Planning
      ↓
Calculator
+
Vector DB
+
Web Search


더 복잡해진다.
      ↓
여러 Retrieval Agent
      ↓
Multi-Agent RAG


이제 Tool
      ↓
Calculator
Search
Python
DB
API
Slack
Notion
...


문제는 세 가지다.
      ↓

언제 Tool을 쓸까?

어떤 Tool을 쓸까?

어떤 Argument로 부를까?


이게
Tool-use Policy


Human이
일일이 Tool 사용법을
Labeling하기 어렵다.
      ↓
Toolformer
      ↓
Tool 사용이
실제로 도움이 되는 위치를
자동으로 찾는다.


Tool이 수천 개다.
      ↓
API Documentation 활용
      ↓
대규모 Tool Learning


같은 기능의
Tool도 여러 개다.
      ↓
Reward
↓
RL
↓
상황별 좋은 Tool 선택


그런데
모든 Tool API가 다르다.
      ↓
Integration Hell
      ↓
MCP
      ↓
공통 Protocol


이제 Tool뿐 아니라
Agent도 많아진다.
      ↓
Agent ↔ Agent
      ↓
A2A-type Protocol


Physical AI에서는
      ↓
Agent
Human
Robot
Payment
Simulation
Digital Twin
      ↓
전체 Ecosystem이 필요
```

---

# 159. 이번 강의 핵심 한 문장

> **Memory & Tool 기반 Agent System은 LLM이 모든 Knowledge와 Capability를 자신의 Parameter 안에 보유해야 한다는 가정에서 벗어나, RAG·Short/Long-term Memory를 통해 최신·Private·과거 경험 정보를 외부에서 가져오고 Calculator·Search·Database·Software API 같은 Tool을 필요할 때 호출함으로써 LLM의 구조적 한계를 보완하는 방식이며, 이를 위해 Agent는 언제 어떤 Tool을 어떤 Argument로 사용할지를 Policy로 학습해야 하고, Toolformer와 API Documentation 기반 Tool Learning 및 Reinforcement Learning이 이러한 능력을 개선하며, 수많은 이질적 Tool의 Integration 문제를 해결하기 위해 MCP 같은 공통 Protocol이 등장하고 Multi-Agent 환경에서는 Agent-to-Agent Communication Protocol까지 중요해진다.**

가장 짧게 기억하면 다음과 같다.

```text
Memory
= LLM이 모르는 정보를
  밖에 저장하고 다시 가져온다.

RAG
= 외부 Knowledge를 검색해서
  답변의 근거로 사용한다.

Agentic RAG
= 검색 결과까지 평가하고
  필요하면 다시 검색한다.

Tool
= LLM이 직접 못 하는 일을
  외부 Capability에 맡긴다.

Tool-use의 3문제
= 언제?
  무엇을?
  어떻게?

Toolformer
= 언제 Tool이 도움이 되는지
  Model이 학습한다.

RL Tool Selection
= 여러 Tool 중
  상황에 맞는 Tool을 고른다.

MCP
= Agent와 Tool의
  연결 규격을 표준화한다.

A2A
= Agent끼리
  소통하기 위한 방향이다.

Agent의 본질
= 모든 것을 아는 것이 아니라
  모를 때 무엇을 찾아
  어떻게 활용할지 아는 것이다.
```