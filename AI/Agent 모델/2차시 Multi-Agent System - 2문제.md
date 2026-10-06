[강의 링크](https://www.youtube.com/watch?v=CkvA4o04fwU)

## 시험 범위에 포함되는 내용
| 시험 토픽             | 근거                                                                    |
| ----------------- | --------------------------------------------------------------------- |
| **다중 에이전트 시스템**   | 여러 Agent의 역할 분담, 협력·경쟁·토론·조직화                                         |
| **LLM Agent의 특성** | Agent의 Role, Capability, Behavior, Knowledge, Model, Tool 등을 구조적으로 정의 |
# 0. 강의 전체 개요

지금까지는 주로 **Single Agent**를 다뤘다.

```text
User / Environment
      ↓
Single Agent
      ↓
Reasoning
Planning
Tool Use
      ↓
Action
```

이번 강의에서는 여기서 한 단계 확장한다.

```text
Single Agent
      ↓
개별 LLM의 한계
      ↓
여러 Agent를 함께 사용
      ↓
역할 분담
협력
경쟁
토론
조직화
      ↓
Multi-Agent System
      ↓
Collective Intelligence
```

핵심 질문은 다음과 같다.

> **Agent를 여러 개 사용하면 왜 더 좋아질 수 있으며, 여러 Agent를 어떤 구조와 관계로 조직해야 하는가?**

---

# 1. AI Agent Harness

강의는 먼저 **Harness**라는 표현을 설명한다.

Agent를 만든다는 것은 단순히

```text
LLM에게
"이 일을 해."
```

라고 명령하는 것만을 의미하지 않는다.

실제로는

```text
어떤 LLM을 사용할까?

어떤 Tool을 제공할까?

언제 어떤 Tool을 쓰게 할까?

어떤 Memory를 줄까?

여러 Agent를 어떻게 연결할까?

누가 누구와 Communication할까?

누가 최종 Decision을 할까?
```

를 모두 설계해야 한다.

---

# 2. Harness의 의미

강의에서 Harness는

> **LLM과 Tool을 어떤 구조와 방식으로 사용하게 해야 Agent가 실제로 잘 동작하는지를 고민하고 구현한 System Architecture / Framework / Methodology**

정도로 설명된다.

즉

```text
LLM
+
Tools
+
Memory
+
Workflow
+
Agents
+
Rules
+
Organization
```

을 둘러싼 전체 System Design이다.

---

# 3. 왜 Harness가 중요한가?

LLM 자체는 계속 강해지고 있지만 완벽하지 않다.

강의에서는 대표적인 한계로

```text
Hallucination

Limited Capacity

Autoregressive Error Propagation

Scaling Cost

Latency / Efficiency
```

등을 든다.

---

# 4. 개별 LLM의 한계 ① Hallucination

아무리 성능이 좋은 LLM이라도 잘못된 내용을 생성할 수 있다.

따라서

```text
Single LLM Output
=
항상 신뢰 가능
```

하다고 볼 수 없다.

여러 Agent가

```text
검증
비판
검색
토론
```

하는 구조를 만들면 이러한 문제를 일부 보완할 수 있다.

---

# 5. 개별 LLM의 한계 ② Autoregressive Error Propagation

현재 대다수 LLM은 이전 Token을 기반으로 다음 Token을 생성하는 Autoregressive Generation을 사용한다.

```text
Token₁
↓
Token₂
↓
Token₃
↓
...
```

초반 Reasoning이나 판단이 잘못되면

```text
초기 오류
↓
이후 Context에 포함
↓
다음 Generation에도 영향
↓
오류 확대
```

될 수 있다.

강의에서는 이것을 Single LLM이 완벽하지 않은 구조적 이유 중 하나로 설명한다.

---

# 6. Autoregressive 방식만 가능한 것은 아니다

강의에서는 Generation 방식 자체도 Autoregressive만 존재하는 것은 아니라고 언급한다.

예를 들어 여러 Token을 동시에 생성한 뒤 반복적으로 정제하는 Diffusion 계열 Generation 방식도 연구되고 있다.

다만 강의 시점에서 Text LLM에서는 Autoregressive 방식이 여전히 가장 일반적인 구조라는 설명이다.

---

# 7. 개별 LLM의 한계 ③ Scaling

Scaling Law에 따라

```text
Model Size ↑
Data ↑
Compute ↑
→ Capability ↑
```

경향이 있지만 무한히 Model을 키울 수는 없다.

문제:

```text
Training Cost ↑

Inference Cost ↑

Latency ↑

충분한 Training Data 확보 어려움
```

등이다.

---

# 8. Small Model도 계속 필요하다

실제 Application에서는 속도와 Cost가 중요하다.

따라서

```text
항상 가장 큰 Model 사용
```

보다는 작은 Model을 사용해야 하는 환경도 존재한다.

결과적으로 개별 Model의 Capacity에는 현실적인 한계가 남는다.

---

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

# 107. Multi-Agent Architecture는 여러 축의 조합

```text
Agent
Environment
Interaction
Organization

+

Cooperation / Competition / Hybrid

+

Rule-based / Role-based

+

Centralized / Decentralized / Hybrid
```

를 Domain에 맞게 조합한다.

---

# 108. Hierarchy와 Permission

강의 Q&A에서는 상위 Agent의 권한을 하위 Agent가 모두 사용하게 두지 않는 경우가 많다고 설명한다.

---

# 109. 왜 Permission을 다르게 하는가?

모든 Agent가 동일 권한을 가지면

```text
누가 최종 Decision을 하는지 불명확

Communication Loop

Consensus 실패
```

등이 발생할 수 있다.

따라서

```text
Agent Role
↓
Permission
↓
Allowed Action
```

을 다르게 설정할 수 있다.

---

# 110. Human Organization과 유사

```text
CEO

Manager

Employee
```

가 모두 같은 권한을 가지고 있지 않은 것처럼 Multi-Agent에서도 역할에 따라 권한을 구분할 수 있다.

---

# 111. Multi-Agent System의 가장 큰 미해결 문제 — Scaling

Agent가 몇 개일 때는 관리 가능하다.

하지만

```text
10 Agents

100 Agents

10,000 Agents
```

로 증가하면 문제가 커진다.

---

# 112. Communication Complexity

Agent 수가 증가하면 Communication Channel도 크게 증가할 수 있다.

```text
Agent 수 ↑
↓
Communication ↑
↓
Token / Network / Compute Cost ↑
```

이다.

---

# 113. Optimal Collaboration Strategy

Task마다

```text
몇 명이 협력해야 하는가?

몇 명이 경쟁해야 하는가?

어떤 Agent가 필요한가?
```

가 다르다.

따라서 Task Complexity에 따라 최적 조직을 동적으로 만드는 것이 중요하다.

---

# 114. Dynamic Role Assignment

고정된 Role만 사용하는 것이 아니라

```text
Current Task
↓
필요한 Role 판단
↓
Agent Role 동적 할당
```

하는 방향이다.

---

# 115. Dynamic Organization

더 나아가

```text
Task A
→ Centralized

Task B
→ Decentralized

Task C
→ Debate

Task D
→ Collaboration
```

처럼 Organization 자체도 변경할 수 있다.

---

# 116. Orchestration이 핵심 연구 주제

결국 Multi-Agent System의 중요한 질문은

> **여러 Agent를 어떻게 효율적으로 Orchestrate할 것인가?**

이다.

단순히 Agent를 많이 추가한다고 성능이 좋아지는 것은 아니다.

---

# 117. Agent가 많으면 오히려 나빠질 수도 있다

```text
Communication Overhead

Hallucination Propagation

Endless Debate

Inconsistent Decision

Resource Waste
```

가 발생할 수 있다.

즉

```text
More Agents
≠
Always Better
```

이다.

---

# 118. Multi-Agent System의 최종 목적

Agent의 수를 늘리는 것 자체가 목적이 아니다.

목표는

```text
각 Agent의 Specialized Capability
+
Appropriate Interaction
+
Good Organization
+
Experience
↓
Collective Intelligence
```

를 만드는 것이다.

---

# 119. Multi-Agent System = 지속적으로 진화하는 조직

강의에서는 완성된 Multi-Agent System을

```text
한 번 만들어 놓고
끝나는 Software
```

로만 보지 않는다.

인간 조직처럼

```text
Experience

Success

Failure

New Knowledge
```

를 통해 계속 발전할 수 있는 **Collective Intelligence Experiment**로 설명한다.

---

# 120. Physical AI로 확장

강의 후반에서는 Multi-Agent/Agent System을 Physical AI와 연결한다.

현재는 사람이

```text
Robot에게 Task 지시
```

하는 구조가 일반적인 형태다.

---

# 121. 앞으로의 가능성 — Agent가 Human과 Robot 모두에게 Task Dispatch

강의에서는 미래 구조를 다음처럼 상상한다.

```text
Environment
↓
Central Agent
↓
Task 판단
├─ Human에게 할당
└─ Robot에게 할당
```

한다.

즉 Human이 항상 System의 Dispatcher일 필요는 없다는 관점이다.

---

# 122. Human과 Robot을 같은 Worker Pool로

개념적으로

```text
Available Workers
├─ Human A
├─ Human B
├─ Robot A
└─ Robot B
```

중 Agent가 Task 특성에 맞는 Worker에게 일을 배분할 수 있다.

---

# 123. 예: Manufacturing

어떤 Task가

```text
위험하고 반복적
```

이면 Robot에게 할당한다.

반면

```text
현재 Robot이 수행하기 어려움
+
Human의 섬세한 판단 필요
```

하면 Human에게 할당한다.

---

# 124. Manufacturing Agent의 목표

```text
Task
↓
Agent
↓
Best Worker Selection
↓
Human / Robot
↓
Overall Factory Efficiency ↑
```

형태다.

---

# 125. Dark Factory

강의에서는 장기적 이상향으로 **Dark Factory**를 언급한다.

모든 Production이 Robot/System으로 이루어지면 사람이 작업하기 위해 조명을 켤 필요가 없다는 의미에서 나온 표현이다.

---

# 126. 강의에서 제시한 단계

### 초기

```text
Human
↓
Robot에게 지시
```

### 중간

```text
Agent
├─ Human에게 Task
└─ Robot에게 Task
```

### 더 발전

```text
Agent
↓
Robotic Workforce
↓
Highly Automated Factory
```

라는 방향이다.

이 부분은 강사의 미래 전망으로 이해해야 한다.

---

# 127. Multi-Agent의 큰 그림

```text
Single LLM은 강력하다.
      ↓
하지만
Hallucination
Capacity
Scaling
Error Propagation
문제가 존재
      ↓
여러 Agent를 활용
      ↓
Multi-Agent System


그런데
여러 개만 띄운다고
좋아지는 것은 아니다.
      ↓
Agent를 조직해야 함
      ↓

Agent
Environment
Interaction
Organization


Agent마다
역할을 어떻게 줄까?
      ↓
Prompt-based Role Play

더 안정적으로?
      ↓
Fine-tuning

더 특정 가치관으로?
      ↓
Value Steering


Agent끼리
어떤 관계를 만들까?
      ↓

Cooperation
vs
Competition


Cooperation
→ 빠른 Goal Alignment
→ 하지만 Hallucination 전파 가능

Competition
→ 비판·다양성
→ 하지만 Endless Debate 가능


그래서
      ↓
Hybrid


Behavior를
어떻게 제약할까?
      ↓

Rule-based
or
Role-based


조직은?
      ↓

Centralized
vs
Decentralized
      ↓
Hybrid


System이 커지면?
      ↓
Scaling Problem
Communication Cost
Dynamic Role
Dynamic Organization
      ↓
Orchestration


최종 목표
      ↓
여러 Agent의
단순 합을 넘어서는
Collective Intelligence
```

---

# 128. Cooperation vs Competition

|구분|Cooperation|Competition|
|---|---|---|
|목표|공동 목표|상충 목표|
|관계|협력|경쟁/토론|
|장점|빠른 협업, 전문성 결합|비판, 다양성, 창의성|
|단점|Hallucination 전파, Groupthink|Endless Debate, Cost|
|필요한 것|Communication|End Condition / Moderator|

---

# 129. Rule-based vs Role-based

|구분|Rule-based|Role-based|
|---|---|---|
|핵심|행동 규칙 정의|역할 정의|
|예|법, Protocol, Constraint|의사, PM, Developer|
|장점|통제·Debugging 용이|전문성·Context 집중|
|Behavior 제한 기준|Rule|Role|
|구현|Prompt/RAG/Code/Training|Prompt/Fine-tuning 등|

---

# 130. Centralized vs Decentralized

|구분|Centralized|Decentralized|
|---|---|---|
|중심 Agent|있음|없음|
|Control|쉬움|어려움|
|Decision|명확|분산|
|Scalability|Bottleneck 가능|높음|
|Fault Tolerance|중앙 Failure 위험|상대적으로 강함|
|Debugging|쉬운 편|어려움|

---

# 131. Multi-Agent의 네 구성요소

|요소|핵심 질문|
|---|---|
|Agent|누가 행동하는가?|
|Environment|어디에서 행동하는가?|
|Interaction|서로 어떻게 소통하는가?|
|Organization|누가 어떤 위치·권한을 갖는가?|

이 네 가지가 이번 강의에서 Multi-Agent Architecture를 설계할 때 가장 중요한 Framework다.

---

# 132. 자주 헷갈리는 점 ① — Multi-Agent는 LLM을 여러 번 호출하는 것인가?

단순히 같은 LLM을 여러 번 호출하는 것만으로는 충분하지 않다.

각 Agent의

```text
Role
Interaction
Organization
Environment
```

가 정의되어야 한다.

---

# 133. 자주 헷갈리는 점 ② — Agent마다 다른 LLM이 필요한가?

아니다.

같은 LLM을 서로 다른 Prompt로 Role-playing시킬 수도 있다.

반대로 역할별 다른 Model을 사용할 수도 있다.

---

# 134. 자주 헷갈리는 점 ③ — Prompt로 Role을 주면 항상 유지되는가?

아니다.

Context가 복잡해지면 Role Consistency가 무너질 수 있다.

더 안정적인 역할이 필요하면 Fine-tuning이나 Steering을 고려할 수 있다.

---

# 135. 자주 헷갈리는 점 ④ — Multi-Agent를 사용하면 Hallucination이 사라지는가?

아니다.

특히 Cooperative System에서는 한 Agent의 Hallucination이 다른 Agent에게 전파될 수도 있다.

---

# 136. 자주 헷갈리는 점 ⑤ — Competition이 항상 Hallucination을 해결하는가?

아니다.

비판적 검증 가능성은 높아지지만 잘못된 Agent끼리 끝없이 논쟁하거나 잘못된 합의를 할 수도 있다.

---

# 137. 자주 헷갈리는 점 ⑥ — 경쟁 Agent는 합의할 때까지 계속 돌리면 되는가?

아니다.

End Condition이 없다면 Endless Debate와 Resource Waste가 발생할 수 있다.

---

# 138. 자주 헷갈리는 점 ⑦ — Cooperation과 Competition 중 하나를 선택해야 하는가?

아니다.

Hybrid System으로 상황에 따라 두 방식을 모두 사용할 수 있다.

---

# 139. 자주 헷갈리는 점 ⑧ — Environment가 좁으면 안 좋은 Agent인가?

아니다.

Domain을 제한하면 Generality는 줄지만 높은 Reliability와 Depth를 얻기 쉬울 수 있다.

---

# 140. 자주 헷갈리는 점 ⑨ — Organization은 단순히 Agent 목록인가?

아니다.

Agent 사이의

```text
Hierarchy

Authority

Decision Weight

Permission
```

을 포함한다.

---

# 141. 자주 헷갈리는 점 ⑩ — 모든 Agent의 권한은 같아야 하는가?

아니다.

Task와 Organization에 따라 권한을 다르게 설정하는 것이 일반적일 수 있다.

---

# 142. 자주 헷갈리는 점 ⑪ — Rule-based Agent는 Autonomous Agent가 아닌가?

아니다.

Autonomy 안에도 Constraint를 줄 수 있다.

현실의 인간도 법과 규칙 안에서 자율적으로 행동한다.

---

# 143. 자주 헷갈리는 점 ⑫ — Role-based와 Rule-based는 완전히 배타적인가?

아니다.

예:

```text
Doctor Role
+
Medical Safety Rules
```

처럼 함께 사용할 수 있다.

---

# 144. 자주 헷갈리는 점 ⑬ — Centralized = Cooperation인가?

아니다.

Organization Structure와 Interaction Type은 서로 다른 축이다.

Centralized Competition도 가능하다.

---

# 145. 자주 헷갈리는 점 ⑭ — Decentralized가 항상 더 강건한가?

특정 Central Failure에는 강할 수 있지만 Coordination과 Debugging 문제가 커질 수 있다.

---

# 146. 자주 헷갈리는 점 ⑮ — Agent 수를 늘리면 성능이 계속 좋아지는가?

아니다.

Agent가 늘어나면 Communication Cost와 Coordination 문제가 빠르게 커진다.

---

# 147. 자주 헷갈리는 점 ⑯ — Multi-Agent에서 가장 중요한 것은 Agent Model 성능인가?

Model Capability도 중요하지만

```text
Agent Architecture

Interaction

Organization

Harness
```

가 전체 Performance에 큰 영향을 미친다.

---

# 148. 자주 헷갈리는 점 ⑰ — Harness는 Framework 이름인가?

특정 하나의 Framework 이름이라기보다

> **Agent가 잘 동작하도록 Model 주변의 Tool·Workflow·Architecture를 구성하는 전체 System Design**

이라는 넓은 표현으로 강의에서 사용한다.

---

# 149. 자주 헷갈리는 점 ⑱ — Multi-Agent Architecture에는 표준 정답이 있는가?

강의에서는 명확하게 없다고 설명한다.

Domain과 Task에 따라 실제 Experiment가 중요하다.

---

# 150. 자주 헷갈리는 점 ⑲ — Multi-Agent System은 완성 후 고정되는가?

강의에서는 Experience와 Data를 재활용하여 지속적으로 발전할 수 있는 Self-evolving System 관점으로 설명한다.

---

# 151. 자주 헷갈리는 점 ⑳ — 인간과 Robot을 연결하는 Physical AI도 Multi-Agent 관점으로 볼 수 있는가?

강의에서는 장기적으로 Agent가 Human과 Robot 모두를 Worker로 보고 Task를 할당하는 System으로 발전할 가능성을 설명한다.

이는 강사의 미래 전망이다.

---

# 152. 앞 LangChain 과정과 연결

이전 LangChain 과정에서는

```text
LLM
↓
Post-training
↓
RAG
↓
Tool Use
↓
Single Agent
↓
Environment
Memory
Planning
Reflection
```

까지 왔다.

이번에는

```text
Single Agent
↓
Multi-Agent
↓
Agent Organization
```

으로 확장된다.

---

# 153. Single Agent vs Multi-Agent

### Single Agent

```text
One Policy / LLM
↓
모든 Reasoning
Planning
Tool Use
```

를 담당한다.

### Multi-Agent

```text
Agent A
Agent B
Agent C
...
↓
Role Specialization
↓
Interaction
↓
Organization
↓
Collective Decision
```

한다.

---

# 154. Agent 발전 흐름

```text
LLM
↓
Tool-using LLM
↓
Single Agent
↓
Agent with Memory / Planning
↓
Multi-Agent
↓
Specialized Roles
↓
Collaboration / Competition
↓
Organization
↓
Dynamic Orchestration
↓
Collective Intelligence
```

---

# 155. 이번 강의에서 반드시 기억할 핵심

### 1. AI Agent Harness는 LLM과 Tool을 포함한 Agent 주변 System을 어떻게 구성해야 Agent가 잘 동작하는지를 다루는 Architecture/Methodology 관점이다.

### 2. LLM Capability가 높아져도 Hallucination과 Capacity·Cost 등의 한계는 남는다.

### 3. Autoregressive LLM은 초기 판단 오류가 이후 Generation에 영향을 줄 수 있다.

### 4. Model Scaling만으로 모든 문제를 해결하기 어렵기 때문에 여러 Agent를 결합하는 방향이 등장한다.

### 5. Multi-Agent System은 여러 Agent가 협력·경쟁·지식 공유를 통해 Collective Intelligence를 만드는 것을 목표로 한다.

### 6. Multi-Agent는 하나의 Model을 크게 만드는 Vertical Scaling과 달리 Agent 수와 전문성을 늘리는 Horizontal Scaling 관점으로 볼 수 있다.

### 7. Multi-Agent의 목표는 개별 Agent 능력의 단순 합이 아니라 Interaction을 통한 Synergy다.

### 8. Agent들은 동일한 LLM Backbone을 사용하면서 서로 다른 Prompt로 Role을 부여할 수도 있다.

### 9. 서로 다른 Specialized Model을 각 Agent에 사용할 수도 있다.

### 10. Agent Role을 부여하는 가장 쉬운 방법은 Role Prompting이다.

### 11. Prompt만으로 Role Consistency가 부족하면 Role-specific Fine-tuning을 사용할 수도 있다.

### 12. 최근에는 특정 가치와 행동 방향을 안정적으로 갖도록 하는 Value Steering 방향도 연구되고 있다고 강의에서 소개한다.

### 13. Multi-Agent Architecture를 설계할 때 Agent, Environment, Interaction, Organization 네 요소를 생각해야 한다.

### 14. Agent는 Role·Capability·Behavior·Knowledge·Model·Tool을 가진 행위자다.

### 15. Environment는 Agent가 동작하는 Domain과 Context를 의미한다.

### 16. Environment 범위를 제한하면 Generality는 줄지만 더 높은 Domain Accuracy를 얻기 쉬울 수 있다.

### 17. Interaction은 Agent 간 Cooperation, Debate, Competition, Delegation 같은 Communication 구조를 의미한다.

### 18. Organization은 Hierarchy, Authority, Permission, Decision Weight 등을 정의한다.

### 19. Cooperative Multi-Agent는 공통 Goal을 위해 역할을 분담한다.

### 20. Cooperation은 Goal Alignment와 전문성 결합에 유리하다.

### 21. Agent 간 Communication이 많아지면서 System Complexity와 Cost가 증가할 수 있다.

### 22. Cooperative System에서는 하나의 Hallucination이 다른 Agent에게 전파될 수 있다.

### 23. Competitive Multi-Agent에서는 서로 상반된 Goal이나 Perspective를 이용해 비판적 검증을 수행한다.

### 24. Competition은 Diversity와 Creative Strategy를 유도할 수 있다.

### 25. Competition은 Endless Debate에 빠질 수 있기 때문에 End Condition이 중요하다.

### 26. Moderator/Orchestrator Agent나 Turn/Time Limit을 이용해 경쟁을 종료시킬 수 있다.

### 27. Game Environment는 Competition과 Strategy를 평가하기 좋은 Testbed가 될 수 있다.

### 28. Cooperation과 Competition을 상황에 따라 함께 사용하는 Hybrid Approach가 가능하다.

### 29. 강의에서는 이러한 Dynamic Agent Selection을 MoE의 Gating 구조와 비유한다.

### 30. Rule-based Multi-Agent System은 Agent가 따라야 할 Protocol과 Constraint를 명시한다.

### 31. Rule은 Agent Autonomy를 제거하는 것이 아니라 허용 가능한 행동 공간을 정의한다.

### 32. Rule은 Prompt, RAG, Software Logic, Training 등 여러 방법으로 구현할 수 있다.

### 33. 명확한 Rule은 Agent Failure와 Constraint Violation을 판단할 수 있어 Debugging에 도움이 된다.

### 34. Role-based System은 각 Agent의 전문 역할을 정의해 행동을 제한한다.

### 35. Role-based Structure는 의료·연구·Software Development처럼 전문가 역할이 존재하는 Domain에 적합할 수 있다.

### 36. Software Project를 PM, Designer, Developer, Evaluator, Customer Agent 등으로 나눌 수 있다.

### 37. Multi-Agent System은 Deployment Experience를 축적해 Agent와 System 자체를 지속적으로 개선할 수 있다.

### 38. Multi-Agent Organization에는 Centralized, Decentralized, Hybrid 구조가 존재한다.

### 39. Centralized System에서는 중앙 Orchestrator가 Communication과 Final Decision을 담당한다.

### 40. Centralized Structure는 통제와 Debugging이 쉬운 대신 Bottleneck과 Single Point of Failure 문제가 있다.

### 41. Decentralized System에서는 Agent끼리 직접 Interaction한다.

### 42. Decentralized Structure는 Scalability와 Flexibility에 유리하지만 Coordination과 Debugging이 어렵다.

### 43. 실제로는 두 구조를 결합한 Hybrid Organization도 사용할 수 있다.

### 44. Cooperation/Competition, Rule/Role, Centralized/Decentralized는 서로 다른 설계 축이다.

### 45. 하나의 System에서 여러 축을 동시에 조합할 수 있다.

### 46. 모든 Agent에 같은 Permission을 제공할 필요는 없다.

### 47. Role과 Hierarchy에 따라 Agent가 수행할 수 있는 Action 권한을 다르게 설정할 수 있다.

### 48. Agent 수가 많아질수록 Communication과 Coordination Cost가 커지는 Scalability 문제가 중요하다.

### 49. Task에 따라 필요한 Agent와 Role을 동적으로 선택·할당하는 Mechanism이 중요한 연구 문제다.

### 50. Multi-Agent System에서는 어떤 Task에서는 협력하고 다른 Task에서는 경쟁하게 하는 Dynamic Orchestration이 가능하다.

### 51. Agent를 많이 사용하는 것 자체가 목표가 아니며 좋은 Organization과 Interaction을 설계하는 것이 중요하다.

### 52. Multi-Agent System은 인간 조직처럼 Success/Failure Experience를 통해 지속적으로 진화하는 Collective Intelligence System으로 볼 수 있다.

### 53. 강의에서는 Physical AI의 미래 방향으로 Agent가 Task에 따라 Human 또는 Robot에게 일을 Dispatch하는 System 가능성도 소개한다.

### 54. 제조 환경에서는 위험하고 반복적인 Task를 Robot에게, 아직 Robot이 어려워하는 Task를 Human에게 할당할 수 있다.

### 55. 장기적으로는 높은 수준으로 자동화된 Dark Factory와 같은 방향도 강사의 미래 전망으로 소개된다.

---

# 156. 이번 강의 전체 논리

```text
Single Agent는 강력하다.
      ↓
하지만
Hallucination
Capacity
Cost
Error Propagation
한계가 있다.
      ↓
Model 하나만
계속 크게 만들까?
      ↓
그것만으로는 부족하다.
      ↓
여러 Agent를 사용하자.
      ↓
Multi-Agent System


그런데
Agent 여러 개를
그냥 실행하면 되는가?
      ↓
아니다.
      ↓
조직이 필요하다.


가장 먼저
Agent마다 역할을 준다.
      ↓
Role Prompting

더 강하게?
      ↓
Fine-tuning

특정 행동 방향?
      ↓
Value Steering


그리고
Architecture를 설계한다.
      ↓

Agent
Environment
Interaction
Organization


Agent는
누가 행동하는지

Environment는
어디서 행동하는지

Interaction은
어떻게 소통하는지

Organization은
누가 어떤 권한을 갖는지


Agent 관계는?
      ↓

Cooperation
or
Competition


Cooperation
      ↓
공통 목표
빠른 협업
전문성 결합

하지만
      ↓
Hallucination Propagation
Communication Cost


Competition
      ↓
비판
검증
다양성
창의성

하지만
      ↓
Endless Debate
Resource Waste


그래서
      ↓
Hybrid


Behavior를
어떻게 제한할까?
      ↓

Rule-based

또는

Role-based


조직은?
      ↓

Centralized
      ↓
통제 쉬움
하지만 Bottleneck

Decentralized
      ↓
Scalable / Flexible
하지만 Debugging 어려움

      ↓
Hybrid


Agent가 많아진다.
      ↓
Communication Cost ↑
Coordination 어려움
      ↓
Dynamic Role Assignment
Dynamic Routing
Dynamic Organization
      ↓
Orchestration


최종 목표
      ↓
개별 Agent의
단순 합을 넘어서는
Collective Intelligence
```

---

# 157. 이번 강의 핵심 한 문장

> **Multi-Agent System은 Hallucination·Capacity·Cost와 같은 Single Agent의 한계를 하나의 더 큰 LLM에만 의존하지 않고 여러 Agent를 수평적으로 확장해 보완하는 방식으로, Agent마다 Prompt·Fine-tuning·Value Steering 등을 통해 서로 다른 역할을 부여하고 Agent·Environment·Interaction·Organization이라는 네 요소를 설계한 뒤 Cooperation과 Competition, Rule-based와 Role-based Operation, Centralized와 Decentralized Organization을 Domain에 맞게 조합함으로써 개별 Agent 능력의 단순 합을 넘어서는 Collective Intelligence를 만드는 것을 목표로 한다.**

가장 짧게 기억하면 다음과 같다.

```text
Multi-Agent
= 한 명의 천재보다
  여러 전문가를 조직한다.

Agent
= 누가 일하는가?

Environment
= 어디서 일하는가?

Interaction
= 서로 어떻게 소통하는가?

Organization
= 누가 어떤 권한을 갖는가?

Cooperation
= 같은 목표를 향해 협력한다.

Competition
= 서로 비판하고 경쟁한다.

Hybrid
= 상황에 따라
  협력과 경쟁을 모두 쓴다.

Rule-based
= 무엇을 해도 되는지 정한다.

Role-based
= 각 Agent가 무엇을 맡는지 정한다.

Centralized
= 중앙 Agent가 조정한다.

Decentralized
= Agent끼리 직접 조정한다.

Multi-Agent의 본질
= Agent 수를 늘리는 것이 아니라
  좋은 조직을 만드는 것이다.
```