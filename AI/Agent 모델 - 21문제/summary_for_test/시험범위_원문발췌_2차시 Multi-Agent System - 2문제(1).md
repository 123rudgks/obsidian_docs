# 9. 그래서 Multi-Agent System

하나의 Agent Capability에만 의존하지 않고 여러 Agent를 하나의 Team처럼 사용한다.

강의에서는 Multi-Agent System을

> **여러 Agent가 협력하고 Knowledge를 공유하며 상호작용하여 개별 Agent 능력의 단순 합보다 높은 Collective Intelligence를 만드는 System**

으로 설명한다.

---

# 10. Vertical Scaling vs Horizontal Scaling

하나의 Model을 계속 크게 만드는 것은

```text
Vertical Scaling
```

에 가깝다.

반면

```text
Agent A
Agent B
Agent C
Agent D
```

를 조직적으로 연결하는 것은

```text
Horizontal Scaling
```

관점으로 이해할 수 있다.

---

# 11. Multi-Agent의 목표

단순히

```text
Agent 4개
=
Capability 4배
```

가 아니다.

목표는 Agent 간 Interaction에서 **Synergy**가 발생하도록 만드는 것이다.

```text
개별 Agent 능력
+
역할 분담
+
검증
+
토론
+
Knowledge Sharing
↓
Collective Intelligence
```

이다.

---

# 12. 인간 조직과 유사하다

강의에서는 인간 사회에서도

```text
한 명의 천재
```

보다

```text
여러 전문가가
서로 협업
```

하여 더 복잡한 문제를 해결하는 경우가 많다고 설명한다.

예:

```text
Multidisciplinary Medicine

Research Team

Company

Government

Engineering Team
```

등이다.

Multi-Agent System은 이러한 인간 조직의 구조를 AI Agent로 모사하려는 방향이다.

---

# 13. 가장 단순한 Multi-Agent 예

어떤 주장에 대한 의견을 구한다고 하자.

```text
User Question
      ↓
Supporting Agent
+
Opposing Agent
      ↓
Debate
      ↓
Better Conclusion
```

처럼 만들 수 있다.

---

# 14. Supporting Agent

User의 주장에 대해

```text
찬성하는 Evidence

주장을 지지하는 Logic
```

을 만드는 역할이다.

---

# 15. Opposing Agent

반대 입장에서

```text
반론

Weakness

Counter Evidence
```

를 찾는다.

---

# 16. Debate

두 Agent가 서로 상반된 관점에서 Argument를 생성한다.

```text
Supporting
      ↕
Debate
      ↕
Opposing
```

목표는 한쪽을 무조건 승리시키는 것보다는 다양한 관점을 검토하여 더 좋은 결론을 만드는 것이다.

---

# 17. Agent가 다른 Agent에게 일을 위임할 수도 있다

Opposing Agent가 자신의 Knowledge만으로 충분한 근거를 만들 수 없다고 하자.

그러면

```text
Opposing Agent
├─ Web Search Agent
├─ Paper Research Agent
└─ Library Search Agent
```

에게 Research를 요청할 수 있다.

---

# 18. Collaboration + Competition

이 구조에는 동시에 두 관계가 존재한다.

### Supporting ↔ Opposing

```text
Competition / Debate
```

### Opposing ↔ Research Agents

```text
Collaboration
```

이다.

즉 실제 Multi-Agent System에서는 협력과 경쟁이 동시에 존재할 수 있다.

---

# 19. Multi-Agent Architecture에는 정답이 없다

강의에서 반복해서 강조한다.

```text
이 Architecture가
무조건 정답
```

이라는 것은 없다.

어떤 Domain에서는 잘 동작한 구조가 다른 Domain에서는 실패할 수 있다.

따라서 Agent System에서는

> **실제로 구축하고 Experiment하면서 어떤 구조가 효과적인지 확인하는 Experience가 중요하다.**

는 것이 강의의 중요한 메시지다.

---

# 20. Agent Engineering은 경험 의존성이 크다

전통 Software에서도

```text
OS Expert

Database Expert

Network Expert
```

