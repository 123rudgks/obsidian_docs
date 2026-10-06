# 10. LLM 발전을 다시 돌아보기

강의에서는 Agent의 등장 배경을 이해하기 위해 Language Model의 발전을 복습한다.

Language Model의 기본 아이디어는 오래전부터 존재했다.

```text
Previous Tokens
↓
Next Token Prediction
```

이다.

---

# 11. 과거 Language Model의 두 가지 큰 문제

강의에서는 초기 Language Model 발전이 느렸던 이유를 크게 두 가지로 설명한다.

```text
1. 긴 문맥을 잘 기억하기 어려움

2. 대규모 Training의 Compute Cost가 매우 큼
```

---

# 12. 긴 Context 문제

문장이 길어질수록 이전 정보가 약해져

```text
초반 Context
↓
시간이 지나면서 손실
↓
뒤에서 이상한 Generation
```

이 발생할 수 있었다.

---

# 13. Compute 문제

대규모 Data를 Neural Network에 학습시키기 위한 Compute가 과거에는 너무 비쌌다.

GPU와 대규모 Computing Infrastructure의 발전은 이후 AI 발전의 중요한 기반이 된다.

---

# 14. Transformer

강의에서는 Transformer가 이러한 발전의 중요한 Turning Point였다고 설명한다.

Transformer 이후

```text
더 긴 Context

더 큰 Data

더 큰 Model

효율적인 Parallel Training
```

이 가능해지면서 Language Model Scaling이 본격화되었다.

---

# 15. 대규모 Internet Data

Web에는 이미 방대한

```text
Text

Image

기타 Digital Data
```

가 존재했다.

따라서

```text
Transformer
+
Large Compute
+
Large Internet Data
```

를 이용해 Model Scale을 빠르게 키울 수 있었다.

---

# 16. Scale과 Generalization

대규모 Data와 Model로 Training하면서 Model이 특정 Task 하나만 잘하는 것이 아니라

```text
Translation

QA

Summarization

Coding

General Knowledge
```

등 여러 Task에 Generalize하는 현상이 강하게 나타났다.

이것이 오늘날 LLM의 중요한 특징이다.

---

# 17. Multimodal로 확장

이후 Model은 Text만 처리하지 않는다.

```text
Text

Vision

Image

Audio

Sensor Data
```

등을 함께 다루는 Multimodal Model로 발전한다.

---

# 18. Physical AI와 LLM의 중요한 차이 — Data

강의에서는 Physical AI 발전 속도가 LLM보다 느릴 수 있는 중요한 이유로 **Data**를 든다.

LLM은 Web에 이미 엄청난 Text Data가 있었다.

반면 Robot 행동 Data는 Web에 자연스럽게 대량 축적되어 있지 않다.

---

# 19. Robot Data는 수집하기 어렵다

기존 방식에서는

```text
Human
↓
Robot 조종
↓
Behavior Data 수집
```

을 반복해야 한다.

즉

```text
Manual Labor

Time

Cost
```

가 많이 필요하다.

---

# 20. Physical AI의 핵심 Bottleneck 중 하나

따라서 Physical AI에서는

> **Robot Training Data를 얼마나 빠르고 저렴하게 대규모로 확보할 것인가?**

가 중요한 문제다.

강의에서는 해외에서 많은 인력을 활용해 Robot Teleoperation Data를 수집하는 Data Factory와 같은 사례도 소개한다.

---

# 21. 의료 사례로 Agent 필요성 이해하기
# 37. AI Agent의 정의

강의에서는 AI Agent를

> **AI/LLM을 이용하여 특정 Goal을 스스로 추구하는 Software System**

으로 설명한다.

보다 구체적으로는

```text
Goal 이해
↓
Planning
↓
Execution
↓
필요하면 Tool / Other Agent 사용
↓
Result Analysis
↓
Replanning
↓
다시 Execution
```

을 인간이 매 Step마다 직접 지시하지 않아도 반복한다.

---

# 38. 일반 LLM과 Agent의 가장 큰 차이

일반적인 LLM 사용:

```text
Human Prompt
↓
LLM
↓
Response
↓
끝
```

Agent:

```text
Goal
↓
Agent
↓
Perception
↓
Planning
↓
Action
↓
Environment 변화
↓
Evaluation
↓
Replanning
↓
Action
↓
...
↓
Goal 달성
```

이다.

---

# 39. Agent의 핵심 — Autonomy

강의에서 가장 중요하게 강조하는 Agent의 특징은 **Autonomy(자율성)** 다.

즉

> **User가 모든 행동을 하나하나 지시하지 않아도 목표를 보고 스스로 다음 행동을 결정한다.**

