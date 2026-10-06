[강의 링크](https://www.youtube.com/watch?v=GXUPaZnEwDo)
## 시험 범위에 포함되는 내용
| 시험 토픽                 | 근거                                                           |
| --------------------- | ------------------------------------------------------------ |
| **도메인 특화 AI**         | 특정 업무·산업·전문 분야로 Problem Space 제한                             |
| **RAG**               | Domain-specific RAG / Deep Research                          |
| **AI Agent 동작**       | Reason → Search → Evaluate → Requery → Synthesis             |
| **AI Agent의 Tool 활용** | Domain별 Tool Pool 설계                                         |
| **LLM Agent의 특성**     | Domain Knowledge, Tool, Memory, Workflow, Evaluation, Safety |
# 0. 강의 전체 개요

지금까지 Agent를 구성하는 핵심 요소를 배웠다.

```text
LLM 하나만으로 부족
↓
Multi-Agent

LLM 내부 Knowledge만으로 부족
↓
Memory + Tool

복잡한 문제를 바로 해결하기 어려움
↓
Reasoning + Planning

긴 Task를 수행
↓
Task Decomposition
↓
Tool / Specialized Agent 활용
↓
Execution
```

이를 종합하면 현대 AI Agent는

```text
Complex Goal
↓
Reasoning
↓
Planning
↓
Subtask Decomposition
↓
적합한 Model / Tool / Agent 선택
↓
Execution
↓
Evaluation
↓
Replanning
```

을 통해 긴 현실 문제를 해결하는 System이라고 볼 수 있다.

그런데 여기서 새로운 질문이 나온다.

> **굳이 하나의 Agent가 세상 모든 문제를 잘해야 하는가?**

강의의 답은

```text
반드시 그럴 필요는 없다.
```

이다.

오히려 현실에서는

```text
우리 회사

우리 업무

우리 Domain

특정 문제 범위
```

에 최적화된 **Domain-Specific AI Agent**가 더 현실적이고 효과적일 수 있다.

---

# 1. General Agent의 이상과 현실

General Agent의 이상적인 형태는

```text
어떤 질문이 들어와도

어떤 Domain이든

어떤 문제든

잘 해결
```

하는 System이다.

강의에서는 이를 일종의

```text
모든 것을 아는 현인
```

에 비유한다.

---

# 2. General Agent를 만들기 어려운 이유

범용 Agent를 매우 높은 수준으로 만들려면

```text
많은 Data

큰 Model

많은 Training

많은 Inference Compute

많은 Engineering

많은 비용
```

이 필요하다.

그리고 실제로 우리가 해결하고 싶은 문제는 대부분

```text
세상의 모든 문제
```

가 아니다.

---

# 3. 현실의 Agent 문제

회사 입장에서 필요한 것은 흔히

```text
우리 회사의 업무를 잘하는 Agent

우리 Domain에서 정확한 Agent

특정 Workflow를 자동화하는 Agent
```

다.

따라서

```text
Generality
```

보다

```text
Domain Performance
```

가 더 중요할 수 있다.

강의에서도 Agent가 모든 문제를 잘할 필요는 없으며 특정 회사나 Domain에 범위를 제한한 Agent가 발전할 가능성이 높다고 설명한다.

---

# 4. Domain-Specific AI Agent

**Domain-Specific Agent**는

> **특정 업무·산업·전문 분야의 Problem Space를 명확히 제한하고, 그 범위 안에서 높은 정확도와 효율성을 내도록 설계한 Agent**

라고 볼 수 있다.

예:

```text
Medical Agent

Legal Agent

Research Agent

Financial Agent

Software Engineering Agent

Company Internal Agent
```

등이다.

---

# 5. Domain을 좁히면 무엇이 달라지는가?

범용 Agent:

```text
모든 상황
모든 Tool
모든 Knowledge
모든 Task
```

를 고려해야 한다.

Domain Agent:

```text
Known Task Range

Known Data Sources

Known Tools

Known Rules

Known Evaluation Criteria
```

를 중심으로 System을 설계할 수 있다.

---

# 6. Domain Narrowing의 핵심 효과

```text
Search Space ↓

Possible Actions ↓

Relevant Knowledge 명확

Evaluation 쉬워짐

Training Data 범위 축소

Tool 범위 축소

Resource Allocation 최적화
```

가 가능해진다.

---

# 7. 이 강의의 대표 사례 — Deep Research

강의에서는 Domain-Specific Agent의 사례로 **Research Domain**을 선택한다.

즉

```text
General Agent
↓
Research라는 Domain으로 Scope 제한
↓
Deep Research Agent
```

를 살펴본다.

---

# 8. Information Search의 발전 흐름

강의에서는 Research Agent를 이해하기 위해 정보 탐색 방식의 발전을 먼저 설명한다.

초기:

```text
User
↓
Web Search
↓
Search Result 직접 읽기
```

---

# 9. LLM Chatbot 시대

LLM이 발전하면서

```text
User Question
↓
LLM
↓
Natural Language Answer
```

형태가 일반화됐다.

사용자는 직접 검색 결과를 일일이 읽기보다 LLM에게 질문하기 시작했다.

---

# 10. LLM 내부 Knowledge의 한계

하지만 LLM Parameter만 이용하면 문제가 생긴다.

예를 들어 존재하지 않는 Historical Event를 질문했을 때 과거 Model은

```text
사실이 아닌 내용을
그럴듯하게 생성
```

할 수 있었다.

즉 **Hallucination** 문제다.

---

# 11. 그래서 Retrieval / RAG

```text
Question
↓
Search / Retrieval
↓
External Evidence
↓
LLM
↓
Grounded Answer
```

로 발전한다.

이를 통해 단순 Internal Knowledge보다 신뢰도 높은 답을 만들 수 있다.

---

# 12. 다음 단계 — Deep Research

RAG도 기본적으로는

```text
검색
↓
답변
```

에 가까울 수 있다.

하지만 실제 Research는 훨씬 복잡하다.

```text
무엇을 조사해야 할까?

어떤 자료가 필요한가?

이 자료는 신뢰할 수 있는가?

다른 자료도 찾아야 하는가?

현재 Hypothesis가 맞는가?

Research Question 자체가 잘못된 것은 아닌가?
```

를 반복해야 한다.

---

# 13. Deep Research의 목표

강의에서 Deep Research는

> **일상적인 단순 질문이 아니라 특정 Domain의 깊고 복잡한 문제를 Agent가 자율적으로 조사하고 해결하는 방향**

으로 설명된다.

단순 Search Assistant보다 훨씬 높은 수준의 Agent다.

---

# 14. Deep Research는 완전히 새로운 기술인가?

강의의 핵심은 **아니다**.

Deep Research를 위해 완전히 새로운 기본 Component가 등장한 것이 아니라

```text
Reasoning

Planning

Search

RAG

Tool Use

Self-evaluation

Memory

Autonomy
```

같이 지금까지 배운 Agent Component들을 **Research Domain에 맞게 다시 조합**한 것이다.

---

# 15. Domain-Specific Agent의 본질

따라서 중요한 질문은

```text
새로운 Agent Component가 필요한가?
```

보다

```text
기존 Component를
이 Domain에서는
어떻게 조합해야 하는가?
```

이다.

---

# 16. Medical Agent도 같은 원리

Research가 아니라 의료라면

```text
Medical Knowledge

Medical Literature

Medical Tools

Clinical Rules

Doctor Review

Medical Evaluation Metric
```

에 맞게 Agent Architecture를 다시 구성해야 한다.

---

# 17. Legal Agent도 마찬가지

```text
Legal Documents

Applicable Law

Case Law

Jurisdiction

Legal Search Tool

Citation Requirement
```

등에 맞게 설계한다.

즉 **Domain이 바뀌면 같은 Agent Component의 조합 방법도 달라진다.**

---

# 18. Deep Research Agent의 큰 구조

```text
Research Question
↓
Reasoning
↓
무엇을 조사할지 결정
↓
Search
↓
Documents
↓
Evaluation
↓
충분한가?
├─ Yes → 다음 단계
└─ No
    ↓
    Query 수정
    ↓
    다른 Search
↓
Evidence Synthesis
↓
Hypothesis / Conclusion
```

이다.

---

# 19. 일반 Search와 Deep Research의 차이

### 일반 Search

```text
Query
↓
Search
↓
Result
```

### Deep Research

```text
Question
↓
Reasoning
↓
Search
↓
Result Evaluation
↓
New Question
↓
Search
↓
Evaluation
↓
...
```

한다.

---

# 20. Research Agent의 핵심 — Autonomy

Deep Research에서 중요한 것은 Agent가

```text
언제 Search할지

무엇을 Search할지

어떤 순서로 Search할지

언제 다시 Search할지
```

스스로 결정하는 것이다.

즉 Introduction 강의의 핵심인 **Autonomy**가 실제 Domain Workflow 안에 들어간다.

---

# 21. Think → Search → Think

Deep Research Agent는

```text
Think
↓
Search
↓
Observe
↓
Think
↓
Search
↓
Observe
```

를 반복한다.

앞 차시의 **ReAct**와 자연스럽게 연결된다.

---

# 22. Search Result를 그대로 믿지 않는다

문서를 Retrieval했다고 해서 바로 최종 답변에 사용하지 않는다.

```text
Retrieved Document
↓
Self-evaluation
↓
Relevant?
Reliable?
Enough?
```

를 평가한다.

---

# 23. 필요한 경우 재검색

검색 결과가 부족하면

```text
Current Evidence
↓
무엇이 부족한지 Reasoning
↓
New Query
↓
Retrieval Again
```

한다.

---

# 24. Citation을 따라갈 수도 있다

Research Paper A를 읽었는데 중요한 주장에 Paper B가 Reference돼 있다면

```text
Paper A
↓
Reference B 발견
↓
Paper B Retrieval
```

처럼 Research Path가 동적으로 확장될 수 있다.

---

# 25. 잘못된 검색 집합을 버릴 수도 있다

처음 Search가 잘못되었다고 판단하면

```text
Search Set A
↓
Poor Evidence
↓
Reject
↓
New Search Strategy
```

로 갈 수 있다.

이것이 Fixed RAG와 다른 Agentic Research의 특징이다.

---

# 26. Domain-Specific Agent의 장점 ① 평가하기 쉬워진다

General Agent에서는 어떤 Retrieval이

```text
좋다 / 나쁘다
```

를 보편적으로 판단하기 어렵다.

왜냐하면 세상의 모든 Domain에 대해 하나의 평가 규칙을 만들기 어렵기 때문이다.

---

# 27. Domain을 좁히면 평가 기준이 생긴다

예를 들어

```text
Internal Medicine Agent
```

를 만든다고 하자.

검색했더니 관련 없는 Psychology Paper만 가져왔다면

```text
이 Retrieval은 부적절할 가능성이 높다.
```

라고 판단할 수 있다.

---

# 28. 중요한 표현

Domain을 좁힌다고 Retrieval Correctness를 **완벽하게** 판단할 수 있게 되는 것은 아니다.

정확한 표현은

> **부정확한 Retrieval을 판단할 가능성을 높인다.**

이다.

강의에서도 이 뉘앙스를 명확히 구분한다.

---

# 29. 왜 중요한가?

Agent가

```text
잘못된 Retrieval
```

을 알아차릴 수 있어야

```text
Retry

Requery

Different Tool

Human Escalation
```

같은 다음 Action을 선택할 수 있다.

---

# 30. Domain-Specific Agent의 장점 ② Adaptability

범용 LLM은 매우 다양한 환경에 대응해야 한다.

따라서 특정 환경에서의 아주 세밀한 Strategy를 모두 학습하기 어렵다.

---

# 31. Domain을 좁히면 Training 범위도 좁아진다

```text
Specific Domain
↓
Relevant Data 범위 ↓
↓
Specific Behavior Training 쉬움
```

이다.

---

# 32. 필요한 Experience를 집중적으로 학습 가능

예:

```text
Medical Literature Retrieval

Medical Evidence Ranking

Medical Query Reformulation

Medical Safety Rules
```

등만 집중적으로 학습할 수 있다.

---

# 33. General Knowledge를 모두 유지할 필요도 줄어든다

특정 Agent가

```text
회사 내부 HR Agent
```

라면

```text
수학 올림피아드

소설 창작

천문학
```

에 최고 성능을 가질 필요는 없다.

---

# 34. Resource Efficiency

Domain이 제한되면

```text
Training Compute

Inference Compute

Tool Pool

Memory

Context
```

도 더 집중적으로 배분할 수 있다.

---

# 35. Domain-Specific Agent의 현실적 의미

세상의 모든 문제를 해결하는 Agent가 아니라

> **내가 실제로 해결해야 하는 특정 문제를 높은 품질로 해결하는 Agent**

를 만드는 것이다.

강의에서는 이것이 현실 문제 해결에 더 직접적인 접근이라고 설명한다.

---

# 36. Domain-Specific Model / Agent를 만드는 방법

강의에서는 여러 방법을 소개한다.

```text
Prompting

Supervised Learning / Fine-tuning

Reinforcement Learning

Test-time Scaling

Agent Architecture Design
```

등이다.

---

# 37. Prompt 기반 Specialization

가장 단순하게

```text
You are a medical research agent...
```

처럼 System Prompt와 Domain Instruction을 줄 수 있다.

장점:

```text
빠름

Training 필요 없음

변경 쉬움
```

이다.

---

# 38. Fine-tuning

Domain Data를 이용해 Model 자체를 학습할 수도 있다.

```text
Domain Dataset
↓
Fine-tuning
↓
Domain-specialized Model
```

한다.

---

# 39. Reinforcement Learning

Domain에서 좋은 Behavior가 무엇인지 Reward를 설계할 수 있다면

```text
Agent Behavior
↓
Domain Reward
↓
RL
```

로 최적화할 수 있다.

강의 후반에도 특정 Domain에 맞추기 위해 여러 Algorithm과 Reward를 사용할 수 있다고 설명한다.

---

# 40. Test-time Scaling도 Domain에 맞게 설계

앞 Reasoning 강의에서는

```text
Easy Task
→ Short Reasoning

Hard Task
→ Long Reasoning
```

을 배웠다.

하지만 Domain이 좁아지면 Task Difficulty Distribution도 어느 정도 예측할 수 있다.

---

# 41. 항상 복잡한 Domain

어떤 Domain의 Query가 거의 항상 복잡하다면

```text
Router
↓
Fast or Slow 선택
```

자체가 필요 없을 수도 있다.

그냥

```text
항상 Deep Reasoning Path
```

로 보내면 된다.

---

# 42. 항상 간단한 Domain

반대로 거의 모든 Query가 간단하다면

```text
Long Reasoning
```

에 Inference Budget을 낭비할 필요가 없다.

---

# 43. Domain Narrowing = 변수 감소

범용 Agent에서는

```text
이 문제는 쉬운가?

어려운가?

어떤 Tool?

어떤 Domain?

어떤 Safety Rule?

어떤 Model?
```

등 많은 변수를 실시간으로 판단해야 한다.

Domain Agent는 일부가 미리 고정된다.

```text
Domain fixed

Tool Pool fixed

Expected Difficulty known

Evaluation Rules known
```

한다.

따라서 System Optimization이 쉬워진다.

---

# 44. Deep Research에서 가장 중요한 Loop

강의에서는 Research Domain의 특성 때문에

```text
Think
↓
Evaluate
↓
Think Again
```

하는 반복 과정이 특히 중요하다고 설명한다.

---

# 45. 왜 Research에서는 앞단 Reasoning이 중요한가?

좋은 연구자는 무작정 Experiment부터 많이 돌리는 것이 아니라 먼저

```text
무슨 문제를 풀고 있는가?

왜 중요한가?

Hypothesis가 논리적인가?

문제 Definition이 맞는가?
```

를 오래 고민한다.

---

# 46. 잘못된 문제를 빨리 푸는 것은 의미가 없다

```text
Wrong Research Question
↓
Fast Experiment
↓
많은 Resource 소비
↓
결국 쓸모없는 Result
```

이 될 수 있다.

---

# 47. 따라서 Deep Research는 Front-loaded Reasoning

Research Workflow를 크게

```text
Front Stage
→ 반복적으로 생각하고 검증

Back Stage
→ 확정된 Plan을 실행
```

하는 구조로 만들 수 있다.

---

# 48. Research Agent의 Front Stage

```text
Problem Definition
↓
Reasoning
↓
Search
↓
Self-evaluation
↓
Problem Refinement
↓
Search Again
↓
Hypothesis Refinement
```

을 반복한다.

---

# 49. Problem이 충분히 정의된 뒤

그다음에는

```text
논문 검색

Experiment 실행

Data 분석
```

처럼 Execution 단계로 넘어간다.

---

# 50. Back Stage

앞에서 충분히 검증한 Plan을

```text
Execute
```

하는 비중이 커진다.

물론 결과가 이상하면 다시 앞단으로 돌아갈 수 있다.

---

# 51. Domain마다 최적 Loop 위치가 다를 수 있다

Research에서는

```text
앞단 Iteration ↑
```

이 중요할 수 있다.

하지만 다른 Domain에서는

```text
실행
↓
빠른 Feedback
↓
Iteration
```

을 많이 돌리는 것이 더 효과적일 수도 있다.

---

# 52. 이것이 Domain-Specific Architecture

즉 Agent의 Component는 같아도

```text
어디서 많이 Reasoning할지

어디서 많이 Tool을 호출할지

언제 Iteration할지
```

가 Domain마다 달라진다.

강의에서도 Research라는 Domain이기 때문에 반복적인 Reasoning을 Process 앞쪽에 배치할 수 있다고 설명한다.

---

# 53. Domain-Specific Agent의 핵심은 Workflow Design

단순히

```text
General Agent
+
Domain Prompt
```

만으로 끝나는 것이 아니다.

실제로는

```text
Domain Workflow 이해
↓
중요한 Decision Point 파악
↓
Agent Module 배치
↓
Tool / Memory / Reasoning 위치 결정
↓
Evaluation / Retry Rule 설계
```

가 필요하다.

---

# 54. Research Domain Example

```text
Research Goal
↓
Problem Definition Agent
↓
Search Agent
↓
Evidence Evaluator
↓
Hypothesis Agent
↓
Experiment Agent
↓
Analysis Agent
↓
Report Agent
```

처럼 구성할 수도 있다.

이것은 강의 내용을 구조화한 예시적 표현이며, 하나의 고정 정답 Architecture는 아니다.

---

# 55. Agent Architecture보다 System도 중요하다

좋은 Agent Workflow를 설계했다고 실제 Service가 바로 완성되는 것은 아니다.

예를 들어 Deep Research Agent가 Web을 검색해야 하는데

```text
Website가 Crawling을 차단
```

하고 있을 수 있다.

---

# 56. Web Crawling

Web Crawling은 Software가 자동으로 Webpage의 Data를 수집하는 것이다.

현재 많은 Site는

```text
LLM Training Data Scraping

Unauthorized Crawling
```

등을 막기 위해 Crawling을 제한한다.

---

# 57. Agent Architecture 밖의 현실 문제

Agent Logic:

```text
Web Search Tool 호출
```

은 완벽해도

```text
Website Block

Authentication Failure

Rate Limit

Network Failure
```

가 생기면 Task를 수행하지 못한다.

---

# 58. 그래서 System Engineering

좋은 Agent를 만드는 데는

```text
Model Knowledge
+
Agent Architecture
+
System Engineering
```

이 필요하다.

---

# 59. Serving까지 생각해야 한다

실제 많은 User가 Agent를 동시에 사용한다면

```text
많은 Request

Parallel Execution

Tool Concurrency

Rate Limit

Memory

Latency
```

도 고려해야 한다.

---

# 60. Model만 알아서는 부족하다

Agent 연구자가

```text
Agent Workflow
```

만 설계하고 끝내면 실제 Deployment 문제를 놓칠 수 있다.

강의에서는 모델을 Serving하고 실제 사용자가 사용할 수 있도록 만드는 시스템적 요소도 함께 이해하는 것이 중요하다고 강조한다.

---

# 61. AI 시대에 넓게 알아야 하는 이유

강의에서는 이제 세부 계산과 구현 중 상당 부분은 LLM에게 맡길 수 있다고 본다.

하지만 사람이

```text
무슨 문제를 풀어야 하는지

어떤 Solution 방향이 맞는지

어떤 기술을 사용해야 하는지
```

는 Guide해야 한다.

---

# 62. LLM에게 좋은 Guide를 주려면

```text
Domain Knowledge

Software

System

AI

Data

Product
```

등을 넓게 이해할 필요가 있다.

즉 AI 시대에는 한 영역을 깊게 아는 것과 함께 넓은 Context를 이해하는 능력도 중요하다는 강사의 관점이다.

---

# 63. LLM 사용 능력의 차이

같은 LLM을 사용하더라도

User A:

```text
번역
요약
```

정도만 사용한다.

User B:

```text
여러 Research Project
↓
Plan
↓
LLM에게 Subtask Guide
↓
Result 평가
↓
다른 Research 연결
```

처럼 활용할 수 있다.

---

# 64. LLM을 쓴다고 자동으로 좋은 Output이 나오지는 않는다

강의에서는

```text
LLM을 사용
=
자동으로 훌륭한 연구
```

는 아니라고 강조한다.

보다 현실적인 구조는

```text
Strong Human
+
Strong Guidance
+
LLM
↓
Better Output
```

이다.

---

# 65. LLM을 잘 쓰는 사람

강의에서 말하는 LLM 활용 능력은 단순 Prompt 작성 능력만을 뜻하지 않는다.

```text
짧은 시간

제한된 Resource
```

안에서

```text
적절한 문제를 정의하고

LLM에게 Guide하고

결과를 평가하고

필요한 Workflow를 만들고
```

Output을 극대화하는 능력에 가깝다.

---

# 66. Domain-Specific Agent의 장점 ③ Self-evaluation

Domain이 좁으면 최종 Output도 더 명확한 기준으로 평가할 수 있다.

예:

```text
이 Result는
우리 Domain에 필요한 결과인가?
```

를 판단할 수 있다.

---

# 67. Cross-validation

하나의 Agent Output을

```text
다른 Agent

Domain Rule

External Tool

Domain Evidence
```

등으로 다시 검증할 수도 있다.

---

# 68. General Agent보다 Evaluation이 쉬워진다

범용 Agent가 어떤 내용이든 생성할 수 있다면

```text
모든 Output에 대한
Universal Evaluation
```

은 매우 어렵다.

Domain Agent는 평가 범위를 제한할 수 있다.

---

# 69. Confidence가 낮으면 Human Intervention

Domain-specific System에서는

```text
Low Confidence

Out-of-domain

Rule Violation

Uncertain Evidence
```

같은 조건을 정의하기 쉬워진다.

---

# 70. 불확실하면 답하지 않는 방법

Agent에게

```text
무조건 Response를 생성
```

시키는 것이 아니라

```text
Confidence 낮음
↓
Abstain
↓
Human Escalation
```

하도록 만들 수 있다.

---

# 71. Human-in-the-loop

```text
Agent
↓
High Confidence
→ 자동 처리

Low Confidence
→ Human Review
```

구조다.

강의에서는 이것을 Domain-Specific Agent의 중요한 장점 중 하나로 본다.

---

# 72. 특히 High-stakes Domain에서 중요

예:

```text
Medical

Legal

Finance
```

같은 분야에서는 무조건 답을 생성하는 것보다

```text
모르거나 불확실하면
Human에게 넘김
```

이 훨씬 안전할 수 있다.

---

# 73. Domain Boundary는 Safety Boundary 역할도 한다

```text
Agent가 다룰 수 있는 범위
```

를 명확히 정의하면

```text
In-domain
→ 처리

Out-of-domain
→ 거절 / 전달
```

할 수 있다.

---

# 74. Domain-Specific Agent가 General Agent보다 현실적인 이유

정리하면

```text
문제 범위 명확

Relevant Data 명확

Tools 명확

Evaluation 명확

Safety Rule 명확

Resource Budget 예측 가능
```

하기 때문이다.

---

# 75. 하지만 Domain Agent도 쉽지는 않다

강의에서는 Agent Concept 자체는 단순해 보일 수 있지만

> **잘 작동하는 Agent를 만드는 것은 어렵다.**

고 강조한다.

---

# 76. Agent Engineering은 아직 빠르게 발전 중

현재도

```text
Planning

Architecture

Evaluation

Memory

Multi-Agent

Tool Use

RL

Routing
```

등이 계속 바뀌고 있다.

따라서 특정 Framework 하나를 배웠다고 끝나는 영역이 아니다.

---

# 77. Domain별 Best Practice도 다르다

```text
Research Agent의 Best Architecture
≠
Medical Agent의 Best Architecture

Medical
≠
Coding

Coding
≠
Manufacturing
```

이다.

---

# 78. Domain Radar

강의에서는 자신의 분야에서

```text
어떤 Agent 연구가 나오는가?

어떤 Architecture가 잘 작동하는가?

어떤 Tool이 등장하는가?
```

를 계속 관찰해야 한다고 설명한다.

즉 기술 변화에 대한 **Radar**를 켜 두라는 의미다.

---

# 79. Domain Agent 개발 경험 자체가 전문성이 된다

Agent는 아직 정답 Architecture가 정해져 있지 않기 때문에

```text
여러 실패 경험

Domain Workflow 이해

Tool Integration 경험

Evaluation 경험
```

이 매우 중요한 Know-how가 된다.

---

# 80. 남아 있는 Agent의 공통 문제

강의 마지막에서는 Domain-Specific Agent에도 아직 많은 문제가 남아 있다고 설명한다.

대표적인 것은

```text
Evaluation

Debugging

Error Propagation

Multi-Agent Management

Token Cost

Latency

System Efficiency
```

다.

---

# 81. Evaluation 문제

Agent는 여러 Step을 거친다.

```text
Reasoning
↓
Search
↓
Tool
↓
Agent
↓
Final Output
```

최종 결과가 틀렸을 때 어느 단계가 문제였는지 찾기 어렵다.

---

# 82. Debugging 문제

전통 Program은 Code Path를 비교적 추적하기 쉽다.

Agent는 확률적 Model Decision이 포함되므로

```text
왜 이 Tool을 골랐지?

왜 이 Agent에게 맡겼지?

왜 Query를 이렇게 만들었지?
```

를 분석하기 어렵다.

---

# 83. Error Propagation

Multi-Agent에서는

```text
Agent A Wrong Information
↓
Agent B가 사용
↓
Agent C까지 전달
↓
System-wide Error
```

가 될 수 있다.

앞 Multi-Agent 강의의 Hallucination Propagation 문제와 연결된다.

---

# 84. Agent 수 증가 문제

```text
Agent Count ↑
↓
Communication ↑
↓
Management Complexity ↑
```

한다.

---

# 85. Token Cost

여러 Agent가 서로 Message를 주고받고 Reasoning하면

```text
Token ↑

Inference Cost ↑
```

한다.

---

# 86. Latency

Agent가 순차적으로

```text
Think
↓
Search
↓
Think
↓
Tool
↓
Evaluate
```

하면 실제 User Response까지 시간이 오래 걸릴 수 있다.

---

# 87. 그래서 Agent System Optimization

좋은 Agent는 Accuracy만 좋은 것이 아니라

```text
Accuracy

Cost

Latency

Reliability

Maintainability
```

를 함께 고려해야 한다.

---

# 88. 강의의 미래 전망 — Human과 Agent

강의 마지막에는 강사의 미래 전망이 나온다.

현재:

```text
Human
↓
Agent를 사용
```

한다.

---

# 89. 미래에는 Agent가 Human에게 Task를 배분할 수도 있다

```text
Agent
↓
Task 판단
├─ Robot에게 Dispatch
└─ Human에게 Dispatch
```

하는 시대가 올 수 있다는 전망이다.

이는 강사의 미래 전망이지 현재 일반적으로 완성된 형태라고 받아들이면 안 된다.

---

# 90. Human의 역할에 대한 강의의 관점

AI가 발전할수록 중요한 것은

```text
AI보다 단순히 모든 일을
직접 더 잘하는 사람
```

이 되는 것이 아니라

```text
AI를 이해하고

Guide하고

만들고

조직하고

평가하고

Control할 수 있는 사람
```

이 되는 것이라는 강사의 메시지다.

---

# 91. AI를 사용하는 것을 넘어 만드는 사람

강의에서는 학습자의 장기적 방향으로

```text
AI User
↓
AI Builder
```

로 발전할 것을 제안한다.

---

# 92. AI Builder의 두 방향

```text
AI Model을 만드는 사람

또는

AI Agent를 만드는 사람
```

이다.

Agent System을 실제로 만들고 실패와 개선을 경험하는 전문성은 쉽게 대체하기 어려운 경험적 Know-how가 될 수 있다는 것이 강사의 관점이다.

---

# 93. General Agent vs Domain-Specific Agent

|구분|General Agent|Domain-Specific Agent|
|---|---|---|
|범위|매우 넓음|제한됨|
|Knowledge|범용|Domain 중심|
|Tool|매우 다양|필요한 Tool 중심|
|Evaluation|어려움|상대적으로 명확|
|Training|광범위|집중 가능|
|Resource|큼|효율화 가능|
|Reliability|Domain별 편차|특정 Domain에서 높일 수 있음|
|목적|폭넓은 문제 해결|특정 현실 문제 해결|

---

# 94. General RAG vs Deep Research Agent

|구분|일반 RAG|Deep Research|
|---|---|---|
|Search|보통 정해진 흐름|Agent가 결정|
|Query|User Query 중심|필요하면 재작성|
|Retrieval|한 번일 수 있음|반복 가능|
|Result 평가|제한적|Self-evaluation|
|Planning|약함|중요|
|Autonomy|낮음|높음|
|목표|정보 기반 답변|깊은 Research 수행|

---

# 95. General Agent vs Deep Research Agent

### General Agent

```text
다양한 Task
↓
General Capability
```

### Deep Research Agent

```text
Research Domain
↓
Search
Reasoning
Evidence
Citation
Hypothesis
Experiment
↓
Domain Workflow 최적화
```

한다.

---

# 96. Domain-Specific Agent와 RAG

Domain-specific RAG에서는

```text
General Web
```

만 검색하는 것이 아니라

```text
Medical DB

Legal DB

Company DB

Research Paper DB
```

같이 Source도 Domain에 맞게 제한할 수 있다.

---

# 97. Domain-Specific Agent와 Tool

Tool Pool도 제한 가능하다.

예:

### Research

```text
Paper Search

Web Search

Code Execution

Data Analysis
```

### Medical

```text
Medical Literature DB

Clinical Guideline DB

Medical Calculator
```

### Software

```text
Git

IDE

Terminal

CI

Issue Tracker
```

---

# 98. Tool 제한의 장점

```text
Tool Selection Search Space ↓

Wrong Tool Call ↓

Permission 관리 쉬움

Security 관리 쉬움
```

을 기대할 수 있다.

---

# 99. Domain-Specific Agent와 Memory

Memory에도 Domain 정보만 저장할 수 있다.

```text
Past Cases

Successful Workflows

Domain-specific Errors

Frequently Used Evidence
```

등이다.

---

# 100. Domain-Specific Agent와 Multi-Agent

각 Agent에게 Domain 안의 세부 전문 분야를 줄 수도 있다.

예:

```text
Medical Agent
├─ Radiology Agent
├─ Internal Medicine Agent
├─ Surgery Agent
└─ Evidence Reviewer
```

이다.

---

# 101. Domain Specialization은 두 단계로 가능

```text
Agent 전체를
Medical Domain으로 제한
↓
그 안에서
Sub-specialty Agent로 분리
```

할 수 있다.

---

# 102. Domain-Specific Agent와 Reasoning

모든 Reasoning을 범용적으로 할 필요가 없다.

Domain에서 자주 등장하는 Reasoning Pattern을 중심으로 강화할 수 있다.

예:

```text
Research:
Evidence → Hypothesis

Medical:
Symptom → Differential Diagnosis

Legal:
Fact → Applicable Rule → Conclusion
```

처럼 Domain Reasoning Structure가 존재한다.

---

# 103. Domain-Specific Agent와 Planning

Planning도 Domain Workflow를 반영한다.

Research:

```text
Question
↓
Literature
↓
Hypothesis
↓
Experiment
↓
Analysis
```

Software:

```text
Requirement
↓
Design
↓
Implementation
↓
Test
↓
Deploy
```

이다.

---

# 104. Domain Knowledge는 Model에만 있을 필요가 없다

Domain Knowledge는

```text
Fine-tuning

Prompt

RAG

Memory

Tool

Rule
```

등 여러 위치에 배치할 수 있다.

이것이 Agent Harness 설계의 중요한 선택이다.

---

# 105. 모든 것을 Fine-tuning할 필요는 없다

예를 들어 자주 바뀌는 Company Policy를 Parameter에 넣는 것보다

```text
Company Document
↓
RAG
```

가 적합할 수 있다.

반대로 일관된 Domain Behavior는 Fine-tuning/RL이 적합할 수도 있다.

---

# 106. 어떤 Knowledge를 어디에 둘 것인가?

좋은 Domain Agent를 만들 때 중요한 질문:

```text
이것은 Model Parameter에 넣을까?

Prompt에 넣을까?

RAG로 가져올까?

Memory에 저장할까?

Tool로 실행할까?

Hard Rule로 만들까?
```

이다.

---

# 107. Domain-Specific Agent Architecture의 본질

결국 Domain-specific Design은

```text
Domain Problem 분석
↓
필요한 Agent Capability 정의
↓
Reasoning Pattern 정의
↓
Tool 정의
↓
Knowledge Source 정의
↓
Evaluation 정의
↓
Safety / Human Escalation 정의
↓
Workflow 구성
```

이다.

---

# 108. 범위를 좁히는 것은 단순 성능 희생이 아니다

보통

```text
Scope ↓
```

라고 하면 Capability를 잃는 것으로 생각할 수 있다.

하지만 현실 Application에서는

```text
Scope ↓
↓
Uncertainty ↓
↓
System 최적화 가능
↓
In-domain Performance ↑
```

라는 이점이 있다.

---

# 109. Domain-Specific Agent의 중요한 Trade-off

```text
Generality ↓

Specialization ↑
```

이다.

즉

```text
모든 것을 조금씩 잘함
```

대신

```text
특정 것을 매우 잘함
```

을 선택한다.

---

# 110. Domain-Specific Agent가 특히 유용한 영역

```text
반복되는 전문 Workflow

정해진 Knowledge Source

명확한 Evaluation Criterion

높은 Reliability 요구

특정 Tool을 반복 사용
```

하는 업무다.

---

# 111. Out-of-domain Detection

Domain Agent에는

```text
이 문제는
내가 다룰 범위인가?
```

를 판단하는 기능도 중요하다.

---

# 112. Out-of-domain이면

```text
Reject

Different Agent로 Routing

Human에게 Escalate
```

할 수 있다.

---

# 113. General Agent보다 “모른다”를 정의하기 쉽다

General Agent:

```text
무엇까지 알아야 하는가?
→ 경계 모호
```

Domain Agent:

```text
지원하는 범위가 정의됨
↓
지원 범위 밖이면
모른다고 판단 가능
```

하다.

---

# 114. Self-evaluation도 Domain Rule을 이용

```text
Output
↓
Domain Criteria
↓
Pass / Fail
```

할 수 있다.

이것이 Domain Agent의 Robustness를 높이는 중요한 방법이다.

---

# 115. Human Escalation은 실패가 아니다

Agent가

```text
잘 모르겠다.
Human Review 필요.
```

라고 판단하는 것은 시스템 실패라기보다 제대로 설계된 Behavior일 수 있다.

---

# 116. 특히 Enterprise에서 중요

회사에서는 무조건 자동화율을 100%로 만드는 것보다

```text
Safe Automation
```

이 더 중요할 수 있다.

---

# 117. Domain Agent의 성공 기준

```text
Answer Quality
```

만 보면 안 된다.

```text
Task Success

Error Rate

Escalation Accuracy

Cost

Latency

Reliability
```

도 함께 평가해야 한다.

---

# 118. 자주 헷갈리는 점 ① — Domain-Specific Agent = 작은 LLM인가?

아니다.

큰 LLM을 Backbone으로 사용하면서 Agent Workflow만 특정 Domain으로 제한할 수도 있다.

핵심은 Model Size가 아니라 **Problem Scope와 System Design**이다.

---

# 119. 자주 헷갈리는 점 ② — Domain-Specific Agent = Fine-tuned Model인가?

아니다.

Fine-tuning은 하나의 방법일 뿐이다.

```text
Prompt

RAG

Tools

Memory

RL

Workflow
```

만으로도 Domain Specialization을 만들 수 있다.

---

# 120. 자주 헷갈리는 점 ③ — Domain을 좁히면 Agent가 무조건 정확해지는가?

아니다.

정확성을 높이기 쉬워지는 것이지 자동 보장은 아니다.

강의에서도 Retrieval 오류를 판단할 **가능성이 높아진다**고 표현한다.

---

# 121. 자주 헷갈리는 점 ④ — Deep Research = 검색을 많이 하는 Agent인가?

아니다.

핵심은

```text
Reasoning

Planning

Search

Self-evaluation

Re-search
```

를 자율적으로 반복한다는 것이다.

---

# 122. 자주 헷갈리는 점 ⑤ — Deep Research를 위해 완전히 새로운 기술이 필요한가?

강의의 핵심은 아니다.

기존 Agent Component를 Research Domain에 맞게 조합한 것으로 설명한다.

---

# 123. 자주 헷갈리는 점 ⑥ — RAG와 Deep Research는 같은가?

아니다.

RAG는 Retrieval을 Generation에 추가한 구조이고, Deep Research는 Agent가 Research Process 자체를 Planning하고 반복한다.

---

# 124. 자주 헷갈리는 점 ⑦ — Search Result를 얻으면 바로 사용하면 되는가?

아니다.

Agent가 Relevance와 Quality를 평가하고 부족하면 다시 검색할 수 있어야 한다.

---

# 125. 자주 헷갈리는 점 ⑧ — Domain Agent는 General Knowledge가 필요 없는가?

필요할 수 있다.

다만 모든 범용 Capability에서 최고 수준일 필요는 없다는 것이 강의의 관점이다.

---

# 126. 자주 헷갈리는 점 ⑨ — Domain을 좁히면 Test-time Scaling은 필요 없나?

아니다.

다만 Domain의 문제 난이도가 예측 가능하면 Reasoning Budget Policy를 더 단순하게 만들 수 있다.

---

# 127. 자주 헷갈리는 점 ⑩ — Deep Research에서는 Experiment를 빨리 많이 돌리는 것이 가장 중요한가?

강의에서는 오히려 앞단에서 문제와 논리를 제대로 정의하는 Reasoning이 중요하다고 강조한다.

---

# 128. 자주 헷갈리는 점 ⑪ — 모든 Domain Agent는 Research Agent와 같은 Workflow를 가져야 하는가?

아니다.

Research에서는 Front-loaded Reasoning이 유용할 수 있지만 다른 Domain에서는 다른 Loop가 더 적합할 수 있다.

---

# 129. 자주 헷갈리는 점 ⑫ — 좋은 Agent Architecture만 만들면 Service가 완성되는가?

아니다.

Crawling, Serving, Rate Limit, Infrastructure 같은 System Engineering 문제도 해결해야 한다.

---

# 130. 자주 헷갈리는 점 ⑬ — AI가 Coding을 해주면 System Knowledge는 필요 없어지는가?

강의의 주장은 반대다.

AI에게 어떤 문제를 풀게 할지 결정하려면 넓은 System 이해가 필요하다.

---

# 131. 자주 헷갈리는 점 ⑭ — LLM을 사용하기만 하면 생산성이 자동으로 높아지는가?

아니다.

좋은 Guidance와 Problem Definition이 있어야 LLM Output을 효과적으로 활용할 수 있다.

---

# 132. 자주 헷갈리는 점 ⑮ — Domain-Specific Agent는 항상 완전 자동화해야 하는가?

아니다.

Confidence가 낮으면 Human에게 Escalate하는 구조가 오히려 좋은 Domain Agent일 수 있다.

---

# 133. 자주 헷갈리는 점 ⑯ — Agent가 모른다고 말하는 것은 성능이 낮다는 의미인가?

반드시 아니다.

High-stakes Domain에서는 Out-of-domain/Low-confidence를 잘 감지하는 것이 중요한 Capability다.

---

# 134. 자주 헷갈리는 점 ⑰ — Agent 수가 많아질수록 성능이 좋아지는가?

아니다.

Management, Token Cost, Error Propagation이 증가한다.

---

# 135. 자주 헷갈리는 점 ⑱ — Agent Error는 최종 Answer만 보면 Debugging할 수 있는가?

어렵다.

Agent는 여러 Intermediate Decision을 거치므로 각 단계에 대한 Observability가 필요하다.

---

# 136. 자주 헷갈리는 점 ⑲ — Domain Agent의 Best Practice는 이미 정해져 있는가?

아니다.

강의에서는 Agent Architecture가 계속 발전하고 있고 Domain마다 최적 구조가 다를 수 있다고 설명한다.

---

# 137. 자주 헷갈리는 점 ⑳ — Domain-Specific Agent의 핵심은 Model인가?

Model도 중요하지만

```text
Domain Workflow

Tool

Memory

Evaluation

Reasoning Structure

System Engineering
```

을 함께 설계하는 것이 핵심이다.

---

# 138. Agent 모델 전체 과정과 연결

## Introduction to AI Agent

```text
Agent란?
↓
Autonomy
Perception
Memory
Reasoning & Planning
Learning
Tool Use
```

---

## Multi-Agent System

```text
Agent 하나로 부족
↓
여러 Agent를 조직
↓
Cooperation / Competition
```

---

## Memory & Tool

```text
Agent가 모든 것을
알고 직접 할 수 없음
↓
External Memory
+
Tool
```

---

## Reasoning & Planning

```text
복잡한 문제
↓
Reasoning
↓
Planning
↓
Action
↓
Replanning
```

---

## Domain-Specific Agent

이제 마지막 질문:

```text
이 모든 Component를

실제 특정 Domain에서

어떻게 조합해야 하는가?
```

이다.

---

# 139. Agent 모델 과정 전체 발전선

```text
LLM
↓
AI Agent
↓
Autonomous Goal Solving

↓
Single Agent 한계
↓
Multi-Agent

↓
Knowledge / Capability 한계
↓
Memory + Tools

↓
Complex Task
↓
Reasoning + Planning

↓
General Agent는
현실적으로 너무 넓음

↓
Scope 제한
↓
Domain-Specific Agent

↓
Domain Knowledge
Domain Tools
Domain Workflow
Domain Evaluation
Domain Safety

↓
Reliable Specialized Agent
```

---

# 140. Deep Research를 Agent 구성요소에 매핑

### Autonomy

```text
무엇을 검색할지
스스로 결정
```

### Perception

```text
Research Question
Current Evidence
Search Result 이해
```

### Memory

```text
이미 읽은 논문
Past Search
Intermediate Conclusion
```

### Reasoning

```text
현재 Evidence가
Hypothesis를 지지하는가?
```

### Planning

```text
다음에 무엇을
조사할 것인가?
```

### Tool Use

```text
Web Search

Paper Search

Code

Data Analysis
```

### Learning / Adaptation

```text
Search Result 평가
↓
Query / Plan 수정
```

이다.

---

# 141. Domain-Specific Agent를 설계할 때 질문해야 할 것

```text
1. 정확히 어떤 문제를 해결하는가?

2. Agent의 Domain Boundary는 어디인가?

3. 어떤 Knowledge Source가 필요한가?

4. 어떤 Tool만 허용할 것인가?

5. 어떤 Workflow가 실제 Domain Workflow와 맞는가?

6. 어느 단계에서 깊은 Reasoning이 필요한가?

7. 어떤 Result가 좋은 Result인가?

8. 어떻게 Self-evaluation할 것인가?

9. 어느 경우에 Retry할 것인가?

10. 어느 경우에 Human에게 넘길 것인가?

11. 어떤 정보는 Memory에 저장할 것인가?

12. 실제 Service에서 Cost와 Latency는 어떻게 관리할 것인가?
```

---

# 142. 이번 강의에서 반드시 기억할 핵심

### 1. 현대 AI Agent는 Reasoning과 Planning을 이용해 Complex Problem을 Subtask로 분해하고 적절한 Tool·Model·Agent를 활용해 긴 문제를 해결하는 System이다.

### 2. 하지만 하나의 Agent가 모든 Domain을 잘해야 할 필요는 없다.

### 3. 범용 Agent는 높은 Generality를 얻기 위해 많은 Data, Compute, Training과 System Complexity를 필요로 한다.

### 4. 실제 회사나 개인은 세상의 모든 문제보다 특정 업무와 Domain의 문제를 해결하는 것이 중요하다.

### 5. 따라서 특정 Domain에 Scope를 제한한 Domain-Specific AI Agent가 현실적인 방향이 될 수 있다.

### 6. Domain을 제한하면 관련 Knowledge, Tool, Data, Rule, Evaluation 기준을 더 명확하게 정의할 수 있다.

### 7. 강의에서는 Domain-Specific Agent의 대표 사례로 Deep Research Agent를 설명한다.

### 8. 정보 탐색은 Web Search → LLM Answer → RAG → Agent-based Deep Research 방향으로 발전했다고 강의에서 설명한다.

### 9. LLM Internal Knowledge만 사용하면 Hallucination과 최신성 한계가 존재한다.

### 10. RAG는 External Evidence를 검색해 LLM Answer를 Grounding한다.

### 11. Deep Research는 단순 Retrieval에서 끝나지 않고 Agent가 언제 무엇을 어떻게 검색할지 자율적으로 결정한다.

### 12. Deep Research Agent는 Search 결과를 Self-evaluation하고 부족하면 Query를 수정해 다시 검색할 수 있다.

### 13. 검색한 논문의 Reference를 따라 추가 Paper를 검색하는 등 Research Path를 능동적으로 확장할 수 있다.

### 14. Deep Research는 완전히 새로운 Agent 기술이라기보다 Reasoning, Planning, Tool, RAG, Evaluation 등 기존 Agent Component를 Research Domain에 맞게 통합한 것으로 볼 수 있다.

### 15. Medical, Legal 등 다른 Domain에서는 동일 Component를 해당 Domain Workflow에 맞게 다시 구성해야 한다.

### 16. Domain Scope를 좁히면 Search Result가 Relevant한지 판단하기 쉬워진다.

### 17. 다만 Domain을 좁힌다고 Retrieval Accuracy를 완벽하게 판별할 수 있는 것은 아니며 오류를 감지할 가능성이 높아진다고 이해해야 한다.

### 18. Retrieval Error를 감지할 수 있으면 Agent는 재검색하거나 다른 Tool을 선택할 수 있다.

### 19. Domain을 좁히면 Training Data와 Behavior 범위를 제한할 수 있어 Adaptability를 높이기 쉬워진다.

### 20. 모든 General Knowledge를 높은 수준으로 유지할 필요가 없기 때문에 Resource도 집중할 수 있다.

### 21. Domain-specific Model/Agent는 Prompt, Fine-tuning, Reinforcement Learning 등 다양한 방법으로 만들 수 있다.

### 22. Test-time Compute도 Domain 특성에 맞게 조절할 수 있다.

### 23. 항상 복잡한 문제가 들어오는 Domain이라면 매번 Fast/Deep Reasoning을 Routing할 필요 없이 Deep Reasoning을 기본으로 사용할 수 있다.

### 24. 반대로 간단한 문제만 들어오는 Domain에서는 Long Reasoning에 Inference Resource를 낭비할 필요가 없다.

### 25. Domain을 좁히면 Agent System이 실시간으로 판단해야 하는 변수를 줄일 수 있다.

### 26. Deep Research에서는 Search와 Execution보다 앞단에서 문제와 논리를 반복적으로 검증하는 Reasoning Loop가 특히 중요하다.

### 27. 좋은 Research는 잘못된 문제를 빠르게 푸는 것이 아니라 먼저 올바른 문제를 정의하는 것이 중요하다는 것이 강의의 관점이다.

### 28. 따라서 Research Agent에서는 Problem Definition, Search, Self-evaluation, Refinement 같은 Iteration을 Workflow 앞단에 많이 배치할 수 있다.

### 29. Problem과 Plan이 충분히 정의된 뒤에는 Paper Retrieval, Experiment 등의 Execution으로 넘어갈 수 있다.

### 30. 다른 Domain에서는 Iteration을 배치해야 하는 위치가 달라질 수 있다.

### 31. Domain-Specific Agent의 핵심은 같은 Agent Component를 Domain Workflow에 맞게 재배치하고 조합하는 것이다.

### 32. 실제 Agent Product에서는 Agent Architecture뿐 아니라 Crawling, Serving, Network, Rate Limit 등의 System Engineering도 중요하다.

### 33. Agent가 Web Search를 계획해도 Website가 Crawling을 차단하면 실제 Task는 실패할 수 있다.

### 34. 따라서 Model, Agent Architecture, Software/System을 함께 이해해야 한다.

### 35. AI가 구현의 많은 부분을 대신하더라도 어떤 문제를 풀고 어떤 방향으로 AI를 사용할지 Human이 Guide해야 한다.

### 36. 좋은 Guidance를 위해서는 Domain과 AI뿐 아니라 System과 Software 등에 대한 넓은 이해가 중요할 수 있다.

### 37. LLM을 사용한다고 자동으로 좋은 Research나 Output이 만들어지는 것은 아니다.

### 38. 뛰어난 Human이 LLM에 좋은 방향을 주고 결과를 평가할 때 더 높은 Output을 만들 수 있다는 것이 강의의 관점이다.

### 39. 특정 Domain에 맞게 Agent를 최적화할 때 Domain-specific Reward를 이용한 Reinforcement Learning도 사용할 수 있다.

### 40. Domain이 좁으면 Final Output이 해당 Domain에서 필요한 결과인지 Self-evaluation하기 쉬워진다.

### 41. Agent 결과를 다른 Agent나 Domain Rule로 Cross-validation할 수도 있다.

### 42. Domain-Specific Agent는 Confidence가 낮거나 Out-of-domain인 경우 무리해서 Answer를 만들지 않고 Human에게 Escalate하도록 설계할 수 있다.

### 43. 특히 Medical·Legal·Finance 같은 High-stakes Domain에서는 Human Intervention이 중요한 Safety Mechanism이 될 수 있다.

### 44. Domain Boundary를 명확히 하면 In-domain과 Out-of-domain Task를 구분하기 쉬워진다.

### 45. Domain-Specific Agent는 좁은 범위 안에서 Reliability, Efficiency, Safety를 높이는 것을 목표로 한다.

### 46. Agent는 개념적으로는 단순해 보이지만 좋은 Agent System을 만드는 것은 매우 어렵다는 것이 강의의 평가다.

### 47. Agent Architecture와 방법론은 아직 빠르게 발전 중이므로 자신의 Domain에 어떤 기술이 적용되는지 지속적으로 살펴볼 필요가 있다.

### 48. Agent Engineering에서는 실제 구현과 실패를 통해 얻은 Domain-specific Experience가 중요한 전문성이 될 수 있다.

### 49. Agent에는 아직 Evaluation, Debugging, Error Propagation, Multi-Agent Management, Token Cost, Latency 등의 기술 문제가 남아 있다.

### 50. Multi-Agent에서는 한 Agent의 잘못된 정보가 다른 Agent에게 전파될 수 있다.

### 51. Agent 수가 많아질수록 Communication과 Management Complexity 및 Cost가 증가한다.

### 52. 좋은 Agent System은 Accuracy뿐 아니라 Cost, Latency, Reliability, Maintainability까지 함께 고려해야 한다.

### 53. 강의 마지막에서는 장기적으로 Agent가 Human이나 Robot에게 Task를 Dispatch하는 시대가 올 수 있다는 강사의 미래 전망을 소개한다.

### 54. 강사는 학습자가 AI를 단순히 사용하는 사람을 넘어 AI Model이나 AI Agent를 직접 만드는 사람으로 발전할 것을 권한다.

### 55. Domain-Specific Agent를 실제로 만들고 최적화한 경험은 중요한 전문성이 될 수 있다는 것이 강의의 최종 메시지다.

---

# 143. 이번 강의 전체 논리

```text
Agent에 필요한 요소를
지금까지 모두 배웠다.

LLM
Multi-Agent
Memory
Tool
Reasoning
Planning
      ↓
이제 복잡하고 긴 문제를
풀 수 있다.


그런데
하나의 Agent가
세상의 모든 문제를
잘해야 할까?
      ↓
아니다.


현실에서 필요한 것은
      ↓
우리 회사

우리 Domain

우리 업무

를 잘하는 Agent


      ↓
Domain-Specific Agent


Domain을 좁히면
      ↓
Relevant Knowledge가 명확

Tool이 명확

Data가 명확

Evaluation이 명확

Resource도 집중 가능


대표 Example
      ↓
Deep Research


Web Search
↓
LLM
↓
RAG
↓
Deep Research


Deep Research는
한 번 검색하고 끝나지 않는다.
      ↓

Think
↓
Search
↓
Evaluate
↓
Think Again
↓
Search Again


즉
Agent Autonomy를
Research Workflow에 적용


그리고
Research라는 Domain이므로
      ↓
앞단에서
Problem Definition과
Reasoning을 많이 반복

      ↓
문제가 잘 정의되면
Execution


다른 Domain이라면
이 구조도 다시 바뀐다.
      ↓
Domain-Specific Architecture


Domain Agent를 만들 때
      ↓
Prompt

Fine-tuning

RL

RAG

Memory

Tools

Test-time Scaling

을 조합


Domain을 좁히면
Final Output도
평가하기 쉬워진다.
      ↓
Self-evaluation

Cross-validation

Human Escalation


하지만
Agent Architecture만
잘 만들면 끝?
      ↓
아니다.


Serving

Crawling

Network

Rate Limit

Cost

Latency

Debugging

까지 필요
      ↓
System Engineering


결국
좋은 Domain Agent
      =
좋은 Model
+
좋은 Domain Knowledge
+
좋은 Workflow
+
좋은 Tools
+
좋은 Evaluation
+
좋은 System
```

---

# 144. 이번 강의 핵심 한 문장

> **Domain-Specific AI Agent는 하나의 범용 Agent가 세상의 모든 문제를 해결하도록 만드는 대신 실제로 필요한 특정 산업·회사·전문 분야로 Problem Scope를 제한하고, 그 Domain의 Knowledge·Tool·Memory·Reasoning Pattern·Workflow·Evaluation·Safety Rule에 맞게 기존 Agent Component를 재조합하여 높은 Reliability와 Resource Efficiency를 얻으려는 접근이며, 강의의 대표 사례인 Deep Research Agent는 단순한 검색이나 RAG를 넘어 Research Question을 스스로 분석하고 필요한 자료를 반복적으로 검색·평가·재검색하며 문제와 가설을 정제한 뒤 실행 단계로 넘어가는 구조를 가지며, Domain을 제한함으로써 Retrieval Error와 Out-of-domain Output을 더 쉽게 판단하고 필요할 경우 Human에게 Escalate할 수 있다는 장점이 있지만 실제 Agent를 잘 만들기 위해서는 Model뿐 아니라 Evaluation·Debugging·Serving·Crawling·Cost·Latency 등 System Engineering까지 함께 고려해야 한다.**

가장 짧게 기억하면 다음과 같다.

```text
General Agent
= 모든 문제를
  잘하려고 한다.

Domain-Specific Agent
= 특정 문제를
  매우 잘하려고 한다.


Domain을 좁히면

Knowledge가 명확해지고

Tool이 명확해지고

Evaluation이 쉬워지고

Resource를 집중할 수 있다.


Deep Research
= 검색 한 번 하고 답하는 것이 아니라

생각
↓
검색
↓
평가
↓
다시 생각
↓
재검색

을 반복한다.


Domain-Specific Agent의 본질
= 새로운 Agent 기술을 만드는 것이 아니라

기존의
Reasoning
Planning
Memory
Tool
Multi-Agent

를

해당 Domain에 맞게
다시 조합하는 것이다.
```