처럼 Experience가 중요한 전문 영역이 존재했다.

강의에서는 미래에는

```text
AI Agent Architecture를
잘 설계하는 전문가
```

역시 별도의 전문성이 될 가능성을 언급한다.

---

# 21. Multi-Agent에서 가장 먼저 필요한 것 — 역할

여러 Agent를 사용한다고 해도 모두 같은 행동을 하면 의미가 줄어든다.

따라서

```text
Agent A의 역할

Agent B의 역할

Agent C의 역할
```

을 어떻게 만들 것인지가 중요하다.

---

# 22. 가장 쉬운 Role Assignment — Prompting

같은 LLM을 여러 번 사용하면서 System Prompt만 다르게 줄 수 있다.

예:

```text
Agent A:
"너는 이 주장에 항상 찬성하는 Agent다."

Agent B:
"너는 이 주장에 비판적인 Agent다."
```

이다.

이를 **Role Playing / Role Prompting**으로 볼 수 있다.

---

# 23. 같은 Backbone으로 여러 Agent를 만들 수 있다

```text
Same LLM
├─ Prompt A → Supporting Agent
├─ Prompt B → Opposing Agent
└─ Prompt C → Research Agent
```

처럼 사용할 수 있다.

즉 Agent 수가 여러 개라고 반드시 서로 다른 Model을 사용해야 하는 것은 아니다.

---

# 24. 반대로 서로 다른 LLM을 사용할 수도 있다

```text
Agent A → Model A

Agent B → Model B

Agent C → Model C
```

처럼 Domain이나 역할에 따라 서로 다른 Backbone을 사용할 수도 있다.

---

# 25. Role Prompting의 장점

```text
Training 필요 없음

구현이 빠름

Cost 낮음

Role 변경 쉬움
```

하다.

---

# 26. Role Prompting의 한계

Prompt로

```text
"너는 항상 반대해."
```

라고 했더라도 Model이 항상 완벽하게 그 Role을 유지한다고 보장할 수 없다.

Complex Context에서는 Role이 흐려질 수도 있다.

---

# 27. 더 강한 Role Specialization — Training

Role을 더 안정적으로 유지시키고 싶다면

```text
Role-specific Data
↓
Fine-tuning
```

할 수 있다.

예:

```text
Supporting Behavior Data
↓
Supporting Agent

Opposing Behavior Data
↓
Opposing Agent
```

처럼 별도의 Model로 학습한다.

---

# 28. Training의 Trade-off

장점:

```text
Role Consistency ↑
Specialization ↑
```

단점:

```text
Data 필요

Training Cost

Time

Model Management 복잡성
```

이 커진다.

---

# 29. Value Steering

강의에서는 최근 연구 방향으로 **Value Steering / Value Alignment**을 소개한다.

단순한

```text
"너는 이런 사람이다."
```

Prompt를 넘어서

```text
Model이 특정 가치관과
행동 방향을 일관되게 따르도록
Steering
```

하는 것이다.

---

# 30. 왜 Value Steering이 필요한가?

예를 들어 특정 Social/Political Perspective를 가진 Agent를 Simulation한다고 하자.

Prompt만으로

```text
Agent A = Perspective A

Agent B = Perspective B
```

라고 지정할 수도 있다.

하지만 Role Consistency가 불완전할 수 있다.

그래서 특정 Value를 비교적 낮은 Cost로 안정적으로 Steering하려는 연구가 진행된다는 것이 강의 설명이다.

---

# 31. Value Steering의 활용 가능성

사람들의 다양한 가치관을 Agent Population으로 Modeling할 수 있다면

```text
Policy Proposal
↓
Thousands of Agents
↓
각 Agent Reaction
↓
Aggregate Simulation
```

처럼 정책 반응을 Simulation하는 가능성을 강의에서 예로 든다.

---

# 32. 하지만 최종 Decision은 별개의 문제

강의에서는 이러한 Simulation이 가능하더라도 실제 사회의 정책 Decision을 완전히 AI에게 맡기는 것은 위험할 수 있다는 개인적 의견도 언급한다.

즉

