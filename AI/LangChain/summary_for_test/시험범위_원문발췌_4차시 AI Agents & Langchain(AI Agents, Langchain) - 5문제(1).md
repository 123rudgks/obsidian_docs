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

