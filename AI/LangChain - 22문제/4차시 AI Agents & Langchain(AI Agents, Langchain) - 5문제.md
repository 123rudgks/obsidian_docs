[강의 링크](https://www.youtube.com/watch?v=PaUjwHB2QMk)
## 시험 범위에 포함되는 내용
| 시험 토픽                 | 매칭 내용                                                                   |
| --------------------- | ----------------------------------------------------------------------- |
| **AI와 AI Agent**      | Environment–Perception–Policy–Action 전체 Agent 구조                        |
| **LLM Agent의 특성**     | Environment Representation, Memory, Planning, ReAct, Reflection         |
| **AI Agent 동작**       | Perception → Observation → Planning → Action → Observation 반복           |
| **AI Agent의 Tool 활용** | Tool Selection 및 Tool Execution을 Agent Workflow에 결합                     |
| **파운데이션 모델 서비스 개발**   | LangChain/LangGraph로 LLM·Prompt·Memory·Retriever·Tool을 Application으로 연결 |
# 0. 강의 전체 개요

앞 차시에서는 Agent의 한쪽을 배웠다.

```text
Observation이 주어졌다.
↓
무엇을 할까?
↓
Reasoning / Planning
↓
어떤 Tool을 사용할까?
↓
Action
```

이번 차시는 남은 문제에서 출발한다.

> **그 Observation은 어떻게 만들어지는가?**

Agent가 실제 Environment에서 행동하려면 먼저

```text
Environment
↓
Representation
↓
Perception
↓
Understanding
↓
Observation
```

과정이 필요하다.

따라서 전체 Agent는 다음과 같이 볼 수 있다.

```text
Environment
      ↓
Perception
      ↓
Environment Representation
      ↓
Observation
      ↓
Policy / Controller
      ↓
Reasoning & Planning
      ↓
Action / Tool Use
      ↓
Environment 변화
      ↓
새 Observation
      ↓
반복
```

이번 강의의 흐름은 크게 다음과 같다.

```text
Agent
↓
Environment를 어떻게 이해할까?
↓
Text Representation
Multimodal Representation
Structured Representation

↓
Environment Understanding이 부족하다.
↓
Prompt Engineering
Memory

↓
Agent를 더 잘 만들려면?
↓
Policy Learning
+
Harness Engineering

↓
Policy Learning에는
다양한 경험이 중요
↓
Exploration
Trial and Error
Self-correction

↓
Action을 어떻게 계획할까?
↓
Local Planning
Global Planning

↓
Reasoning과 Action을
어떻게 반복할까?
↓
ReAct

↓
실패하면?
↓
Reflection / Self-refinement

↓
이 모든 Module을
어떻게 실제 System으로 묶을까?
↓
LangChain
LangGraph
```

---

# 1. Agent의 남은 절반 — Environment Understanding

앞 차시에서는 주로

```text
Observation
↓
Action
```

을 다뤘다.

하지만 실제 Agent에서는 Observation 자체가 그냥 주어지는 것이 아니다.

Agent가 실제 세계 또는 Software Environment에서 정보를 받아 이해해야 한다.

---

# 2. Environment를 어떻게 표현할 것인가?

환경에 따라 표현 방식이 완전히 달라진다.

예를 들어 바둑이라면

```text
Board
↓
Matrix
```

처럼 표현할 수 있다.

또 어떤 Environment는

```text
Graph
```

구조로 표현할 수도 있다.

---

# 3. 모든 Environment에 공통된 Representation은 없다

Environment는 너무 다양하다.

예:

```text
Chat
Voice Conversation
Robot
Game
Metaverse
Web Browser
Software Engineering
```

따라서

```text
Image?

Text?

Graph?

Vector?

Multimodal Data?
```

중 무엇으로 표현할지는 Application에 따라 달라진다.

---

# 4. Understanding은 결국 Representation 문제이기도 하다

강의에서는 Environment Understanding을

> **환경 정보를 Agent가 처리할 수 있는 Representation으로 변환하는 문제**

와 연결한다.

Deep Learning 관점에서는 결국 적절한 Vector Representation을 얻는 문제이기도 하다.

---

# 5. Environment의 예

## Chat Environment

```text
User 발화

대화 History

상대방의 감정 변화
```

등이 Environment가 될 수 있다.

---

## Voice Environment

```text
Speech

Tone

Pause

Audio Context
```

등이 Environment가 된다.

---

## Robotics

```text
Physical World

Objects

Position

Distance

Robot State
```

를 이해해야 한다.

---

## Embodied Agent

Embodied Agent는 Robotics보다 넓은 개념으로 강의에서 설명된다.

실제 Robot뿐 아니라

```text
Game Character

Metaverse Avatar
```

처럼 어떤 Environment 안에서 몸을 가지고 행동하는 Agent도 포함할 수 있다.

---

## Software Engineering Agent

```text
GitHub

Source Code

Issue

Repository

Execution Result
```

등이 Environment가 된다.

즉 Agent는 **무엇과 Interaction하는가에 따라 Environment가 완전히 달라진다.**

---

# 6. 가장 단순한 Environment Representation — Text

Environment가 원래 Text가 아니어도 Text로 표현할 수 있다.

강의에서는 대표적인 사례로 **ALFWorld**를 소개한다.

---

# 7. ALFWorld

실제 Simulation은 3D Environment다.

예:

```text
Room

Cabinet

Object

Furniture
```

등이 존재한다.

하지만 Agent에게 실제 Image를 직접 보여주는 대신 Environment 상태를 Text로 변환한다.

---

# 8. 예

Agent가

```text
Go to cabinet.
```

이라는 Action을 수행하면 Environment는

```text
You arrived in front of the cabinet.
The cabinet is closed.
```

처럼 Text Observation을 반환할 수 있다.

Cabinet을 열면

```text
The cabinet is empty.
```

같은 새로운 Observation을 준다.

---

# 9. 즉 Agent가 보는 Environment

실제:

```text
3D Simulation
```

Agent가 받는 것:

```text
Text Description
```

이다.

---

# 10. 왜 이것이 흥미로운가?

LLM은 직접 3D Simulation에서 Pre-training한 Model이 아닐 수 있다.

그럼에도 Environment를 Text로 설명하면

```text
현재 어디에 있는가?

무엇이 보이는가?

다음에 무엇을 해야 하는가?
```

를 어느 정도 추론할 수 있다.

---

# 11. Language Model 속에 이미 World Knowledge가 있다

LLM은 Text Corpus를 통해

```text
Kitchen

Refrigerator

Cabinet

Bedroom

Object relations
```

같은 현실 세계의 구조를 간접적으로 학습한다.

따라서 Text로 Environment를 설명해도 어느 정도 Physical World Reasoning이 가능할 수 있다.

---

# 12. 하지만 “어느 정도 가능하다”와 “완벽하다”는 다르다

강의에서는 이 구분을 강조한다.

GPT-3처럼 초기 Model도 특정 Prompt에서는 Instruction을 수행할 수 있었다.

하지만

```text
100번 중 100번
안정적으로 수행
```

하는 것은 아니었다.

후속 Training과 Model 발전이 이러한 능력을 더 안정적으로 만든다.

Agent Capability도 동일하다.

---

# 13. Pre-trained LLM에도 Agent적인 소양이 일부 존재할 수 있다

별도의 Agent Training을 하지 않아도 LLM이

```text
Environment 상황을 이해

다음 Action 추론

필요한 Tool 판단
```

을 일부 수행할 수 있다.

하지만 그 Capability를 더 안정적으로 만들기 위한 추가 연구와 Training이 필요하다.

---

# 14. Text Representation의 한계

단순 Environment에서는 Text가 충분할 수 있다.

하지만 복잡한 Real-world Environment에서는 Text만으로 모든 정보를 표현하기 어렵다.

---

# 15. Touchdown 같은 Navigation Task

강의에서는 실제 Street View와 Navigation Instruction을 사용하는 복잡한 Environment를 예로 든다.

예:

```text
첫 번째 신호등에서 왼쪽

두 번째 신호등은 지나감

특정 국기가 있는 건물까지 이동
```

같은 Instruction이다.

---

# 16. 이런 Task에는 여러 Intelligence가 동시에 필요

강의에서는 크게 세 가지를 강조한다.

### ① Language Understanding

```text
Navigation Instruction이
무엇을 의미하는가?
```

---

### ② Visual Understanding

현재 Scene에서

```text
Person

Car

Building

Sign

Traffic Light

Window

Flag

Color
```

등을 알아야 한다.

---

### ③ Spatial Understanding

```text
처음 어디에 있었는가?

어떤 방향으로 이동했는가?

현재 어디쯤인가?

왔던 길은 어디인가?
```

를 기억하고 추론해야 한다.

---

# 17. Environment Representation에는 History도 포함된다

현재 Image만 보고 모든 것을 알 수 있는 것은 아니다.

예를 들어

```text
"왔던 길로 돌아가."
```

라는 Instruction을 수행하려면 이전 이동 경로를 기억해야 한다.

즉 Environment State에는

```text
Current Observation
+
Past Trajectory
```

가 필요할 수 있다.

---

# 18. Smart Glasses와 연결

강의에서는 이러한

```text
Language Understanding

Continuous Visual Understanding

Spatial Understanding
```

능력을 Smart Glasses 같은 Application과 연결한다.

연속적인 Video Stream을 이해하고 사용자의 현실 Environment를 보조하는 Agent가 가능한 방향이다.

---

# 19. Physical Environment에서 Software Environment로

Environment가 반드시 현실 세계일 필요는 없다.

Web Browser도 하나의 Environment다.

---

# 20. Web Environment도 매우 복잡하다

Website 화면에는

```text
Text

Button

Image

Search Box

Menu

Advertisement

Navigation

Form
```

등이 섞여 있다.

Agent는 단순 Object Detection보다 더 복잡한 의미를 이해해야 한다.

---

# 21. Affordance Discovery

Website에서

```text
About
```

이라는 Button을 보면 사람은

```text
이 Website에 대한 정보를 보려면
여기를 누르면 되겠구나.
```

라고 추론한다.

이처럼 Interface가 어떤 행동을 가능하게 하는지를 파악하는 것을 강의에서는 **Affordance Discovery**와 연결한다.

---

# 22. UI는 사람의 상식을 활용해 설계된다

일반적인 Search Box, Button, Menu 구조는 User가 이미 알고 있는 Interface Pattern을 이용한다.

Agent 역시 이러한 Pattern을 학습해야 한다.

---

# 23. Dark Pattern

반대로

```text
회원 탈퇴

구독 취소
```

같은 기능을 일부러 찾기 어렵게 만드는 경우도 있다.

따라서 Agent가 단순하게 일반 UI Convention만 믿어서는 모든 Task를 해결할 수 없다.

---

# 24. Web Environment에는 다양한 Perception이 필요

강의에서는 Website를 이해할 때

```text
OCR
↓
Image 안 Text 읽기

Image Captioning
↓
Image 의미 이해

UI Element Understanding
↓
Button / Menu 관계 이해

Action Prediction
↓
Click하면 어떤 State가 될지 예측
```

등이 필요하다고 설명한다.

---

# 25. 단순 Computer Vision 문제보다 복잡하다

Website Screen은

```text
Dog

Cat

Car
```

같은 Object의 집합이 아니다.

각 Element가

```text
어떤 기능을 수행하는가?

Click하면 어떤 일이 일어나는가?
```

라는 Interaction Meaning을 포함한다.

---

# 26. Structured Information을 활용할 수도 있다

Web Browser Environment에는 Image만 존재하는 것이 아니다.

내부에는

```text
HTML

DOM

Accessibility Tree
```

같은 Structured Data가 존재한다.

---

# 27. WebArena 계열의 직관

강의에서는 Web Agent가

```text
Screenshot
+
HTML
+
Accessibility Information
```

을 이용할 수도 있고,

어떤 Agent는 Image 없이 HTML 중심으로 Task를 수행할 수도 있다고 설명한다.

---

# 28. Environment-specific Representation을 활용한다

Browser라는 특수 Environment에서는 굳이 모든 것을 Vision만으로 해결할 필요가 없다.

```text
Available Structured Information
```

을 적극 활용하면 현재 LLM/VLM Capability로도 더 높은 성능을 만들 수 있다.

---

# 29. 실무 Agent에서는 쉬운 Environment부터 선택한다

강의에서는 기업과 연구의 방향을 비교한다.

기업에서는 일반적으로

```text
Environment를 쉽게 표현할 수 있고
Task를 명확하게 정의할 수 있는 문제
```

부터 상용화하기 쉽다.

예:

```text
Email

Document

Structured Software Workflow
```

등이다.

---

# 30. 연구에서는 반대로 어려운 Environment를 탐색한다

연구에서는

```text
현재 기술로도
Environment Understanding이 어려운 문제
```

를 해결하려고 한다.

즉

```text
Industry
→ 현재 가능한 Environment부터

Research
→ 현재 어려운 Environment까지 확장
```

하는 경향으로 강의에서 설명한다.

---

# 31. Environment Understanding을 보완하는 방법 ① Prompt Engineering

가장 간단한 방법은 Environment 정보를 Prompt에 최대한 자세하게 적어주는 것이다.

```text
Environment Description
+
Rules
+
Available Actions
+
Current State
↓
LLM
```

이다.

---

# 32. Prompt Engineering의 한계

Environment가

```text
고정적
작음
단순
```

하다면 잘 동작할 수 있다.

하지만 Environment가 계속 변하고 범위가 매우 크면 모든 정보를 Prompt에 넣을 수 없다.

---

# 33. Environment Understanding 보완 ② Memory

다음 방법은 **Memory**다.

앞 차시 RAG와 비슷하게

```text
Model Parameter 밖에
External Knowledge를 저장
```

한다.

---

# 34. RAG와 Memory의 공통점

RAG:

```text
External Documents
↓
Relevant Knowledge Retrieval
↓
Context
```

Memory:

```text
Past Agent Experience
↓
Relevant Experience Retrieval
↓
Context
```

이다.

---

# 35. Agent가 경험을 쌓는다

예를 들어 Computer-use Agent가

```text
Click

Scroll

Keyboard Input

Back

Search
```

등을 통해 Task를 수행한다.

이 전체 Sequence를 **Trajectory**로 볼 수 있다.

---

# 36. 성공한 Experience를 저장

Task를 수행한 뒤

```text
성공했는가?

실패했는가?
```

를 판단한다.

좋은 Experience라면 Memory에 저장할 수 있다.

---

# 37. Raw Trajectory를 그대로 저장할 필요는 없다

그대로 저장하면 너무 길고 Specific하다.

따라서

```text
Raw Trajectory
↓
Abstraction / Summarization
↓
Higher-level Insight
↓
Memory
```

처럼 변환할 수 있다.

---

# 38. Experience → Insight

예를 들어 Raw Experience가

```text
Setting 클릭
→ Account 클릭
→ Privacy 클릭
→ Delete Account 발견
```

이라면 Memory에는

```text
회원 탈퇴 기능은
Account/Privacy Settings 아래에
숨겨져 있을 가능성이 높다.
```

처럼 더 일반화된 Knowledge를 저장할 수 있다.

---

# 39. 새로운 Task가 들어오면 Memory를 검색

```text
New Task
↓
Memory Search
↓
Relevant Past Experiences
↓
LLM Context
↓
Next Action
```

으로 사용한다.

---

# 40. Memory가 Environment Understanding을 보완한다

Agent가 모든 Environment를 완전히 알고 있을 필요는 없다.

일부 Experience를 가지고 나머지를 추론할 수도 있다.

강의에서는 사람이 어떤 건물의 몇 개 Room만 방문했어도 Room Number와 구조를 바탕으로 처음 가는 Room을 찾을 수 있는 것과 연결한다.

---

# 41. Human Memory와 비슷한 관점

사람도 현실 세계의 모든 State를 직접 경험하지 않는다.

```text
Partial Experience
+
Generalization
+
Reasoning
```

으로 새로운 Environment를 탐색한다.

Agent Memory 역시 이런 역할을 기대한다.

---

# 42. Memory의 장점 — Parameter Update가 필요하지 않다

Memory는 **Non-parametric** 방식으로 Knowledge를 축적할 수 있다.

즉 Base LLM을 다시 Training하지 않고도

```text
Experience Database ↑
↓
Agent Capability 보완
```

이 가능하다.

---

# 43. 개인 Experience를 조직 Knowledge로 만들 수도 있다

강의에서는 기업 관점도 설명한다.

직원이 수행했던 Workflow와 Experience를

```text
Memory
```

로 저장하면,

다른 직원의 Agent가 이를 검색해 사용할 수도 있다.

---

# 44. Collective Intelligence

```text
Employee A Experience
Employee B Experience
Employee C Experience
↓
Shared Agent Memory
↓
Organization-wide Knowledge
```

로 만들 수 있다.

즉 사람 개인에게만 존재하던 Tacit Knowledge 일부를 조직 Asset으로 전환할 가능성이 있다.

---

# 45. Automation과 Memory

반복적인 Work Experience가 충분히 축적되면 Agent가

```text
사람이 이전에 했던 Routine Task
```

를 점점 더 자동화할 수도 있다.

강의에서는 동시에 현재 Agent가 인간의 섬세한 경험 전체를 완전히 재현한다고 보기는 어렵다고 설명한다.

---

# 46. Agent와 Human의 관계에 대한 강의 관점

강의에서는 Agent를 단순히

```text
사람을 대체하는 존재
```

로만 보지 않는다.

오히려

```text
Human
+
Agent
```

가 하나의 생산 시스템을 구성한다고 설명한다.

즉 Agent Performance를 높이는 좋은 Experience와 Insight를 만들어낼 수 있는 사람의 가치도 중요해질 수 있다는 관점이다.

---

# 47. Agent 성능을 높이는 두 가지 큰 방법

강의에서는 현재 수준에서 크게 두 축으로 정리한다.

```text
1. Policy Model을 더 잘 학습한다.
   → Learning

2. Policy Model 주변의 System을 잘 설계한다.
   → Harness Engineering
```

---

# 48. Policy Learning

Agent의 Controller인 Policy Model 자체의

```text
Reasoning

Planning

Tool Selection

Decision Making
```

능력을 Training으로 개선한다.

---

# 49. Harness Engineering

Model 밖에서

```text
Memory

RAG

Tools

Database

Prompt

Workflow

Execution Environment
```

등을 잘 구성하는 것이다.

강의에서는 이러한 Model 주변 System Engineering을 **Harness Engineering**이라고 설명한다.

---

# 50. 왜 두 가지가 모두 중요한가?

매우 강력한 Model이라도

```text
잘못된 Tool

부족한 Context

Memory 없음

Poor Interface
```

이면 좋은 Agent가 되기 어렵다.

반대로 좋은 Harness가 있어도 Policy Model이 Planning을 못하면 한계가 있다.

따라서

```text
Strong Policy
+
Strong Harness
```

가 함께 필요하다.

---

# 51. Memory는 Harness Engineering의 일부

Memory를 구축하는 것은 Model Parameter를 Training하는 것이 아니다.

```text
Model 밖의 External System
```

을 설계하는 것이므로 Harness Engineering으로 분류할 수 있다.

---

# 52. Policy Learning에서 중요한 것 — Experience Diversity

강의에서는 RL/Agent Training에서 **다양한 경험**을 강조한다.

Agent가 좋은 행동만 Experience하면 실제 새로운 상황에서 대처 능력이 부족할 수 있다.

---

# 53. 좋은 경험뿐 아니라 나쁜 경험도 필요

```text
Good Action
→ High Reward

Bad Action
→ Low Reward
```

를 모두 경험해야

```text
무엇을 해야 하는가

무엇을 하지 말아야 하는가
```

를 배울 수 있다.

---

# 54. Exploration

새로운 행동을 시도해 Environment를 더 넓게 경험하는 것을 **Exploration**과 연결할 수 있다.

```text
Known Action만 반복
↓
Environment 일부만 경험

다양한 Action 시도
↓
새 State 발견
↓
Better Learning
```

이다.

---

# 55. Rollout

Agent가 현재 Policy로 실제 행동 Sequence를 수행해 보는 것을 **Rollout** 관점으로 설명할 수 있다.

```text
Policy
↓
Action
↓
Environment
↓
Next Action
↓
...
↓
Trajectory
```

를 만든다.

---

# 56. Diversity를 어떻게 높일까?

Language Model에서는

```text
Temperature
```

같은 Sampling Parameter를 조정하여 더 다양한 Output을 생성할 수 있다.

더 일반적인 Agent에서는 다양한 Action을 시도하도록 Exploration Strategy를 설계할 수 있다.

---

# 57. Curiosity

강의에서는 **Curiosity**를 Exploration을 장려하는 아이디어로 언급한다.

익숙하지 않은 State나 새로운 Experience를 탐색하는 행동에 추가적인 Incentive를 주는 식이다.

핵심은 세부 Algorithm보다

> **Agent가 Environment의 다양한 영역을 직접 경험하도록 유도한다.**

는 것이다.

---

# 58. Trial and Error

다양한 Experience에는 실패도 포함된다.

예:

```text
회원 탈퇴를 찾는다.
↓
Menu A 클릭
↓
없음
↓
Back
↓
Menu B 클릭
↓
없음
↓
Back
↓
Menu C
↓
발견
```

한다.

---

# 59. 실패도 Environment Knowledge가 된다

실패를 통해

```text
이 Website는
일반적인 위치에
회원 탈퇴 기능을 두지 않는다.
```

같은 Knowledge를 얻는다.

이 Experience를 Memory나 Training Data로 활용하면 다음 Task에서 더 좋은 행동을 할 수 있다.

---

# 60. Self-correction

```text
Action
↓
Failure
↓
현재 판단이 잘못됐음을 인식
↓
다른 Action 시도
```

를 반복하는 것이 **Self-correction**이다.

Agent의 중요한 Capability다.

---

# 61. Environment Understanding 개선 방법 정리

### Training-free / Harness

```text
Prompt Engineering

Memory

RAG

Structured Environment Information
```

### Learning-based

```text
Exploration

Rollout

Trial and Error

Self-correction Data

Reinforcement Learning
```

로 크게 나눠볼 수 있다.

---

# 62. 마지막 핵심 요소 — Reasoning & Planning

Environment를 이해하고 Tool도 사용할 수 있게 되었다.

이제 남은 질문:

> **어떤 순서로 행동해야 하는가?**

이다.

---

# 63. Reasoning과 Agent Planning의 관계

일반 Reasoning Task:

```text
Math Problem
↓
Reasoning
↓
Answer
```

Agent:

```text
Current State
↓
Reasoning
↓
Next Action
↓
New State
↓
Reasoning
↓
Next Action
```

이다.

즉 Reasoning이 **Action Planning**에 사용된다.

---

# 64. Reasoning Model이 Agent에도 중요한 이유

수학·Coding·Science Problem에서 강한 Reasoning Model이라면

```text
복잡한 State 분석

Action Consequence 추론

Subgoal 설정
```

에도 유리할 가능성이 있다.

---

# 65. Local Planning

**Local Planning**은

> **현재 State에서 바로 다음 Action을 결정하는 것**

이다.

```text
Observation_t
↓
Reasoning
↓
Action_t
```

이다.

---

# 66. Global Planning

**Global Planning**은

> **Goal까지 필요한 전체 Action Sequence를 미리 계획하는 것**

이다.

예:

```text
Goal
↓
Step 1
↓
Step 2
↓
Step 3
↓
...
↓
Goal
```

---

# 67. Global Planning의 한계

미래 Environment를 완벽하게 예측하기 어렵다.

첫 번째 Action 이후 실제 State가 예상과 다르면 원래 Plan을 그대로 사용할 수 없다.

따라서 결국 **Replanning**이 필요하다.

---

# 68. 사람의 Study Plan 비유

예를 들어

```text
8월
9월
10월
11월
12월
```

의 Study Roadmap을 미리 만들 수 있다.

하지만 실제 8월 성취도를 본 뒤 9~12월 계획을 수정해야 한다.

---

# 69. 그렇다고 Global Plan이 필요 없는 것은 아니다

Global Planning은

```text
Big Picture

Long-term Direction

Subgoal
```

을 정하는 데 의미가 있다.

Local Planning은 실제 상황 변화에 대응한다.

따라서 둘은 상호 보완적이다.

---

# 70. 실전 Agent

```text
Global Plan
↓
Local Action
↓
Observation
↓
Local Replanning
↓
필요하면 Global Plan 수정
```

처럼 사용할 수 있다.

---

# 71. ReAct

강의에서는 Agent의 대표적인 초기 Prompting 구조로 **ReAct**를 소개한다.

핵심은

**Reasoning + Acting**

을 결합하는 것이다.

---

# 72. 기존 Chain-of-Thought

```text
Question
↓
Thought
↓
Thought
↓
Thought
↓
Answer
```

처럼 내부 Reasoning을 진행한다.

---

# 73. Agent에서는 Action과 Observation이 끼어든다

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
↓
Answer
```

이다.

---

# 74. ReAct의 핵심

Agent는 생각만 하지 않는다.

```text
Reason
↓
실제 Environment에 Action
↓
새로운 Evidence 획득
↓
다시 Reason
```

한다.

즉 Reasoning과 Environment Interaction을 하나의 Loop로 묶는다.

---

# 75. Search Agent 예

```text
Thought:
Apple Remote가 무엇인지 찾아보자.

Action:
Search("Apple Remote")

Observation:
원하는 정보가 부족함.

Thought:
다른 Query가 필요하다.

Action:
Search("Front Row software")

Observation:
새로운 정보 획득

Thought:
이제 답변 가능
```

같은 구조다.

---

# 76. RAG와 ReAct 차이

단순 RAG:

```text
Query
↓
Search
↓
Answer
```

ReAct Agent:

```text
Thought
↓
Search
↓
Observation
↓
Thought
↓
다른 Search
↓
Observation
↓
...
```

처럼 검색을 반복적으로 수정할 수 있다.

---

# 77. ReAct의 의미

구조 자체는 단순해 보이지만

> **Reasoning과 Action Planning을 하나의 반복 Loop로 통합했다.**

는 점이 중요하다.

---

# 78. 기존 Reasoning 기법을 Agent에 재사용

강의에서는 기존 LLM Reasoning 연구에서 사용했던 Idea들을 Agent Planning에도 적용할 수 있다고 설명한다.

예:

```text
Plan-and-Solve

Think-and-Execute

Self-Discover

Zero-shot CoT

기타 Planning/Reasoning Prompt
```

등이다.

---

# 79. Plan-and-Solve형 Agent

큰 Task를 먼저 계획한다.

```text
Goal
↓
Plan 1
Plan 2
Plan 3
```

그다음 각 Plan을 실제로 실행한다.

즉

```text
Planning
↓
Execution
```

을 구분한다.

---

# 80. Pre-planning

행동하기 전에 미리 계획하는 것이다.

```text
Current State
↓
Plan
↓
Action
```

---

# 81. 행동 이후에도 Planning이 필요하다

실제 Execution Result가 예상과 다르면

```text
Plan
↓
Action
↓
Unexpected Result
```

가 발생한다.

따라서 실행 이후의 수정 과정도 필요하다.

---

# 82. Reflection

강의에서는 이를 **Reflection**과 연결한다.

```text
Plan
↓
Execute
↓
Result
↓
Self-evaluation
↓
Reflection
↓
New Plan
↓
Retry
```

이다.

---

# 83. 비슷한 용어

강의에서는

```text
Reflection

Self-reflection

Self-validation

Self-refine
```

등 여러 이름으로 관련 방향을 설명한다.

세부 Algorithm보다 공통 구조가 중요하다.

---

# 84. Reflection의 본질

> **자신이 방금 한 행동과 결과를 스스로 평가하고 그 Feedback을 다음 계획에 반영한다.**

이다.

---

# 85. Pre-planning vs Post-planning

### Pre-planning

```text
행동 전에
무엇을 할지 결정
```

### Reflection

```text
행동 후에
무엇이 잘못됐는지 평가
↓
계획 수정
```

으로 볼 수 있다.

---

# 86. Error Recovery

Agent System에서는 처음부터 모든 Action을 맞히는 것보다

```text
Error
↓
Detection
↓
Recovery
```

능력이 중요하다.

Reflection은 Error Recovery의 한 방법이다.

---

# 87. 반복적인 Reflection

```text
Attempt
↓
Failure
↓
Reflection
↓
Retry
↓
Failure
↓
Reflection
↓
Retry
↓
Success
```

처럼 성공할 때까지 반복하는 Agent를 만들 수 있다.

---

# 88. Agent의 강력함은 반복 가능성에도 있다

사람은

```text
시간
피로
주의력
```

등의 제한을 가진다.

Software Agent는 Compute Resource가 허용되는 범위에서 동일한 Task를 여러 번 시도하거나 병렬화할 수 있다.

강의에서는 이러한 반복·병렬화 Capability가 Agent의 큰 가능성으로 언급된다.

---

# 89. Local Agent와 Global Agent를 분리할 수도 있다

하나의 Model이 모든 Planning을 담당하지 않아도 된다.

```text
Global Planner
↓
High-level Plan

Local Planner
↓
Immediate Action
```

처럼 Module을 분리할 수 있다.

---

# 90. Algorithm과 AI를 경쟁 관계로 볼 필요가 없다

강의 후반의 중요한 Engineering 메시지다.

초기 Deep Learning은

```text
Human-designed Algorithm
vs
Neural Network
```

처럼 표현되기도 했다.

하지만 실제 Product를 만들 때는 둘을 모두 사용할 수 있다.

---

# 91. 좋은 System은 여러 기술을 조합한다

```text
Algorithm

Data Structure

Knowledge Base

Traditional Software

Deep Learning

LLM

Agent
```

를 목적에 맞게 결합한다.

---

# 92. AI Model이 모든 것을 담당해야 하는 것은 아니다

어떤 영역은

```text
Explicit Algorithm
```

이 더 안정적일 수 있고,

어떤 영역은

```text
Neural Model
```

이 더 적합할 수 있다.

최종 목표는 특정 기술을 선택하는 것이 아니라 **좋은 System을 만드는 것**이다.

---

# 93. Software Engineering 역량과 Agent

강의에서는

```text
Algorithmic Thinking

Structured Data

Data Analysis

Deep Learning

LLM Operation

Agent System Design
```

등 기존 Computer Science Skill도 여전히 의미가 있다고 강조한다.

---

# 94. 이제 LangChain

강의 마지막에서 **LangChain**을 소개한다.

강의 목적은 LangChain API나 Code 자체를 자세히 배우는 것은 아니다.

LangChain이 어떤 문제를 해결하려는 Framework인지 이해하는 것이 핵심이다.

---

# 95. LangChain

강의에서는 LangChain을

> **오늘까지 배운 LLM 기반 Application의 여러 구성요소를 쉽게 연결할 수 있도록 만든 Open-source Framework**

로 설명한다.

---

# 96. LangChain이 연결하는 것

예:

```text
LLM

Prompt

Memory

Retriever

Data

Tool

External Model
```

등이다.

---

# 97. LangChain의 목적

직접 모든 Integration Code를 처음부터 작성하기보다

```text
Component
+
Component
+
Component
```

를 하나의 Application Workflow로 쉽게 조합할 수 있게 한다.

---

# 98. 예: RAG

```text
Document Loader
↓
Chunking
↓
Embedding
↓
Vector Store
↓
Retriever
↓
Prompt
↓
LLM
```

을 연결할 수 있다.

---

# 99. 예: Agent

```text
LLM
↓
Tool Registry
↓
Memory
↓
Tool Execution
↓
Observation
↓
Next LLM Call
```

같은 구조도 연결할 수 있다.

---

# 100. LangGraph

강의에서는 LangChain이 발전하면서 **LangGraph** 같은 Framework도 함께 활용되는 방향을 언급한다.

Agent Workflow를

```text
Node
↓
Transition
↓
State
↓
Loop
```

같은 구조로 관리할 수 있는 방향으로 이해하면 된다.

강의에서는 세부 API보다 Framework가 기술 발전에 따라 계속 변화하고 있다는 점을 강조한다.

---

# 101. LangChain에서 중요한 것은 Framework 이름 암기가 아니다

Agent 분야의 기술은 매우 빠르게 바뀐다.

따라서 특정 Version의 Function Name을 모두 외우기보다

```text
Prompt

Memory

Tool

Retriever

Model

State

Workflow
```

가 어떤 역할을 하는지 이해하는 것이 더 중요하다는 것이 강의의 전체 방향과 맞닿아 있다.

---

# 102. LangChain 과정 전체 시대 흐름

강의 마지막에는 LLM 발전을 연대기적으로 정리한다.

---

# 103. 2018 — GPT-1

강의에서는 GPT-1을

```text
Transformer Decoder
+
Pre-training
```

을 Language Understanding에 본격적으로 활용한 초기 흐름으로 설명한다.

---

# 104. BERT

비슷한 시기에 Encoder 기반 BERT도 등장하면서 Pre-training을 이용한 Language Understanding이 크게 발전한다.

---

# 105. 2019 — GPT-2

```text
Model Scale ↑
Data Scale ↑
```

를 통해 Generation 능력이 크게 발전한다.

강의에서는 이를 Generative AI 발전선과 연결한다.

---

# 106. 2020 — GPT-3

Scale을 크게 키우면서

```text
Few-shot Example
↓
In-context Learning
```

능력이 강하게 나타난다.

즉 Parameter Update 없이 Context에 Example을 주는 것만으로 여러 Task를 수행할 수 있다.

---

# 107. 2022 — ChatGPT

강의에서는 핵심 변화를

```text
Instruction Following
+
RLHF
```

로 설명한다.

즉 단순 Language Model에서 실제 User가 사용할 수 있는 Assistant로 넘어간다.

---

# 108. 이후 — Multimodal

GPT-4 등의 흐름에서

```text
Text
+
Vision
```

을 함께 처리하는 Multimodal Capability가 강화된다.

---

# 109. Omni 방향

강의에서는 이후

```text
Text
Audio
Vision
```

사이의 Interaction이 더 자연스러워지는 방향을 설명한다.

핵심을 단순 Modality 추가뿐 아니라 **Interaction** 강화와도 연결한다.

---

# 110. Reasoning Model

그다음 발전선은 단순 Text Generation에서

```text
Human-level Complex Problem Solving
```

으로 확장된다.

Math, Coding, Olympiad-type Problem 등에 강한 Reasoning Model들이 등장한다.

---

# 111. Reasoning → Agent

Reasoning Capability가 강해지면 다음 단계는

```text
문제를 생각한다.
```

에서

```text
문제를 해결하기 위해
실제 Action을 한다.
```

로 이동한다.

---

# 112. Agent 발전

강의의 정리:

```text
Tool 하나 호출
↓
여러 Tool 사용
↓
Complex Task
↓
Software 사용
↓
Computer Use
↓
Physical AI
```

로 확장한다.

---

# 113. LLM 역할 자체가 변한다

초기:

```text
Language Generator
```

이후:

```text
Reasoner
```

그리고 Agent 시대:

```text
Policy / Decision Maker
```

로 발전하는 것으로 강의에서는 정리한다.

---

# 114. Model 밖의 System도 커진다

LLM이 모든 일을 수행하는 것이 아니라

```text
LLM
= Decision

Harness
= Memory + Tools + Retrieval +
  Execution + Environment
```

구조로 발전한다.

---

# 115. LangChain의 위치

따라서 LangChain 같은 Framework는

```text
LLM 자체를 만드는 Framework
```

라기보다

```text
LLM 주변의 Application / Harness를
구성하는 Framework
```

관점에서 이해하는 것이 중요하다.

---

# 116. 강의 Q&A — Loss에 Log를 사용하는 이유

강의 마지막 Q&A에서

```text
왜 Negative Log를 사용하는가?
```

라는 질문이 나온다.

강의의 설명은 다음과 같다.

Probability 기반 Likelihood는 여러 Probability의 곱으로 구성되는 경우가 많다.

```text
p₁ × p₂ × p₃ × ...
```

Log를 취하면

```text
log p₁
+
log p₂
+
log p₃
+ ...
```

처럼 Sum으로 바뀐다.

따라서 Optimization과 미분을 더 다루기 쉬운 형태로 만들 수 있다.

또 Log는 Monotonic Transformation이므로 원래 값의 대소 방향을 보존한다는 설명이다.

---

# 117. 왜 Negative인가?

Likelihood는

```text
높을수록 좋음
→ Maximize
```

이지만 일반 Loss는

```text
낮을수록 좋음
→ Minimize
```

형태로 구현하기 편하다.

따라서

```text
-log likelihood
```

형태를 사용한다.

---

# 118. LLM 발전의 돌파구는 한 축에만 있지 않다

Q&A에서 강의는 LLM 발전 방향을 크게 여러 관점으로 설명한다.

### Learning Algorithm

```text
새로운 RL
새로운 Training Method
Self-improvement
```

---

### Architecture

Transformer 외에도

```text
State Space Model

Mamba

Hybrid Architecture
```

등의 새로운 방향.

---

### Data / Experience

```text
Human Data
↓
Synthetic Data
↓
Self-generated Experience
```

로 발전한다.

---

### Hardware

```text
GPU

AI Semiconductor

Memory

Compute Efficiency
```

를 개선하여 더 큰 Scale을 저렴하게 처리한다.

즉 AI 발전은 Model Architecture만의 문제가 아니다.

---

# 119. Agent Memory에 관한 Q&A

긴 Agent Interaction에서 이전 정보를 기억하는 방법에 대한 질문에 강의에서는 크게

```text
Non-parametric Memory
```

와

```text
Parametric / Model-side Memory
```

방향을 언급한다.

---

# 120. Non-parametric Memory

```text
External Memory Store
↓
Relevant Information Retrieval
```

방식이다.

이번 강의에서 주로 설명한 Agent Memory가 여기에 해당한다.

---

# 121. Model 내부 Memory 방향

강의에서는 State Space Model과 Mamba 같은 Architecture도 긴 Sequence의 상태를 관리하는 기술 흐름과 연결하여 언급한다.

이 부분은 강의 Q&A 수준의 소개다.

---

# 122. Compute Cost 문제

Agent/LLM System은 Cost가 매우 클 수 있다.

강의에서는 모든 Task에 항상 가장 비싼 Model을 사용하는 대신

```text
Simple Task
↓
Small / Cheap Model

Difficult Task
↓
Large / Expensive Model
```

을 조합하는 방향을 제시한다.

즉 앞 Image Foundation Model에서 배운 **Model Routing**과 유사한 관점이다.

강의에서는 계산비용이 부담될 때 작은 LLM과 비싼 LLM을 상황에 따라 Mix-and-match하는 방향을 언급한다.

---

# 123. Small Model의 중요성

Q&A에서도 작은 Model의 연구 가치가 다시 언급된다.

특히

```text
On-device

No Internet

Privacy

Closed Environment
```

에서는 매우 큰 Cloud Model을 항상 사용할 수 없다.

따라서 Small Model은 계속 중요한 연구·산업 영역이다.

---

# 124. Agent 전체 구성요소

이번 차시까지 LangChain 과정에서 나온 내용을 하나의 Agent System으로 통합하면 다음과 같다.

```text
             ┌───────────────┐
             │  Environment  │
             └───────┬───────┘
                     ↓
              Perception
                     ↓
              Observation
                     ↓
┌────────────────────────────────────┐
│               Agent                │
│                                    │
│  Memory ─────────────┐             │
│                      ↓             │
│  RAG ───────────→ Context          │
│                      ↓             │
│             Policy / LLM           │
│                      ↓             │
│             Reasoning              │
│                      ↓             │
│             Planning               │
│                      ↓             │
│             Tool Selection         │
└──────────────────┬─────────────────┘
                   ↓
             Tool / Action
                   ↓
             Environment
                   ↓
             New Observation
                   ↓
                 반복
```

---

# 125. Memory와 RAG 비교

|구분|RAG|Agent Memory|
|---|---|---|
|저장 대상|External Documents|Past Experiences|
|주요 내용|Knowledge|Trajectory / Insight|
|Retrieval 기준|Query relevance|Current Task relevance|
|Model Parameter 수정|보통 X|보통 X|
|목적|지식 보완|경험 보완|

공통점은 **Non-parametric Knowledge를 Retrieval하여 Context에 넣는다**는 것이다.

---

# 126. Policy Learning vs Harness Engineering

|구분|Policy Learning|Harness Engineering|
|---|---|---|
|바꾸는 것|Model|Model 주변 System|
|예|RL, SFT|Memory, RAG, Tools|
|Parameter Update|O|보통 X|
|목표|Decision 능력 향상|Model이 일하기 좋은 환경 구축|

---

# 127. Local vs Global Planning

|구분|Local Planning|Global Planning|
|---|---|---|
|계획 범위|다음 Action|전체 Action Sequence|
|장점|환경 변화에 유연|Big Picture 제공|
|단점|장기 방향 부족 가능|미래 예측 오류|
|실전|반복 사용|Local과 조합|

---

# 128. ReAct vs Reflection

### ReAct

```text
Thought
↓
Action
↓
Observation
↓
Thought
```

즉 **실행 중 Reasoning**이다.

### Reflection

```text
Action
↓
Result
↓
Evaluation
↓
Feedback
↓
Retry
```

즉 **실행 후 자기 수정**이다.

---

# 129. Agent의 세 가지 시간축

이번 강의 내용을 시간 흐름으로 보면 이해하기 쉽다.

### 행동 전

```text
Planning
```

### 행동 중

```text
ReAct
```

### 행동 후

```text
Reflection
```

이다.

---

# 130. Agent의 Knowledge도 두 종류

### Parametric Knowledge

```text
LLM Parameter 안의
Knowledge / Skill
```

### Non-parametric Knowledge

```text
RAG Documents

Memory

Environment State

Tool Results
```

이다.

Agent는 두 가지를 함께 활용한다.

---

# 131. 자주 헷갈리는 점 ① — Agent의 Environment는 Physical World인가?

아니다.

```text
Chat

Game

Web

Software

Robot

Metaverse
```

등 Agent가 Interaction하는 모든 공간이 Environment가 될 수 있다.

---

# 132. 자주 헷갈리는 점 ② — Environment는 Image로 표현해야 하는가?

아니다.

Task에 따라

```text
Text

Image

Graph

HTML

Accessibility Tree

Multimodal Data
```

등 다양한 Representation을 사용할 수 있다.

---

# 133. 자주 헷갈리는 점 ③ — LLM이 3D Simulation을 직접 보지 못하면 Agent가 될 수 없는가?

아니다.

강의의 ALFWorld 사례처럼 Environment를 Text Observation으로 변환해 Agent에게 제공할 수도 있다.

---

# 134. 자주 헷갈리는 점 ④ — Text만으로 모든 Environment를 해결할 수 있는가?

아니다.

정밀한 Navigation이나 Visual Environment에서는 Image·Spatial Information 등이 함께 필요할 수 있다.

---

# 135. 자주 헷갈리는 점 ⑤ — Web Agent는 Screenshot만 보면 되는가?

반드시 아니다.

HTML, DOM, Accessibility Tree 등 Web-specific Structured Information을 함께 사용할 수 있다.

---

# 136. 자주 헷갈리는 점 ⑥ — Memory는 Model Fine-tuning인가?

아니다.

강의에서 주로 설명하는 Memory는 Model 밖에 Experience를 저장하고 필요할 때 Retrieval하는 Non-parametric 방식이다.

---

# 137. 자주 헷갈리는 점 ⑦ — 모든 Past Trajectory를 그대로 저장해야 하는가?

아니다.

Raw Experience를 더 일반적인 Insight로 Summarize/Abstract하여 저장할 수도 있다.

---

# 138. 자주 헷갈리는 점 ⑧ — Memory와 RAG는 완전히 다른가?

저장 대상은 다르지만 구조적으로 매우 유사하다.

```text
External Information
↓
Relevant Item Retrieval
↓
LLM Context
```

라는 공통 구조를 가진다.

---

# 139. 자주 헷갈리는 점 ⑨ — 좋은 Agent를 만들려면 LLM만 더 큰 것으로 바꾸면 되는가?

아니다.

강의에서는

```text
Policy Learning
+
Harness Engineering
```

두 축이 모두 중요하다고 설명한다.

---

# 140. 자주 헷갈리는 점 ⑩ — Harness Engineering이란?

LLM 주변의

```text
Prompt
Memory
RAG
Tools
Database
Execution System
```

등을 구성하여 Model이 실제 Task를 잘 수행하도록 만드는 System Engineering 관점이다.

---

# 141. 자주 헷갈리는 점 ⑪ — Agent Training에는 성공 Example만 필요한가?

아니다.

실패, 낮은 Reward, 시행착오도 무엇을 하지 말아야 하는지 배우는 중요한 Experience가 될 수 있다.

---

# 142. 자주 헷갈리는 점 ⑫ — Exploration은 무작정 Random Action을 하는 것인가?

핵심은 Environment의 다양한 State를 경험하는 것이다.

Randomness, Curiosity, Sampling Diversity 등 여러 Strategy를 사용할 수 있다.

---

# 143. 자주 헷갈리는 점 ⑬ — Local Planning과 Global Planning 중 하나를 선택해야 하는가?

아니다.

강의에서는 서로 보완적으로 사용할 수 있다고 설명한다.

---

# 144. 자주 헷갈리는 점 ⑭ — Global Plan을 세웠으면 그대로 따라야 하는가?

아니다.

실제 Environment 변화에 따라 Replanning해야 한다.

---

# 145. 자주 헷갈리는 점 ⑮ — ReAct = Chain-of-Thought인가?

관련 있지만 다르다.

CoT가 Reasoning Sequence라면 ReAct는

```text
Reasoning
+
Action
+
Observation
```

을 반복적으로 결합한다.

---

# 146. 자주 헷갈리는 점 ⑯ — Reflection은 처음 Planning하는 것인가?

아니다.

Action 이후 Result를 평가하고 다음 Plan을 수정하는 Post-action Feedback 과정이다.

---

# 147. 자주 헷갈리는 점 ⑰ — Agent가 한번 실패하면 Task가 끝나는가?

반드시 아니다.

Reflection/Self-correction Loop를 이용해 실패 원인을 분석하고 다시 시도할 수 있다.

---

# 148. 자주 헷갈리는 점 ⑱ — AI 시대에는 기존 Algorithm이나 Data Structure가 필요 없는가?

강의의 결론은 반대다.

실제 System에서는

```text
Algorithm
+
Structured Data
+
Deep Learning
+
LLM
+
Agent
```

를 함께 사용하는 것이 중요하다고 설명한다.

---

# 149. 자주 헷갈리는 점 ⑲ — LangChain이 LLM인가?

아니다.

LangChain은 LLM을 포함한 여러 Application Component를 연결하기 위한 Framework다.

---

# 150. 자주 헷갈리는 점 ⑳ — LangChain을 배우는 것은 API를 외우는 것인가?

이 강의에서는 아니다.

강의는 LangChain Code를 직접 배우기보다

```text
어떤 Component들이 있고

어떻게 연결되는가
```

라는 Concept 이해를 우선한다.

---

# 151. LangChain 과정 전체 연결

## 1차시 — Post-training

문제:

```text
Pre-trained LLM은
언어는 잘하지만
좋은 Assistant는 아니다.
```

해결:

```text
Instruction Tuning

RLHF

DPO

RLVR
```

결과:

```text
User Instruction을 따르고
선호되는 Behavior를 가진 LLM
```

---

## 2차시 — RAG

문제:

```text
LLM Parameter만으로는
모든 Knowledge를 알 수 없다.
```

해결:

```text
Information Retrieval
↓
External Documents
↓
LLM Context
```

결과:

```text
Augmented Language Model
```

---

## 3차시 — Tool Use & MCP

문제:

```text
Answer만 생성해서는
현실 Task를 완료할 수 없다.
```

해결:

```text
Tool Calling

Function Calling

Search

Browser

Software

API
```

LLM 역할:

```text
Generator
↓
Controller / Decision Maker
```

그리고 Tool Integration:

```text
MCP
```

---

## 4차시 — AI Agents & LangChain

마지막 문제:

```text
Agent가 Environment를
어떻게 이해하고

경험을 어떻게 기억하고

행동을 어떻게 계획하며

실패 후 어떻게 수정할까?
```

해결:

```text
Environment Representation

Perception

Memory

Exploration

Planning

ReAct

Reflection

Harness Engineering
```

그리고 이 Component를 실제 System으로 연결:

```text
LangChain / LangGraph
```

---

# 152. LangChain 과정 전체 발전선

```text
Pre-trained LLM
      ↓
Post-training
      ↓
Instruction을 잘 따르는 LLM
      ↓
RAG
      ↓
외부 Knowledge 사용
      ↓
Tool Use
      ↓
외부 Function 실행
      ↓
Agent
      ↓
환경을 관찰하고
스스로 Action 결정
      ↓
Memory
      ↓
Past Experience 활용
      ↓
Planning
      ↓
복잡한 Action Sequence 설계
      ↓
ReAct
      ↓
Reasoning ↔ Action ↔ Observation
      ↓
Reflection
      ↓
실패에서 수정
      ↓
Harness Engineering
      ↓
Model + Memory + RAG +
Tools + Environment 통합
      ↓
LangChain / LangGraph
```

---

# 153. LLM 발전 전체 시대 흐름

강의가 마지막에 정리한 흐름을 구조화하면 다음과 같다.

```text
GPT-1 / BERT
↓
Pre-training
↓
Language Understanding


GPT-2
↓
Scale-up
↓
Generative AI


GPT-3
↓
Large-scale LLM
↓
In-context Learning


ChatGPT
↓
Instruction Following
+
RLHF
↓
Practical Assistant


Multimodal Models
↓
Text + Vision + Audio


Reasoning Models
↓
Complex Problem Solving


Agents
↓
Reasoning
+
Decision Making
+
Tool Use


Computer-use Agent
↓
Software Environment Interaction


Physical AI
↓
Real-world Action
```

강의에서는 이 과정에서 LLM의 역할을

```text
Generator
↓
Reasoner
↓
Policy / Decision Maker
```

로 변화한다고 정리한다.

---

# 154. 이번 강의에서 반드시 기억할 핵심

### 1. Agent는 Action을 결정하기 전에 Environment를 적절한 Representation으로 인식해야 한다.

### 2. Environment는 Physical World뿐 아니라 Chat, Game, Web, Software 등 매우 다양하다.

### 3. Environment Representation에는 Text, Image, Graph, HTML, Accessibility Tree 등 여러 방식이 존재할 수 있다.

### 4. ALFWorld처럼 실제 3D Environment를 Text Observation으로 변환하여 LLM Agent가 처리하게 만들 수도 있다.

### 5. LLM은 Text에서 학습한 Common-sense Knowledge 덕분에 직접 3D Training을 하지 않았어도 Text로 표현된 Environment를 어느 정도 추론할 수 있다.

### 6. 하지만 복잡한 Real-world Navigation에서는 Language, Vision, Spatial Understanding이 함께 필요하다.

### 7. Web Environment는 단순 Object Recognition이 아니라 UI Element가 어떤 기능을 수행하는지까지 이해해야 한다.

### 8. Web Agent는 Screenshot 외에도 HTML이나 Accessibility Tree 같은 Environment-specific Structured Information을 활용할 수 있다.

### 9. Agent의 Environment Understanding이 부족하면 Prompt Engineering으로 Environment Rule을 설명할 수 있다.

### 10. Environment 규모가 커지고 계속 변하면 Prompt만으로는 한계가 있다.

### 11. Agent Memory는 과거 Experience를 외부에 저장하고 현재 Task와 관련된 Experience를 Retrieval하여 활용하는 방식이다.

### 12. Raw Trajectory를 그대로 저장하는 대신 더 일반화된 Insight로 Summarization하여 Memory에 저장할 수 있다.

### 13. Memory는 Model Parameter를 다시 학습하지 않고 Agent Knowledge를 보완할 수 있다.

### 14. RAG가 External Document Knowledge를 검색한다면 Agent Memory는 Past Experience를 검색한다.

### 15. 회사에서는 개인의 Workflow Experience를 Memory로 축적하여 조직 Knowledge로 전환할 가능성이 있다.

### 16. Agent 성능 향상에는 Policy Model을 직접 학습하는 방법과 Model 주변 System을 개선하는 Harness Engineering 두 축이 있다.

### 17. Memory, RAG, Tool, Database 등의 구성은 Harness Engineering에 포함할 수 있다.

### 18. Policy Learning에서는 다양한 Experience를 확보하는 것이 중요하다.

### 19. Agent는 성공 Experience뿐 아니라 실패 Experience와 낮은 Reward도 경험해야 무엇을 피해야 하는지 학습할 수 있다.

### 20. Environment의 다양한 State를 경험하기 위해 Exploration을 사용한다.

### 21. Agent가 현재 Policy로 Environment에서 여러 Action을 실행해 Trajectory를 만드는 것을 Rollout 관점으로 볼 수 있다.

### 22. Language Model에서는 Temperature 같은 방법으로 Output Diversity를 조절할 수 있다.

### 23. Curiosity 같은 Mechanism을 이용해 새로운 State를 탐색하도록 Agent를 유도할 수도 있다.

### 24. Trial-and-error를 통해 실패 원인을 학습하고 다음 행동을 수정하는 Self-correction이 가능하다.

### 25. Reasoning Capability는 Agent에서 다음 Action을 Planning하는 데 사용된다.

### 26. Local Planning은 현재 State에서 바로 다음 Action을 결정한다.

### 27. Global Planning은 Goal까지의 전체 Action Sequence를 미리 구성한다.

### 28. 미래 Environment를 완벽히 예측할 수 없으므로 Global Plan은 실제 Observation에 따라 수정되어야 한다.

### 29. Local Planning과 Global Planning은 상호 보완적으로 사용할 수 있다.

### 30. ReAct는 Reasoning과 Acting을 결합한다.

### 31. ReAct에서는 Thought → Action → Observation → Thought의 Loop를 반복한다.

### 32. Search Agent에서는 Search Result가 부족하면 다시 Reasoning하여 Query를 수정하고 Search를 반복할 수 있다.

### 33. ReAct의 핵심은 Reasoning만 수행하는 것이 아니라 Environment Interaction을 Reasoning 과정 안에 넣는 것이다.

### 34. 기존 CoT, Plan-and-Solve 등의 Reasoning Technique도 Agent Planning으로 확장할 수 있다.

### 35. Reflection은 Agent가 Action 결과를 스스로 평가하고 실패 원인을 분석하여 새로운 Plan을 만드는 과정이다.

### 36. Reflection은 행동 전 Planning보다 행동 후 Feedback/Recovery 관점에 가깝다.

### 37. Reflection Loop를 이용하면 Agent가 실패 후 다시 시도할 수 있다.

### 38. Software Agent는 반복과 병렬 실행이 가능하다는 점에서 강력한 Potential을 가진다.

### 39. Global Planner와 Local Planner를 별도 Agent/Module로 분리할 수도 있다.

### 40. 실제 AI Product에서는 Algorithm과 Neural Network를 경쟁 관계로 볼 필요가 없다.

### 41. Data Structure, Algorithm, Knowledge Base, Deep Learning, LLM, Agent를 적절하게 결합하는 것이 중요하다.

### 42. LangChain은 LLM, Prompt, Memory, Data, Retriever, Tool 등을 연결하여 LLM Application을 구축할 수 있도록 돕는 Open-source Framework로 강의에서 소개된다.

### 43. LangGraph 같은 도구는 Agent Workflow와 State를 보다 구조적으로 관리하는 방향으로 소개된다.

### 44. 이번 강의의 목표는 특정 LangChain API를 외우는 것이 아니라 Agent System의 구성요소와 연결 방식을 이해하는 것이다.

### 45. 강의의 시대적 정리에서 LLM은 Language Generator에서 Reasoner를 거쳐 Policy/Decision Maker로 발전하고 있다.

### 46. Agent 시대에는 LLM뿐 아니라 Memory, Tool, Environment, Execution을 포함한 Harness 전체가 중요하다.

### 47. LLM Training의 발전은 Learning Algorithm, Architecture, Data/Experience, Hardware 등 여러 축에서 동시에 이루어진다.

### 48. Compute Cost가 큰 경우에는 작은 Model과 큰 Model을 Task에 따라 선택하는 전략을 고려할 수 있다.

### 49. On-device, Offline, Privacy-sensitive Environment에서는 Small Model의 필요성이 계속 존재한다.

---

# 155. 이번 강의 전체 논리

```text
Agent가 Action을 한다.
      ↓
그런데
Action을 결정하려면
현재 Environment를 알아야 한다.
      ↓
Environment Representation


Environment는 다양하다.
      ↓
Text
Image
Graph
HTML
Multimodal


단순 Environment라면
      ↓
Text로 표현
      ↓
LLM이 Common Sense를 이용
      ↓
ALFWorld


하지만
Real-world Navigation은 복잡
      ↓
Language
+
Vision
+
Spatial Understanding


Software Environment도 복잡
      ↓
Screenshot
+
OCR
+
HTML
+
Accessibility Tree
      ↓
Web Agent


그런데
Environment를 전부
한 번에 알 수 없다.
      ↓
Past Experience 활용
      ↓
Memory


Trajectory
↓
성공 여부 평가
↓
Insight 추출
↓
Memory 저장
↓
새 Task에서 Retrieval


Agent를 더 잘 만들려면
두 가지 방법
      ↓

Policy Model 개선
+
Harness Engineering


Policy Learning에서는
Experience가 중요
      ↓
Exploration
Rollout
Trial and Error
Self-correction


Environment를 이해했으면
어떤 Action을 할지 정해야 함
      ↓
Planning


다음 Action만?
      ↓
Local Planning

전체 Plan?
      ↓
Global Planning

둘을 함께 사용
      ↓
Replanning


Reasoning과 Action을
하나의 Loop로
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


실패하면?
      ↓
Reflection

Execute
↓
Evaluate
↓
Reflect
↓
Replan
↓
Retry


이 모든 Module을
실제 Application으로 연결
      ↓
LangChain / LangGraph


결국
LLM 역할은
      ↓
Text Generator
↓
Reasoner
↓
Decision Maker / Policy


그리고
LLM 밖에는
      ↓
Memory
RAG
Tools
Environment
Execution
      ↓
Harness
```

---

# 156. 이번 강의 핵심 한 문장

> **AI Agent는 단순히 LLM이 Tool을 선택하는 System이 아니라 다양한 Physical·Software Environment를 Text·Vision·Structured Data 등의 Representation으로 인식하고, 부족한 Environment Knowledge를 Prompt와 Non-parametric Memory로 보완하며, Policy Learning을 통한 Exploration·Trial-and-error와 Memory·RAG·Tool을 설계하는 Harness Engineering을 함께 활용하고, Local/Global Planning과 ReAct의 Reasoning–Action–Observation Loop 및 Reflection 기반 Self-correction을 통해 목표를 반복적으로 수행하는 System이며, LangChain과 LangGraph는 이러한 LLM·Prompt·Memory·Retriever·Tool·Workflow Component를 실제 Application으로 연결하기 위한 Framework다.**

가장 짧게 기억하면 다음과 같다.

```text
Environment
= Agent가 행동하는 세계다.

Perception
= Environment를
  Agent가 이해할 정보로 바꾼다.

Memory
= 과거 Experience를
  Model 밖에 저장해 다시 쓴다.

Policy Learning
= Agent 자체의
  판단 능력을 키운다.

Harness Engineering
= Memory·RAG·Tool 등
  Agent 주변 System을 잘 만든다.

Exploration
= 다양한 행동을 해 보며
  Environment를 경험한다.

Local Planning
= 다음 Action을 정한다.

Global Planning
= 전체 방향을 잡는다.

ReAct
= 생각 → 행동 → 관찰을 반복한다.

Reflection
= 실패한 결과를 보고
  스스로 계획을 수정한다.

LangChain
= LLM·Memory·RAG·Tool 등을
  하나의 Application으로 연결한다.

Agent의 본질
= 보고 → 생각하고 → 행동하고 →
  결과를 보고 다시 수정한다.
```