```text
Agent Simulation
→ Decision Support

Final Social Decision
→ Human Confirmation
```

과 같은 구분이다.

---

# 33. Multi-Agent System을 설계하는 네 가지 구성요소

강의에서는 Multi-Agent System Architecture를 설계할 때 크게 네 요소를 생각한다.

```text
1. Agent

2. Environment

3. Interaction

4. Organization
```

이다.

---

# 34. 구성요소 ① Agent

실제 행동을 수행하는 행위자다.

Agent마다

```text
Role

Capability

Behavior

Knowledge

Model

Tools
```

등을 정의할 수 있다.

---

# 35. Agent 설계 질문

예:

```text
몇 개의 Agent가 필요한가?

각각 어떤 역할인가?

같은 LLM을 사용할까?

다른 LLM을 사용할까?

어떤 Tool을 제공할까?

어떤 Knowledge를 줄까?
```

등이다.

---

# 36. 구성요소 ② Environment

Agent가 활동하는 공간이다.

예:

```text
Medical Domain

Classroom

Company

Software Development

Game

Financial Market
```

등이다.

---

# 37. Environment Constraint

강의에서는 Environment를 제한하는 것이 중요하다고 설명한다.

```text
범용 Environment
→ Generality ↑
→ 깊은 Accuracy 확보 어려움

Specific Environment
→ Domain 범위 ↓
→ 정확한 Agent 설계 가능성 ↑
```

이다.

---

# 38. Domain Boundary

예를 들어

```text
"이 Agent는
Medical Environment에서만 동작한다."
```

고 제한하면

- Knowledge
    
- Tool
    
- Rules
    
- Agent Role
    

을 해당 Domain에 맞게 설계하기 쉬워진다.

---

# 39. 범용성과 전문성의 Trade-off

```text
General Agent
→ 폭넓음
→ 깊이 부족 가능

Domain-specific Agent
→ 범위 좁음
→ 높은 Reliability 가능
```

이라는 구조다.

---

# 40. 구성요소 ③ Interaction

Agent가 서로 어떻게 Communication하는지 정의한다.

예:

```text
협력

경쟁

토론

질문-답변

Task Delegation

Information Sharing
```

등이다.

---

# 41. Interaction Protocol

단순히 Agent를 여러 개 실행하는 것만으로 Multi-Agent System이 완성되는 것이 아니다.

```text
누가 먼저 말하는가?

누구에게 질문할 수 있는가?

몇 번 Communication하는가?

누가 답변을 전달받는가?
```

등을 정해야 한다.

---

# 42. 구성요소 ④ Organization

Agent가 여러 명이면 사회적 구조가 필요할 수 있다.

```text
Flat

Hierarchical

Centralized

Decentralized
```

등이다.

---

# 43. Startup 조직 예

```text
CEO
↓
CTO
↓
Team Lead
↓
Developer
```

같은 Hierarchy를 Agent System으로 구성할 수 있다.

---

# 44. 모든 Agent의 권한이 동일하지 않을 수 있다

CEO Agent와 일반 Employee Agent가 동일한 Decision Weight를 가져야 하는 것은 아니다.

따라서

```text
Authority

Decision Weight

Permission
```

을 역할에 따라 다르게 줄 수 있다.

---

# 45. Organization을 잘못 설계하면

예를 들어 원래 CEO Agent가 최종 판단을 내려야 하는데 일반 Agent의 의견이 더 크게 반영되면 원하는 조직 Simulation과 달라진다.

따라서

> **Agent의 Hierarchy와 Authority를 System Architecture에 정확히 반영해야 한다.**

---

# 46. 네 요소를 다시 정리

```text
Agent
= 누가 행동하는가?

Environment
= 어디에서 행동하는가?

Interaction
= 서로 어떻게 소통하는가?

Organization
= 누가 어떤 위치와 권한을 갖는가?
```

이다.

---

# 47. 같은 Example을 네 요소로 분해

Supporting/Opposing System을 보면

### Agents

```text
Supporting Agent
Opposing Agent
Research Agent
```

### Environment

```text
User Question
+
Context / Search Environment
```

