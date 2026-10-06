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