는 것이다.

---

# 40. “자동화”와 “자율성”은 미묘하게 다르다

단순 Automation은

```text
정해진 조건
↓
정해진 Action
```

을 수행한다.

Agent는

```text
현재 상황
+
Goal
↓
판단
↓
적절한 Action 선택
```

을 한다.

따라서 상황에 따라 행동이 달라질 수 있다.

---

# 41. 여행 Agent 비유

전통적인 `Agent`라는 단어 자체도

> **다른 사람이나 조직을 대신하여 행동하는 존재**

라는 의미를 가진다.

예를 들어 Travel Agent에게

```text
하와이 여행을 하고 싶다.
```

라고 Goal을 준다.

Travel Agent는 대신

```text
일정 계획

항공권 검색

숙박 예약

방문 장소 선정
```

등을 수행한다.

AI Agent도 구조적으로 비슷하다.

---

# 42. Washing Machine으로 Agent 이해하기

강의에서는 세탁기를 이용해 Agent와 Rule-based System의 차이를 설명한다.

일반 세탁기:

```text
User가 Mode 선택
↓
미리 정해진 Program 실행
↓
세탁
```

한다.

---

# 43. Rule-based Washing Machine

사용자가

```text
표준

울

강력

섬세
```

등의 Mode를 선택한다.

Machine은 미리 정의된 Sequence를 그대로 실행한다.

즉 Decision 자체를 User가 상당 부분 수행한다.

---

# 44. Agent Washing Machine

AI Agent가 들어간 세탁기는

```text
Camera / Sensor
↓
옷 상태 인식

Weight Sensor
↓
양 파악

Material 분석
↓
세탁 방법 판단
```

한 뒤 스스로

```text
세탁 Mode

시간

강도

물의 양
```

등을 결정할 수 있다.

---

# 45. Goal은 동일하다

두 세탁기 모두

```text
Goal = 옷을 깨끗하게 세탁한다.
```

이다.

차이는

```text
Goal 달성 방법을 누가 결정하는가?
```

이다.

일반 세탁기:

```text
Human
```

Agent 세탁기:

```text
AI Agent
```

가 더 많은 판단을 담당한다.

---

# 46. AI 발전을 네 단계로 보기

강의에서는 현대 AI의 발전을 크게 네 축으로 설명한다.

```text
Perception AI
↓
Generative AI
↓
Agentic AI
↓
Physical AI
```

---

# 47. Perception AI

먼저 AI가 환경을 **인식**하는 능력이 발전했다.

예:

```text
Computer Vision

Speech Recognition
```

이다.

---

# 48. 왜 Perception이 먼저 필요한가?

사람도 행동하기 전에 세상을 이해해야 한다.

AI도

```text
Environment
↓
Perception
↓
Understanding
```

이 먼저 필요하다.

강의에서는 Perception 기술 발전을 이후 AI 발전의 기반으로 본다.

---

# 49. Generative AI

Perception만으로는 부족하다.

인식한 정보를 기반으로

```text
Text

Image

Audio

Idea
```

등을 만들어야 한다.

Generative AI는 이러한 **Generation Capability**를 제공한다.

---

# 50. Generative AI의 한계 — Passive

하지만 Generative AI는 여전히 기본적으로

```text
User Request
↓
Generation
```

이다.

즉 User가 먼저 요청해야 Response한다.

강의에서는 이것을 수동적인 구조라고 설명한다.

---

# 51. Agentic AI

Agentic AI는 여기서 한 단계 더 나아간다.

```text
Goal
↓
Agent 스스로 판단
↓
필요한 행동 결정
↓
Action
```

한다.

즉 **능동적이고 자율적인 AI**다.

---

# 52. Physical AI

Agentic AI가 Digital Environment를 넘어

```text
Robot

IoT

Vehicle

Factory Machine
```

등과 연결되어 현실 세계에서 행동하면 Physical AI로 확장할 수 있다.

---

# 53. 네 기술은 대체 관계가 아니다

중요하다.

```text
Physical AI가 등장
→ Agentic AI 필요 없음
```

이 아니다.

오히려 강의에서는 계층적으로 본다.

```text
Physical AI
      ↑
Agentic AI
      ↑
Generative AI
      ↑
Perception AI
```

즉 상위 단계가 아래 Capability를 활용한다.

---

# 54. AI Scientist Example

강의에서는 Agent Capability를 체감하게 한 사례로 **AI Scientist**를 소개한다.

연구자가 논문을 완성하려면 일반적으로