### Interaction

```text
Supporting ↔ Opposing
= Debate

Opposing ↔ Research
= Collaboration
```

### Organization

```text
Supporting ≈ Opposing
= 비슷한 위치

Opposing > Research
= Hierarchical
```

처럼 볼 수 있다.

---

# 48. Multi-Agent Interaction 유형 ① Cooperation

여러 Agent가 **공통 목표**를 위해 역할을 나누는 구조다.

```text
Shared Goal
      ↓
Agent A
Agent B
Agent C
      ↓
Role Specialization
      ↓
Combined Result
```

---

# 49. Cooperation 예

건축 프로젝트:

```text
Architect Agent

Designer Agent

Engineer Agent
      ↓
Building Design
```

처럼 각 Agent가 자신의 전문성을 이용해 하나의 결과물을 만든다.

---

# 50. Cooperation의 장점

모든 Agent의 Goal이 같으므로

```text
Goal Alignment ↑
↓
문제 해결 속도 ↑
```

가능성이 있다.

그리고 전문성을 분리할 수 있다.

---

# 51. Cooperation의 단점 — Communication Overhead

Agent 수가 많아지면

```text
Agent ↔ Agent Communication
```

도 증가한다.

따라서

```text
Token Cost

Latency

System Complexity
```

가 증가한다.

---

# 52. Cooperation의 단점 — Hallucination Propagation

한 Agent가 잘못된 정보를 전달했다고 하자.

다른 Agent들이 같은 Team이라는 이유로 비판적으로 검증하지 않고 받아들이면

```text
Hallucination
↓
Shared Information
↓
다른 Agent도 사용
↓
전체 System으로 전파
```

될 수 있다.

---

# 53. Cooperative Groupthink

강의의 직관을 정리하면

```text
"우리 팀 Agent가 말했으니
일단 신뢰한다."
```

같은 구조가 생길 수 있다.

이 경우 잘못된 정보의 Debugging도 어렵다.

---

# 54. Multi-Agent Interaction 유형 ② Competition

각 Agent가 상충되는 목표나 관점을 가진다.

```text
Agent A
vs
Agent B
```

구조다.

---

# 55. Competition의 장점 — Critical Evaluation

상대 Agent의 말을 기본적으로 검증하고 비판적으로 보게 된다.

```text
Claim
↓
Opponent 검증
↓
Counterargument
↓
Refinement
```

이 반복된다.

---

# 56. Competition의 장점 — Diversity

서로 다른 Strategy를 추구하므로

```text
Idea Diversity ↑

Strategy Diversity ↑

Creativity ↑
```

를 기대할 수 있다.

---

# 57. Competition의 장점 — Unexpected Solution

모든 Agent가 같은 방향으로 문제를 푸는 것보다 서로 이기려고 다른 Strategy를 만들면서 예상하지 못한 Solution을 찾을 가능성이 있다.

---

# 58. Competition의 단점 — Endless Debate

공통 Goal이 약하거나 합의 방식이 없으면

```text
Argument
↓
Counterargument
↓
Counter-counterargument
↓
...
```

가 무한히 반복될 수 있다.

---

# 59. End Condition이 중요

Competition Agent에는

```text
Turn Limit

Time Limit

Consensus Rule

Score Threshold

Moderator Decision
```

등의 종료 조건이 필요하다.

---

# 60. Moderator / Orchestrator

서로 경쟁하는 Agent 사이에

```text
Moderator Agent
```

를 둘 수 있다.

역할:

```text
토론 관리

논점 정리

종료 판단

최종 결론
```

등이다.

---

# 61. 인간 토론과 유사

Debate Program에도 사회자/Moderator가 존재한다.

Multi-Agent Debate에서도

```text
Debaters
+
Moderator
```

구조를 만들 수 있다.

---

# 62. Game Environment

강의에서는 경쟁 Agent를 연구하기 좋은 Environment로 Game을 언급한다.

예:

```text
Tic-tac-toe

Poker
```

등이다.

---

# 63. 왜 Game이 좋은가?

Game에는 일반적으로

