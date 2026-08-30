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