```text
Literature Search
↓
Research Question
↓
Novelty Check
↓
Hypothesis
↓
Experiment Design
↓
Coding
↓
Experiments
↓
Analysis
↓
Paper Writing
↓
Review
↓
Revision
```

이라는 긴 Process를 거친다.

---

# 55. 이것은 Agent Task에 적합한 구조다

하나의 Generation으로 해결되는 Task가 아니다.

```text
Search

Planning

Coding

Experiment

Evaluation

Revision
```

등이 반복된다.

즉 전형적인 **Long-horizon Agent Task**다.

---

# 56. AI Scientist 연구 사례의 의미

강의에서는 AI Agent가 연구 아이디어부터 Experiment와 Paper 작성까지 이어지는 Workflow를 수행하고, 그 결과를 실제 Workshop Review에 제출해 평가받은 사례를 소개한다.

핵심은 특정 Acceptance 숫자를 외우는 것이 아니다.

> **“Agent가 단순 Writing Assistant를 넘어 긴 Research Process 자체를 수행하려는 단계까지 발전하고 있다.”**

는 점이다.

---

# 57. AI Agent의 여섯 가지 핵심 특징

강의에서는 Agent를 설명하기 위해 다음 **여섯 가지 특징**을 제시한다.

```text
1. Autonomy

2. Perception

3. Memory

4. Reasoning & Planning

5. Learning & Adaptation

6. Tool Use
```

이 여섯 요소가 잘 결합되면서 강력한 Agent System이 만들어진다.

---

# 58. ① Autonomy — 자율성

가장 중요한 특징이다.

> **Human이 모든 Step을 직접 지시하지 않아도 Goal을 보고 스스로 계획하고 행동한다.**

---

# 59. 숨겨진 Intent까지 추론

예를 들어 User가

```text
연구실 인턴 지원 이메일을 작성해 줘.
```

라고만 했다고 하자.

User가

```text
정중하게 써.

교수에게 보내는 Mail이야.

Professional Tone으로 써.
```

를 하나하나 지정하지 않아도 Agent가 Context를 보고 이것을 추론할 수 있다.

---

# 60. Autonomy의 핵심

```text
Explicit Instruction만 수행
```

하는 것이 아니라

```text
Goal
+
Context
↓
Implicit Intent 추론
↓
필요한 Action 결정
```

한다.

---

# 61. ② Perception — 인식

Agent가 행동하기 전에

> **현재 상황을 이해하고 필요한 정보를 확보하는 능력**

이다.

---

# 62. Internship Email 예

Agent가 Email을 작성하기 전에

```text
Application Deadline은?

Resume는 최신인가?

연구실이 원하는 Skill은?

Professor는 누구인가?
```

등을 파악할 수 있다.

---

# 63. Perception은 External Search까지 확장된다

필요한 정보가 Context에 없다면

```text
Website Search

Document Search

Web Search
```

를 수행하여 Environment를 추가로 파악할 수 있다.

즉 Perception은 단순 Sensor Input만 의미하지 않는다.

---

# 64. Agent에서 Perception의 역할

```text
Environment
↓
Information 수집
↓
Current State 이해
↓
Planning
```

의 앞단에 위치한다.

---

# 65. ③ Memory — 기억

Agent는 과거 Interaction을 활용할 수 있어야 한다.

예를 들어 오랫동안 사용한 AI Service는 User가 매번

```text
나는 누구이고

무슨 일을 하고

어떤 Preference를 가지고 있다.
```

를 다시 설명하지 않아도 이전 Context를 활용할 수 있다.

---

# 66. Memory가 중요한 이유

Agent가 매번 모든 것을 처음부터 시작하면

```text
Context 반복 입력

User Effort 증가

Personalization 부족
```

이 발생한다.

Memory가 있으면

```text
Past Interaction
↓
Current Decision에 활용
```

할 수 있다.

---

# 67. Memory의 더 어려운 문제 — 무엇을 기억할 것인가?

모든 것을 영원히 기억하는 것도 좋은 System은 아니다.

Agent는

```text
무엇을 기억할까?

무엇을 잊을까?
```

를 결정해야 한다.

---

# 68. Forgetting

사람도 시간이 지나면 모든 일을 동일하게 기억하지 않는다.

```text
중요하지 않은 정보
→ 점점 잊음

중요한 정보
→ 오래 기억
```

할 수 있다.

Agent Memory에서도 **Forgetting** 자체가 중요한 연구 문제다.

---

# 69. Short-term / Long-term Memory

초기적인 방법으로

```text
Short-term Memory
Long-term Memory
```

를 구분할 수 있다.

최근 정보는 Short-term에 유지하고 시간에 따라 일부 정보를 약화시키거나 Long-term으로 이동시키는 식이다.