```text
Rules

Action

Opponent

Win/Lose Condition
```

이 명확하다.

따라서 경쟁 Strategy와 Adaptation을 평가하기 쉽다.

---

# 64. Poker 사례

Poker에는 단순 Optimization뿐 아니라

```text
Bluffing

Opponent Modeling

Hidden Information
```

같은 Social/Strategic 요소도 존재한다.

따라서 Agent의 복잡한 전략 Capability를 평가할 수 있다.

---

# 65. Competition의 Trade-off

장점:

```text
Creativity

Critical Thinking

Diversity

Adaptation
```

단점:

```text
Resource Waste

Endless Loop

Slow Convergence
```

이다.

---

# 66. 가장 현실적인 방식 — Hybrid

강의에서는 협력이나 경쟁 한쪽만 고집하기보다 **둘을 함께 사용하는 Hybrid Structure**를 중요하게 설명한다.

```text
어떤 상황
→ Cooperation

다른 상황
→ Competition
```

이다.

---

# 67. 같은 Agent도 상황에 따라 관계가 바뀔 수 있다

예:

```text
Phase 1:
각 Agent가 아이디어 경쟁

Phase 2:
좋은 아이디어를 선택

Phase 3:
선택된 방향을 함께 협력해 구현
```

할 수 있다.

---

# 68. MoE와의 비유

강의에서는 이러한 구조를 **Mixture of Experts(MoE)** 와 비유한다.

MoE에서는 여러 Expert가 존재한다.

```text
Input
↓
Gating
↓
Relevant Experts 활성화
↓
Output
```

한다.

---

# 69. MoE의 핵심 직관

Model 전체 Capacity는 매우 크더라도 하나의 Query를 처리할 때 모든 Expert를 활성화할 필요는 없다.

예:

```text
Medical Query
↓
Medical Expert 활성화

Legal Query
↓
Legal Expert 활성화
```

처럼 처리할 수 있다.

---

# 70. Multi-Agent에서도 Gating

비슷하게 Agent System에서도

```text
Current Problem
↓
Routing / Gating
↓
협력할까?
경쟁할까?
어떤 Agent를 사용할까?
```

를 판단하게 할 수 있다.

---

# 71. Agent-level Mixture of Experts

개념적으로

```text
Agent Pool
├─ Medical Agent
├─ Research Agent
├─ Critic Agent
├─ Coding Agent
└─ Planner Agent

       ↓
Router
       ↓
Task에 맞는 Agent Combination
```

같은 구조다.

강의에서는 Agent System 역시 이런 방향으로 발전할 수 있다고 설명한다.

---

# 72. Multi-Agent 운영 전략 — Rule-based vs Role-based

강의에서는 Multi-Agent 전략을 또 다른 관점에서

```text
Rule-based

Role-based
```

로 구분한다.

---

# 73. Rule-based System

Agent들이 따라야 하는 Protocol과 Constraint를 명시적으로 정의한다.

```text
Rule 1

Rule 2

Rule 3
...
```

안에서 Agent가 행동한다.

---

# 74. 인간 사회의 법과 유사

인간도 완전한 자유 상태에서 행동하는 것이 아니라

```text
Law

Contract

Policy

Company Rule
```

안에서 행동한다.

Multi-Agent System도 동일한 방식으로 Rule을 줄 수 있다.

---

# 75. Agent의 Autonomy와 Rule은 반대 개념이 아니다

Agent가 Autonomous하다고

```text
아무 행동이나 자유롭게 한다.
```

는 뜻은 아니다.

실제 System에서는

```text
Allowed Actions

Forbidden Actions

Communication Rule

Decision Rule
```

등을 제한할 수 있다.

---

# 76. Rule을 구현하는 방법

강의에서는 세부 구현이 다양할 수 있다고 설명한다.

예:

```text
Prompt

RAG로 Rule Document 제공

Explicit Software Logic

Training

Reinforcement Learning
```

등이다.

중요한 것은 구현 방식이 아니라 **Rule이라는 Constraint가 존재한다**는 것이다.

---

# 77. Rule-based System의 장점 — Debugging