---

# 70. 시간만으로 Memory Importance를 결정할 수는 없다

하지만 사람의 기억을 생각하면

```text
오래됨
=
무조건 중요하지 않음
```

은 아니다.

아주 오래된 정보도 중요하면 계속 기억한다.

따라서 Agent Memory에서는

```text
Recency

Importance

Frequency

Context
```

등을 함께 고려하는 방향의 연구가 필요하다.

---

# 71. ④ Reasoning & Planning

Agent의 가장 중요한 목적 중 하나는

> **Single LLM Call로 해결하기 어려운 복잡하고 긴 문제를 작은 문제로 나누어 해결하는 것**

이다.

---

# 72. Task Decomposition

```text
Complex Goal
↓
Subtask A
↓
Subtask B
↓
Subtask C
↓
...
↓
Goal
```

로 분해한다.

---

# 73. Planning

분해만 하는 것이 아니라

```text
무엇을 먼저 할지

무엇이 Dependency인지

어떤 순서로 할지

어떤 Tool을 사용할지
```

를 정한다.

---

# 74. Internship Example

```text
Goal:
Internship Application Email 작성
```

Agent가 다음처럼 Plan을 만들 수 있다.

```text
1. Lab Website 확인

2. Internship 공고 확인

3. 필요한 Qualification 분석

4. Candidate Resume 확인

5. Resume와 Qualification 연결

6. Professional Email 작성
```

즉 단순 Text Generation보다 더 복잡한 Process다.

---

# 75. Reasoning & Planning의 핵심

```text
복잡한 Goal
↓
Reasoning
↓
작은 Unit으로 분해
↓
Execution Order 설계
↓
Action
```

이다.

---

# 76. ⑤ Learning & Adaptation

Agent는 실행 후 Feedback을 이용해 행동을 수정할 수 있다.

---

# 77. Self-evaluation

예:

```text
Draft Email 생성
↓
Agent가 스스로 평가
↓
"너무 비전문적이다."
↓
다시 수정
```

할 수 있다.

---

# 78. User Feedback

Agent가 확신하지 못한다면 User에게 추가 정보를 요청할 수도 있다.

```text
정보 부족
↓
Clarifying Question
↓
User Feedback
↓
Plan Update
↓
Better Result
```

이다.

---

# 79. Learning과 Adaptation의 의미

반드시 매번 Neural Network Weight를 Update한다는 뜻은 아니다.

넓은 Agent 관점에서는

```text
Feedback

Self-evaluation

Memory

Replanning
```

을 이용해 현재 행동을 개선하는 것도 포함된다.

---

# 80. Agent는 한 번 실패했다고 끝나지 않는다

```text
Attempt
↓
Evaluation
↓
Problem 발견
↓
Replanning
↓
Retry
```

가 가능하다.

이것이 단순 LLM Call과 Agent System의 중요한 차이다.

---

# 81. ⑥ Tool Use

LLM이 직접 모든 작업을 수행할 필요는 없다.

필요하면 External Tool을 호출한다.

---

# 82. Tool의 예

```text
Search

Calculator

Database

Gmail

Notion

Slack

Code Executor

External API
```

등이다.

---

# 83. Email Agent 예

Agent가 Email 초안을 만든 뒤

```text
이제 Gmail로 보낼까요?
```

라고 할 수 있다.

User가 승인하면

```text
LLM
↓
Gmail Tool Call
↓
실제 Email Send
```

가 가능하다.

---

# 84. Tool Use의 의미

이때 Agent는 Text를 생성하는 데서 끝나지 않는다.

```text
Decision
↓
External Software Call
↓
Real Action
```

까지 수행한다.

---

# 85. Tool이 Agent Capability를 확장한다

```text
LLM 자체 Capability
+
External Tool Capability
↓
Agent Capability
```

가 된다.

따라서 Agent는 자신이 직접 잘하지 못하는 일을 다른 Tool에 맡길 수 있다.

---

# 86. 여섯 특징을 하나의 Loop로 연결

Agent의 여섯 특징은 서로 독립된 기능 목록이 아니다.

다음처럼 하나의 Process가 된다.

```text
Goal
↓
Autonomy
→ 무엇을 해야 하는지 스스로 판단

↓
Perception
→ 현재 상황과 정보 파악

↓
Memory
→ 과거 Context와 Experience 활용

↓
Reasoning & Planning
→ Goal을 Subtask로 분해

↓
Tool Use
→ 필요한 External Capability 실행

↓
Learning & Adaptation
→ 결과 평가 및 수정

↓
다시 Perception / Planning
↓
Goal 달성까지 반복
```

---