Agent는 Autonomous하기 때문에 Debugging이 매우 어렵다.

하지만 Rule이 명확하면

```text
이 행동은 Rule을 지켰는가?

이 Response는 Constraint를 위반했는가?
```

를 평가할 기준이 생긴다.

---

# 78. Legal Agent 예

법률 Agent System에

```text
Applicable Laws

Policies

Legal Constraints
```

를 명시해 둔다.

Agent가 법을 명백히 위반하는 Response를 생성했다면

```text
System Failure
```

를 판단할 수 있다.

---

# 79. Robust Agent System

강의에서는 Agent가 언제든 실패할 수 있다는 점을 인정하고

> **실패가 발생했을 때 발견하고 Debugging할 수 있는 System을 만드는 것도 Robustness의 중요한 요소**

라고 설명한다.

Rule은 이를 위한 기준점이 된다.

---

# 80. Role-based System

Rule 대신 각 Agent의 **역할**을 중심으로 행동 범위를 제한한다.

예:

```text
Surgeon Agent

Radiologist Agent

Pathologist Agent

Coordinator Agent
```

처럼 구성한다.

---

# 81. Role이 Context를 제한한다

Surgeon Agent는

```text
"나는 Surgeon으로서
이 문제를 판단한다."
```

는 Context를 가진다.

즉 Role 자체가

```text
어떤 Knowledge를 사용할지

어떤 관점으로 판단할지

어떤 Action을 해야 할지
```

를 제한한다.

---

# 82. Role-based는 Domain Expert에 적합

강의에서는 특히

```text
Medical

Scientific Research

Software Development
```

같이 실제로 여러 전문가가 역할 분담하는 Domain과 잘 맞는다고 설명한다.

---

# 83. 기본 Assumption

Role-based Multi-Agent에서는

> **각 Agent가 자신의 Role에 대해서는 충분히 전문적이다.**

라는 가정을 어느 정도 둔다.

그 전문성을 합쳐 더 좋은 결과를 만든다는 것이다.

---

# 84. Multidisciplinary Medicine

의료에서 실제로

```text
Surgeon

Physician

Radiologist

Pathologist
```

등이 함께 논의한다.

Multi-Agent System에서도 이를 그대로 Role로 Mapping할 수 있다.

---

# 85. Software Company Simulation

강의에서는 Software Development 과정도 Role-based Agent로 나눌 수 있다고 설명한다.

예:

```text
Project Manager
↓
System Designer
↓
Developer
↓
Evaluator
↓
Customer / User Evaluator
```

이다.

---

# 86. Software Project Multi-Agent

```text
PM Agent
→ Requirement와 Task 분해

Designer Agent
→ Architecture 설계

Developer Agent
→ Code 작성

Evaluator Agent
→ Test / Evaluation

Customer Agent
→ User 만족도 평가
```

처럼 역할을 나눈다.

---

# 87. 하나의 Agent가 모든 역할을 하지 않는다

Single Agent:

```text
기획
설계
Coding
Test
평가
```

를 모두 수행한다.

Multi-Agent:

```text
각 Agent
→ 하나의 전문 역할
```

로 분산한다.

---

# 88. Role Specialization의 기대 효과

```text
Context 집중

전문성 강화

Prompt 복잡도 감소

Responsibility 명확화
```

를 기대할 수 있다.

---

# 89. Multi-Agent System은 Self-evolving할 수 있다

강의에서는 Multi-Agent System을 한 번 만들고 끝나는 Static System으로 보지 않는다.

```text
Agent 실행
↓
Discussion / Action
↓
Success / Failure Data 축적
↓
Experience 분석
↓
Agent / System 개선
```

이 반복될 수 있다.

---

# 90. Experience Reuse

예를 들어 의료 Multi-Agent가 실제 Case를 처리하면서 Discussion Data가 쌓인다.

```text
Case₁
Case₂
Case₃
...
```

를 다음 Agent Training이나 Memory로 재사용할 수 있다.

---

# 91. System 자체가 점점 똑똑해질 수 있다

```text
Initial Agent System
↓
Deployment
↓
Experience
↓
Data Accumulation
↓
Learning
↓
Improved Agent System
```

이라는 Cycle이다.

---

# 92. 다른 Domain으로 Knowledge Transfer

한 Disease를 대상으로 만든 Multi-Agent Workflow에서 얻은 Experience와 Architecture가 다른 Disease나 Domain에서도 일부 재사용될 수 있다.

즉 Multi-Agent System 자체도 **Reusable Organizational Knowledge**를 만들 수 있다.

---

# 93. Multi-Agent의 구조적 Architecture

강의 후반에서는 조직 구조를 크게

```text
Centralized

Decentralized

Hybrid
```

로 설명한다.

---

# 94. Centralized Architecture

가운데 하나의 Central Agent가 존재한다.

```text
        Agent A
          ↓
Agent B → Central Agent ← Agent C
          ↑
        Agent D
```

Central Agent가

```text
Communication

Moderation

Integration

Final Decision
```

을 담당한다.

---

# 95. 판사 Example

```text
Prosecutor Agent
       ↓
    Judge Agent
       ↑
Defense Agent
```

에서 Judge Agent가 양쪽 주장을 듣고 최종 판단한다.

---

# 96. Centralized 구조의 장점

```text
Control 쉬움

Decision Process 명확

Stable Output

Debugging 상대적으로 쉬움
```

하다.

---

# 97. Centralized 구조의 단점 — Bottleneck

모든 Communication이나 Decision이 중앙 Agent를 거치면

```text
Central Agent Load ↑
↓
Latency ↑
```

할 수 있다.

---

# 98. Single Point of Failure

중앙 Agent가 실패하면 System 전체가 영향을 받는다.

```text
Central Agent Failure
↓
Entire System Failure
```

가능성이 있다.

이를 **Single Point of Failure** 관점으로 볼 수 있다.

---

# 99. Decentralized Architecture

중앙 Controller 없이 Agent끼리 직접 Communication한다.

```text
Agent A ↔ Agent B

  ↕          ↕

Agent C ↔ Agent D
```

이다.

---

# 100. Decentralized의 장점

```text
Autonomy ↑

Scalability ↑

Flexibility ↑

Adaptability ↑
```

를 기대할 수 있다.

---

# 101. Fault Tolerance

Agent 하나가 실패해도 다른 Agent가 문제를 보완할 가능성이 있다.

따라서 중앙 집중 구조보다 특정 Agent Failure에 강할 수 있다.

---

# 102. Decentralized의 단점

누가 어떤 판단을 했는지 추적하기 어렵다.

```text
Communication 복잡

Coordination 어려움

Debugging 어려움

Unexpected Interaction
```

문제가 발생할 수 있다.

---

# 103. Hybrid Organization

Centralized와 Decentralized의 장점을 함께 사용한다.

예:

```text
Local Teams
→ 내부적으로 Decentralized

Team Leaders
→ Central Coordinator와 Communication
```

같은 구조다.

---

# 104. 실제 인간 조직과 비슷하다

대부분의 인간 Organization도 완전한

```text
Centralized
```

또는 완전한

```text
Decentralized
```

구조만 사용하지 않는다.

Department 내부 자율성과 중앙 Management를 함께 사용한다.

Multi-Agent System도 Hybrid Structure가 현실적일 수 있다.

---

# 105. Cooperation / Competition / Organization은 서로 다른 축

헷갈리면 안 된다.

```text
Cooperation vs Competition
=
Agent 간 목표 관계

Centralized vs Decentralized
=
Agent Organization Structure
```

이다.

예를 들어

```text
Centralized Competition System
```

도 가능하다.

Judge Agent 아래 Prosecutor와 Defense Agent가 경쟁하는 구조다.

---

# 106. Rule / Role도 또 다른 축

```text
Rule-based vs Role-based
=
Agent Behavior를
어떻게 Constraint하는가?
```

에 대한 구분이다.

따라서 하나의 Multi-Agent System은

```text
Hybrid Competition/Cooperation
+
Role-based
+
Centralized
```

같이 여러 특성을 동시에 가질 수 있다.

---

