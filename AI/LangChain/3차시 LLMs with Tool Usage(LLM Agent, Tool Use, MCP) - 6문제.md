[강의 링크](https://www.youtube.com/watch?v=JKUw6hTXUro)
## 시험 범위에 포함되는 내용
| 시험 토픽                 | 매칭 내용                                                        |
| --------------------- | ------------------------------------------------------------ |
| **AI와 AI Agent**      | 일반 Model과 Environment에서 행동하는 Agent의 차이                       |
| **LLM Agent의 특성**     | Perception, Planning, Tool Use, Interaction, Decision Making |
| **AI Agent 동작**       | Observation → 판단 → Tool 선택 → 실행 → 결과 관찰 → 다음 Action          |
| **AI Agent의 Tool 활용** | Search, Calculator, DB, API, Browser, Software, Robot 등      |
| **Tool Learning**     | Tool을 언제·어떻게 사용할지 Training                                   |
| **다중 에이전트 시스템**       | A2A 및 Multi-Agent Ecosystem                                  |
## LLM Agent, Tool Use, MCP

# 0. 강의 전체 개요

앞 두 차시를 먼저 연결하면 다음과 같다.

```text
[1차시]

Pre-trained LLM
↓
Instruction Tuning
↓
Preference Learning / RLHF
↓
사람의 Instruction을
더 잘 따르는 LLM


[2차시]

LLM 자체 Knowledge만으로 부족
↓
Search Tool 연결
↓
Retrieval
↓
RAG
↓
외부 Knowledge까지 활용
```

이번에는 질문이 한 단계 더 확장된다.

> **Search만 Tool일 필요가 있는가?**

```text
Search

Calculator

Translator

Database

API

Browser

Software

Robot

사람
```

등도 Tool이 될 수 있다.

그리고 LLM이

```text
현재 환경을 관찰
↓
무엇을 해야 할지 판단
↓
Tool 선택
↓
Tool 실행
↓
실행 결과 관찰
↓
다음 행동 판단
↓
반복
```

하면 **LLM Agent**가 된다.

전체 흐름은 다음과 같다.

```text
일반 Model
= Input → Output

Agent
= Environment 안에서
  Observation ↔ Action 반복
        ↓
Policy
        ↓
Decision Making


LLM을 Policy/Controller로 사용
        ↓
LLM Agent
        ↓
Perception
Planning
Tool Use
Environment Interaction
        ↓
Function Calling
        ↓
언제 어떤 Tool을 쓸지 학습
        ↓
Prompting
or
Tool Learning
        ↓
Human Demonstration
Imitation Learning
Synthetic Tool-use Data
        ↓
많은 API를 사용하고 싶음
        ↓
API Retrieval
+
Tool Documentation
        ↓
새 Tool에도 Generalization


Tool 사용 범위 확장
        ↓
Web Agent
GUI Agent
Computer-use Agent
        ↓
긴 Action Sequence
        ↓
Final Reward만으로 부족
        ↓
Process Reward / PRM


그런데 Tool마다
연결 방식이 전부 다름
        ↓
Tool Integration 문제
        ↓
MCP
        ↓
LLM ↔ External Software
표준화된 연결


향후 External Software도
각각 Agent가 됨
        ↓
Agent ↔ Agent
        ↓
A2A
        ↓
Multi-Agent Ecosystem


하지만
연결만 하면 끝이 아님
        ↓
Permission
Security
Safety
Privacy
```

---

# 1. Agent는 LLM 때문에 새로 등장한 개념이 아니다

Agent라는 개념은 LLM 이전부터 존재했다.

강의에서는 Robot을 이용한 전통적인 Agent 구조부터 설명한다.

```text
Environment
     ↓
Sensor
     ↓
Perception / Observation
     ↓
Agent
     ↓
Action Decision
     ↓
Actuator
     ↓
Environment 변화
     ↓
다시 Observation
```

이다.

---

# 2. 일반 Model과 Agent의 차이

일반적인 Model은 흔히

```text
Input
↓
Model
↓
Output
```

으로 생각한다.

즉 하나의 Input에 하나의 Output이 대응되는 **Single-turn Function**에 가깝다.

---

# 3. Agent는 Multi-turn이다

Agent는 환경 안에서 계속 살아가며 반복적으로 Interaction한다.

```text
Observation₁
↓
Action₁
↓
Environment 변화
↓
Observation₂
↓
Action₂
↓
Environment 변화
↓
...
```

즉 핵심은 한 번 답하고 끝나는 것이 아니라

> **환경과의 지속적인 Interaction**

이다.

---

# 4. Perception

Agent는 먼저 Environment를 관찰한다.

Robot이라면

- Camera
    
- Microphone
    
- Sensor
    

등으로 Environment를 감지한다.

이를 넓게 **Perception**이라고 볼 수 있다.

---

# 5. Action

관찰한 결과를 바탕으로

```text
무엇을 할 것인가?
```

를 결정한다.

Robot에서는

- 이동
    
- 물체 잡기
    
- Motor 제어
    

등이 Action이다.

---

# 6. Actuator

결정된 Action을 실제 Environment에 수행하는 장치다.

Robot에서는

```text
Motor
Joint
Manipulator
```

등이 될 수 있다.

---

# 7. Policy

Agent에서 매우 중요한 개념이다.

> **현재 Observation에서 어떤 Action을 선택할 것인가를 결정하는 규칙**

을 **Policy**라고 한다.

개념적으로

`π(a | o)`

처럼

```text
Observation
↓
Policy
↓
Action
```

이라고 볼 수 있다.

---

# 8. Policy Model

Policy가 Neural Network 등의 Model로 구현되어 있다면 **Policy Model**이라고 부를 수 있다.

강의에서는

```text
Observation → Action
```

을 만들어내는 Model이라는 관점에서 설명한다.

---

# 9. Reinforcement Learning과 Agent

강화학습에서는 Agent가 Environment와 Interaction하면서 Reward를 받는다.

```text
Observation
↓
Action
↓
Environment
↓
Reward
+
New Observation
```

을 반복한다.

Training 목표는

> **장기적으로 높은 Reward를 얻도록 좋은 Action을 선택하는 Policy를 학습하는 것**

이다.

---

# 10. Action Selection = Decision Making

어떤 상황에서 무엇을 해야 할지 선택하는 것이므로 Agent Problem은 본질적으로 **Decision Making 문제**이기도 하다.

즉 Agent의 핵심은 단순 Text Generation이 아니다.

```text
상황 이해
↓
의사 결정
↓
행동
```

이다.

---

# 11. 과거 Agent — Physical Agent

과거 Agent 연구에서는

- Robot
    
- Drone
    
- Autonomous System
    

등 Physical Environment와 상호작용하는 Agent가 많이 연구됐다.

예:

```text
걷기
뛰기
장애물 회피
물체 조작
```

등이다.

---

# 12. LLM이 Agent의 Brain이 된다

최근에는 LLM이

- Language Understanding
    
- Reasoning
    
- Planning
    

능력을 갖게 되면서 Agent의 **Controller / Brain** 역할을 할 가능성이 커졌다.

```text
Environment
↓
Perception
↓
LLM
↓
Planning / Decision
↓
Action
```

이다.

---

# 13. LLM 발전 관점에서 본 Agent

강의에서는 다른 방향에서도 Agent 발전을 설명한다.

```text
LLM
↓
Text Generation

LLM + Search
↓
RAG

LLM + Search + Other Tools
↓
Tool-using LLM

Tool 선택과 사용을
자율적으로 반복
↓
LLM Agent
```

즉 RAG는 Agent로 가는 중간 단계로 볼 수도 있다.

---

# 14. 무엇을 Agent라고 부르지 않는가?

강의에서는 단순 RAG Pipeline 자체를 일반적으로 Agent라고 부르지 않는다고 설명한다.

```text
Query
↓
항상 Search
↓
Document
↓
항상 Generate
```

처럼 Workflow가 개발자에 의해 완전히 고정되어 있기 때문이다.

---

# 15. Reasoning Model만 강하다고 Agent도 아니다

Chain-of-Thought나 강한 Reasoning 능력을 가진 Model이라고 해서 그것만으로 Agent가 되는 것도 아니다.

Agent에는

```text
Environment Interaction
+
Action
+
Autonomous Decision
```

같은 요소가 필요하다.

---

# 16. Search와 Web Exploration의 차이

강의에서는 Search와 Web Agent도 구분한다.

### Search

```text
Query
↓
Search Engine
↓
Search Result
```

### Web Exploration

```text
Website 접속
↓
Page 이해
↓
버튼 Click
↓
Form 입력
↓
다른 Page 이동
↓
기능 사용
```

이다.

---

# 17. Web Agent

Web Agent는 Browser라는 Software Environment 안에서 실제 User처럼 행동한다.

예:

```text
Online Shopping

Flight Booking

Calendar Management

Community Posting
```

등이다.

즉 단순히 정보를 찾는 것이 아니라 **Website의 기능을 실행한다.**

---

# 18. Computer-use Agent

Web Browser 하나를 넘어

```text
PowerPoint

Spreadsheet

IDE

CAD

Desktop Application
```

등 여러 Software를 사용할 수 있다면 **Computer-use Agent** 방향으로 확장된다.

---

# 19. GUI가 공통 Interface가 된다

Software마다 API가 다르더라도 사람에게는 공통 Interface가 있다.

```text
Screen
+
Mouse
+
Keyboard
```

이다.

그래서 Computer-use Agent는

```text
Screenshot
↓
Visual Understanding
↓
Mouse / Keyboard Action
```

으로 Software를 조작할 수 있다.

---

# 20. Agent를 이해할 때 반복해서 볼 요소

강의에서는 Agent 분야가 바뀌더라도 다음 Concept을 중심으로 보면 좋다고 설명한다.

```text
Tool Use

Reasoning

Planning

Environment Representation

Perception

Interaction

Communication
```

이다.

---

# 21. LLM Agent의 구조

LLM 중심으로 Agent를 다시 표현하면 다음과 같다.

```text
Environment
↓
Perceiver
↓
Observation
↓
Controller / Policy Model
↓
Planning
↓
Tool / Action 선택
↓
Tool Execution
↓
Environment 변화
↓
다시 Perception
```

---

# 22. Controller

Controller는 Agent의 핵심 Decision Maker다.

LLM Agent에서는 LLM이 Controller 역할을 할 수 있다.

```text
Observation
↓
LLM
↓
어떤 Action을 할지 결정
```

한다.

---

# 23. LLM Agent에서 Action은 주로 Planning과 Tool Selection

Robot에서는 Action이 Motor Command였다.

LLM Agent에서는 대표적으로

```text
어떤 Tool을 사용할까?

어떤 Argument를 넣을까?

지금 Tool을 쓸까,
Text를 생성할까?
```

를 결정하는 것이 Action이 된다.

---

# 24. Tool Execution

LLM이 Tool 사용을 결정하면 실제 실행 주도권은 외부 Software로 넘어간다.

```text
LLM
↓
Tool Call
↓
External Program
↓
Result
↓
LLM
```

이다.

---

# 25. Perceiver와 Controller는 같은 Model일 수도 있다

예를 들어 Text Environment라면 LLM 자체가

```text
Observation을 읽고
+
Planning
```

을 모두 수행할 수 있다.

---

# 26. Perceiver를 별도 Model로 둘 수도 있다

예를 들어 Medical Agent를 생각하자.

Input:

```text
Medical Image
+
Patient Text
```

가 있다.

이때

```text
Medical Image Encoder
↓
Perception

LLM
↓
Planning / Decision
```

처럼 역할을 분리할 수 있다.

---

# 27. 왜 Specialized Perceiver를 사용할 수 있는가?

범용 VLM이 Medical Image를 완벽하게 표현한다고 보장할 수 없다.

따라서

```text
Specialized Medical Vision Model
```

이 더 정확한 Representation을 만들어줄 수 있다.

---

# 28. LLM에게 모든 역할을 맡기지 않는다

강의에서는 LLM을 단순 Text Generator가 아니라 **Decision Maker**로 보는 관점을 강조한다.

즉

```text
Perception
Data Processing
Calculation
Search
```

같이 다른 Module이 더 잘할 수 있는 Task는 Tool/Module에 위임하고,

LLM은

```text
Planning
Decision Making
Coordination
```

에 집중시키는 방식이다.

---

# 29. Tool이란 무엇인가?

Agent에서 Tool의 범위는 매우 넓다.

극단적으로는

```text
사람
```

도 Tool이 될 수 있다.

예를 들어 AI Scientist가 실제 Chemical Experiment를 수행할 Robot이 없다면 Human Researcher에게 실험을 요청할 수도 있다.

---

# 30. Physical Tool

Robot/Autonomous Agent에서는

- Robot Arm
    
- Sensor
    
- Vehicle
    
- Physical Machine
    

등 실제 세계와 상호작용하는 장치가 Tool이 될 수 있다.

---

# 31. Software Tool

현재 실무 Agent에서 특히 중요한 것은 Software Tool이다.

예:

```text
Function

Library

API

Search Engine

Database

Calculator

Code Interpreter

Software Application
```

등이다.

---

# 32. GUI Tool

API가 없어도

```text
Mouse
Keyboard
Screen
```

을 이용해 Software 자체를 Tool처럼 사용할 수 있다.

---

# 33. Scientific Tool

과학 분야에서는

```text
Simulation Software

Particle Accelerator Control Software

Scientific Analysis Program
```

등도 Agent가 사용할 수 있는 Tool이 될 수 있다.

---

# 34. Tool Use의 기본 예 — Weather

User:

```text
"오늘 날씨 어때?"
```

라고 묻는다.

LLM이 내부 Knowledge로 답하면 최신 Weather를 알 수 없다.

그래서

```text
User
↓
LLM
↓
"Weather Tool이 필요하다."
↓
Weather Function Call
↓
Current Weather Data
↓
LLM
↓
Natural Language Answer
```

을 만든다.

---

# 35. Function Calling

LLM이 자연어 Response 대신 특정 형식의 Tool Invocation을 생성하는 것을 **Function Calling** 관점으로 볼 수 있다.

예:

```text
get_weather(
    location="Seoul"
)
```

같은 Structured Output을 생성한다.

---

# 36. Tool Call 이후에는 LLM이 실행하는 것이 아니다

중요하다.

```text
LLM
→ Function Call 생성
```

까지만 한다.

실제 Function은

```text
Application / Runtime
```

이 실행한다.

그리고 그 Result를 다시 LLM에게 전달한다.

---

# 37. Tool Use Loop

```text
Text Generation
↓
Tool 필요 판단
↓
Function Call
↓
External Tool 실행
↓
Tool Result
↓
LLM Context에 추가
↓
다시 Text Generation
```

이다.

---

# 38. LLM이 두 가지 Mode를 다뤄야 한다

Agent LLM은

```text
Natural Language Generation Mode

Tool Calling Mode
```

를 오갈 수 있어야 한다.

강의에서는 자연어와 Code/Structured Call이 섞이는 능력을 Code Mixing과 연결하여 설명한다.

---

# 39. 가장 어려운 것은 “언제 Tool을 쓸 것인가?”

Tool 자체를 사용할 줄 아는 것만으로 충분하지 않다.

더 중요한 것은

```text
지금 Tool을 사용해야 하나?

그냥 내가 답해도 되나?

어떤 Tool이 적절한가?
```

를 판단하는 능력이다.

강의에서도 Text Generation과 Tool Execution 사이를 언제 전환할지가 중요하다고 강조한다.

---

# 40. Tool Use를 가르치는 두 방향

크게

```text
1. Prompting

2. Tool Learning / Training
```

으로 볼 수 있다.

---

# 41. Prompt-based Tool Use

Inference Prompt에

```text
사용 가능한 Tool

Tool 설명

Argument 설명

언제 사용할지

Example
```

을 넣는다.

Model 자체를 다시 Training하지 않고 Tool 사용을 유도한다.

---

# 42. Tool Learning

Model이 Tool을 더 자연스럽게 사용하도록 Training Data를 만들어 학습시킬 수도 있다.

```text
User Request
↓
Reasoning / Tool Call
↓
Tool Result
↓
Final Response
```

같은 **Trajectory**를 Training Data로 사용한다.

---

# 43. Human Demonstration

가장 직접적인 방법은 사람이 실제 Tool을 사용하는 모습을 수집하는 것이다.

예를 들어 Search Agent라면 Human이

```text
Search Query 작성
↓
Result 확인
↓
Scroll
↓
새 Query 작성
↓
다른 문서 열기
↓
답변 작성
```

하는 행동을 기록한다.

---

# 44. Human Trajectory

이렇게 사람이 실제 Task를 수행한

```text
Observation
Action
Observation
Action
...
```

Sequence를 **Trajectory**라고 볼 수 있다.

강의에서는 약 6,000개의 Human Search Workflow를 수집한 사례를 소개한다.

---

# 45. Imitation Learning

Human Trajectory를 따라 하도록 Agent를 학습시키는 것을 **Imitation Learning(모방 학습)** 관점으로 설명한다.

```text
Human Behavior
↓
Training Data
↓
Agent
↓
Human Behavior 모방
```

이다.

---

# 46. Behavior Cloning

Imitation Learning에서 Human Action을 직접 따라 하도록 학습하는 것을 **Behavior Cloning**이라고도 한다.

---

# 47. 왜 일반 Supervised Learning과 다른 용어를 사용하는가?

Classification에는 명확한 Correct Label이 존재한다.

하지만 Agent의 행동에는

```text
이 Action만이 유일한 정답
```

이라는 보장이 없다.

같은 Task를 여러 Strategy로 성공할 수 있다.

그래서

> **절대적 정답을 학습한다기보다 좋은 Human Behavior를 모방한다.**

는 의미에서 Imitation Learning이라는 표현을 사용한다.

---

# 48. SFT와 연결

Training Mechanism 자체는 Supervised Fine-Tuning일 수 있다.

```text
Human Trajectory
↓
Next Action / Tool Call Prediction
```

을 SFT로 학습한다.

즉

```text
SFT
= 학습 Mechanism

Imitation Learning
= 무엇을 학습하는가에 대한 관점
```

으로 구분할 수 있다.

---

# 49. RL도 사용할 수 있다

Agent는 Environment Interaction과 Reward 구조를 가지고 있기 때문에 Reinforcement Learning도 자연스럽게 적용할 수 있다.

즉 Tool-use Agent Training에는

```text
Imitation Learning / SFT

Reinforcement Learning
```

모두 사용할 수 있다.

---

# 50. 많은 Agent Data가 원래 Web에 존재하지 않을 수 있다

번역 Data나 Summary Data는 Internet에 많이 존재한다.

하지만

```text
Tool A 호출
↓
결과 분석
↓
Tool B 호출
↓
계산
↓
최종 답변
```

같은 Agent Trajectory는 인간이 과거에 Text Dataset으로 많이 남겨두지 않았다.

---

# 51. 그래서 Synthetic Tool-use Data가 중요하다

Agent를 학습시키기 위해 새로운 Training Data 자체를 생성해야 할 수 있다.

```text
Existing Text
↓
Tool Call을 중간에 삽입
↓
Synthetic Agent Trajectory
```

를 만든다.

---

# 52. Toolformer 계열의 직관

강의에서는 기존 Text에 Tool 사용 Step을 Synthetic하게 삽입하는 구조를 설명한다.

예를 들어 원래 Text가

```text
Pittsburgh is also known as the Steel City.
```

라면 중간에

```text
Question:
Pittsburgh의 다른 이름은?

Tool:
QA(...)

Result:
Steel City
```

같은 Tool Use를 삽입할 수 있다.

---

# 53. Calculator 예

LLM이 Text를 생성하다가

```text
[Calculator: 400 × ...]
```

같은 Function Call을 생성한다.

실제 계산기는

```text
0.29
```

같은 Result를 반환한다.

그 Result를 LLM Context에 넣으면 LLM은

```text
29%
```

처럼 최종 자연어를 생성한다.

---

# 54. Translation Tool 예

```text
Text
↓
Machine Translation Call
↓
Translation Result
↓
LLM Context
↓
계속 Generation
```

할 수 있다.

---

# 55. Search Tool 예

```text
Query
↓
Wikipedia/Search Tool
↓
Relevant Information
↓
LLM
↓
Final Answer
```

한다.

RAG에서 배운 Search 역시 Agent에게는 하나의 Function이다.

---

# 56. Tool Use를 Parameter 안에 학습

이런 Data로 Training하면 Model은

```text
언제 QA Tool을 쓸지

언제 Calculator를 쓸지

언제 Translator를 쓸지

언제 Search를 쓸지
```

같은 Pattern을 학습할 수 있다.

---

# 57. 모든 Synthetic Tool Call이 유용한 것은 아니다

LLM이 이미 쉽게 알고 있는 정보를 굳이 Tool로 찾으면 오히려 효율이 떨어질 수 있다.

예:

```text
Tool 사용 전 Prediction
vs
Tool 사용 후 Prediction
```

을 비교했을 때 Tool 사용이 실제 Loss/Performance를 개선하지 않는다면 해당 Sample은 제거할 수 있다.

---

# 58. Synthetic Data Filtering

따라서

```text
Synthetic Tool Call 생성
↓
실제로 도움이 되는가 평가
↓
유용한 Sample만 유지
↓
Training
```

한다.

이번에도 앞 Image Foundation Model 강의에서 배운

> **Synthetic Data의 Quality Filtering**

이 다시 등장한다.

---

# 59. Agent Training에서 Data Engineering이 중요하다

강의에서는 실제 기업에서도

```text
Agent Training Data

Synthetic Trajectory

Tool-use Data
```

를 잘 설계하고 생성할 수 있는 능력이 중요해지고 있다고 설명한다.

---

# 60. 하지만 세상의 Tool은 너무 많다

몇 개의 Tool만 Model 내부에 학습시키는 것은 가능하다.

하지만 실제 세상에는

```text
수천
수만
수십만
```

개의 API가 존재한다.

모든 API 사용법을 Model Parameter 안에 완전히 기억시키는 것은 어렵다.

---

# 61. Tool Learning의 목표를 바꾼다

목표를

```text
16,000개 API를 전부 암기
```

에서

```text
처음 보는 API라도
Documentation을 읽고
사용할 수 있는 능력
```

으로 바꾼다.

---

# 62. ToolLLaMA 계열의 Idea

강의에서는 16,000개 이상의 API를 활용하는 Agent 학습 사례를 설명한다.

Training 단계에서는

```text
API Documentation
↓
Instruction 생성
↓
Solution / Tool-use Path 생성
↓
Large Tool-use Dataset
↓
Fine-tuning
```

한다.

---

# 63. API Documentation에 들어갈 것

일반적으로

```text
Function Name

Function Purpose

Parameters

Parameter Meaning

Expected Output
```

등이 필요하다.

---

# 64. Inference에서는 관련 API를 먼저 검색

Tool이 16,000개라면 전부 Prompt에 넣을 수 없다.

따라서

```text
User Request
↓
API Retrieval
↓
관련 Top-K APIs
↓
API Documentation
↓
Tool-using LLM
```

을 사용한다.

---

# 65. 여기에도 RAG 구조가 등장한다

앞 차시에서는

```text
Question
↓
Document Retrieval
```

이었다.

Tool Agent에서는

```text
Task
↓
API Retrieval
```

을 수행한다.

즉

> **RAG를 Knowledge Document뿐 아니라 Tool Documentation Retrieval에도 적용할 수 있다.**

---

# 66. Tool Generalization

Training에서 본 적 없는 API라도 Documentation이 충분히 명확하다면 Model이

```text
이 Function은 무엇을 하는가?

어떤 Argument가 필요한가?
```

를 이해하고 사용할 수 있도록 하는 것이 목표다.

---

# 67. Tool Benchmark

강의에서는

- ToolBench
    
- APIBench
    
- APIBank
    
- ToolAlpaca
    

등 다양한 Tool-use Benchmark/Research 흐름을 소개한다.

각 Benchmark에서는

```text
실제 API인가?

Synthetic API인가?

한 번 호출하는가?

여러 Tool을 연결하는가?

Tool 검색이 필요한가?

몇 단계 Reasoning인가?
```

등을 평가한다.

세부 Benchmark 이름을 모두 외우는 것보다는 이러한 평가 요소를 이해하는 것이 중요하다.

---

# 68. Tool Agent에서 Web Agent로

일반 Function Call을 잘하는 것에서 더 발전하면

```text
Web Browser
```

자체를 하나의 Environment로 다루는 Web Agent가 된다.

---

# 69. Web Agent는 특수한 구조 정보를 활용할 수 있다

Browser에는

```text
HTML

DOM

Accessibility Tree
```

같은 Structured Information이 존재할 수 있다.

Agent가 이러한 정보를 이용하도록 만들면 Screen Pixel만 보는 것보다 도움이 될 수 있다.

---

# 70. Computer-use Agent는 더 일반적이다

특정 Browser API나 DOM에 의존하지 않고

```text
Screen
+
Mouse
+
Keyboard
```

같은 사람과 동일한 Interface만 이용한다.

그래서 훨씬 범용적이지만 더 어렵다.

---

# 71. 왜 Computer-use Agent가 어려운가?

Screen Image만 보고

```text
어디를 Click할지

무엇을 입력할지

현재 State가 무엇인지
```

판단해야 하기 때문이다.

앞 Image Foundation Model 과정에서 배운 VLM의 **Fine-grained Spatial Perception 문제**와 직접 연결된다.

---

# 72. Agent Task는 Action Sequence가 길다

Simple Tool Use:

```text
한 번 Tool Call
↓
Answer
```

Agent:

```text
Action₁
↓
Action₂
↓
Action₃
↓
...
↓
Actionₙ
↓
Success / Failure
```

가 될 수 있다.

---

# 73. Final Reward만으로는 학습이 어려울 수 있다

강화학습에서 마지막에

```text
Success = 1
Failure = 0
```

만 제공하면 어떤 중간 Step이 잘못됐는지 알기 어렵다.

Action Sequence가 길어질수록 이 문제가 커진다.

---

# 74. Process Reward

따라서 Final Outcome만 평가하지 않고 **중간 Reasoning/Action Process**도 평가할 수 있다.

```text
Step 1 → Reward

Step 2 → Reward

Step 3 → Reward

...
```

형태다.

---

# 75. PRM

중간 Process가 좋은지를 평가하는 Model을 **Process Reward Model(PRM)** 이라고 설명한다.

```text
Agent Trajectory
↓
각 Step 평가
↓
Process Reward
```

를 제공한다.

강의에서는 긴 Agent Trajectory를 학습시키기 위한 중요한 연구 방향으로 소개한다.

---

# 76. Agent 성능을 높이는 두 축

강의 흐름을 정리하면 Agent의 Tool Use를 잘하려면 크게 두 가지가 있다.

```text
1. Model을 잘 학습
   → Tool Learning

2. 좋은 Tool을 잘 제공
   → Tool Ecosystem / Integration
```

이다.

---

# 77. Tool Use를 Prompt로도 개선 가능

Training이 아니더라도 System Prompt 등에

```text
사용 가능한 Tool

Tool Description

Usage Rule

Examples
```

를 제공하면 Tool Calling 능력을 유도할 수 있다.

---

# 78. 그런데 Integration 문제가 생긴다

내 Agent가

```text
Slack

Gmail

Google Calendar

Database

GitHub

수많은 SaaS
```

를 사용하고 싶다고 하자.

문제는 각각 API 형식이 다르다는 것이다.

---

# 79. Tool마다 Connector를 따로 만들면

```text
Agent ↔ Slack용 Adapter

Agent ↔ Gmail용 Adapter

Agent ↔ Calendar용 Adapter

Agent ↔ GitHub용 Adapter
```

를 하나씩 개발해야 한다.

Tool이 수백·수천 개로 늘어나면 유지하기 어렵다.

---

# 80. USB 비유

강의에서는 서로 다른 Connector 규격을

```text
USB-A
USB-B
USB-C
...
```

처럼 비유한다.

Device마다 Connector가 다르면 연결할 때마다 Adapter가 필요하다.

Software Tool도 동일하다.

---

# 81. 해결 — Protocol Standardization

모든 Tool과 LLM이 공통된 규격으로 Communication할 수 있도록 **Protocol**을 정의한다.

이 흐름에서 강의가 소개하는 것이 **MCP(Model Context Protocol)** 다.

---

# 82. MCP

강의에서는 MCP를

> **LLM/Agent와 외부 Software·Tool 사이의 연결 방식을 표준화하여 다양한 Tool을 쉽게 연결하고 재사용할 수 있도록 하는 Protocol**

이라는 관점으로 설명한다.

즉 핵심은

```text
Tool 자체의 기능
```

보다

```text
Tool을 LLM에게
어떻게 노출하고 연결할 것인가?
```

에 있다.

---

# 83. MCP 이전

각 Provider/Tool마다

```text
Custom Integration
Custom Tool Calling
Custom Data Format
```

이 필요할 수 있다.

---

# 84. MCP 사용 관점

```text
LLM / Agent
      ↓
공통 Protocol
      ↓
MCP-compatible Tools
├─ Slack
├─ Calendar
├─ Gmail
├─ Database
└─ 기타 Software
```

처럼 연결한다.

---

# 85. MCP의 장점

강의에서 강조하는 방향은 다음과 같다.

```text
Standardization

Extensibility

Compatibility

Tool Reuse

Integration Cost 감소
```

이다.

즉 Tool 추가를 훨씬 쉽게 만든다.

---

# 86. MCP가 필요한 경제적 이유

Software Company 입장에서도 AI Agent가 자신의 Software를 사용할 수 있게 만드는 것이 중요해질 수 있다.

과거에는 User가 직접 Application에 접속했다면

```text
Human
↓
Application
```

향후에는

```text
Human
↓
AI Agent
↓
Application
```

이 될 수 있다.

따라서 Agent Ecosystem에 연결되지 않은 Software는 사용 기회를 잃을 수도 있다는 것이 강의의 산업적 관점이다.

---

# 87. 플랫폼 비유

강의에서는 Food Delivery Platform과 Restaurant의 관계를 비유한다.

과거:

```text
Customer
↓
Restaurant에 직접 주문
```

현재:

```text
Customer
↓
Delivery Platform
↓
Restaurant
```

처럼 중간 Platform이 User Interface를 장악할 수 있다.

Agent 역시 Software 사용의 새로운 Platform 역할을 할 가능성이 있다.

---

# 88. MCP에서의 기본 관점

강의에서는 MCP를

```text
중앙의 Intelligent LLM/Agent
+
외부의 Software Tools
```

라는 구조로 설명한다.

즉

```text
LLM
= Intelligence

External App
= Tool / Software
```

관점이다.

MCP는 이 둘을 연결한다.

---

# 89. 그런데 앞으로 Tool 자체도 Agent가 되면?

현재는

```text
Central LLM Agent
↓
Non-intelligent Tool
```

이라고 볼 수 있다.

하지만 향후에는

```text
Slack Agent

Calendar Agent

Email Agent

Shopping Agent
```

처럼 각 Software 자체에도 Intelligence가 들어갈 수 있다.

---

# 90. 중앙 Agent가 사라질 수도 있다

모든 Component가 Agent라면

```text
Central AI
↓
Passive Tools
```

가 아니라

```text
Agent A
↔
Agent B
↔
Agent C
↔
Agent D
```

의 Network가 될 수 있다.

---

# 91. A2A

강의 후반에서는 이런 방향을 **A2A(Agent-to-Agent)** Protocol과 연결한다.

핵심 Idea는

> **중앙 LLM이 Tool을 호출하는 구조를 넘어, 독립적인 Agent들이 서로 Communication하고 협업할 수 있도록 한다.**

는 것이다.

---

# 92. MCP와 A2A를 강의 관점에서 구분

### MCP

```text
Intelligent Agent
↔
External Tool / Software
```

연결을 표준화한다.

### A2A

```text
Agent
↔
Agent
```

Communication을 다룬다.

강의에서는 향후 Software 자체가 Agent화되면 Agent 간 Protocol이 중요해질 수 있다고 설명한다.

---

# 93. Multi-Agent Ecosystem

모든 Software가 Agent가 된다면

```text
Personal Agent

Company Agent

Calendar Agent

Shopping Agent

Research Agent
```

가 서로 Interaction하는 형태의 거대한 Multi-Agent System이 가능하다.

---

# 94. 그런데 연결이 쉬워질수록 다른 문제가 커진다

Agent 간 연결 자체는 기술적으로 비교적 쉽게 만들 수도 있다.

진짜 어려운 문제는 다음이다.

```text
누가 어떤 Data를 볼 수 있는가?

어떤 Action까지 실행할 수 있는가?

누구를 신뢰할 것인가?

잘못된 Agent가 접근하면?

Private Data를 어디까지 공개할 것인가?
```

---

# 95. Permission

모든 Tool을 Agent에게 무제한 제공하면 안 된다.

예:

```text
Read Email

Send Email

Delete Email

Transfer Money

Modify Calendar
```

는 위험도가 모두 다르다.

따라서 **권한 관리**가 필요하다.

---

# 96. Security

Agent가 External Tool과 연결될수록 Attack Surface도 증가한다.

따라서

```text
Authentication

Authorization

Credential Management

Malicious Tool 방지
```

등이 중요해진다.

---

# 97. Privacy

Personal Agent가 나에 대한 모든 정보를 알고 있다고 하자.

다른 Agent가

```text
그 User의 모든 정보를 알려 줘.
```

라고 요청했을 때 그대로 제공하면 큰 문제가 된다.

따라서 Agent-to-Agent Communication에서는 Privacy Policy와 Access Control이 필수다.

---

# 98. Safety

Agent는 단순히 잘못된 Text를 생성하는 것보다 더 위험할 수 있다.

왜냐하면 Tool을 통해

```text
실제 Email 전송

File 삭제

구매

계좌 작업

Software 변경
```

같은 현실 Action을 수행할 수 있기 때문이다.

따라서 Agent에서는 Output Safety뿐 아니라 **Action Safety**가 중요하다.

---

# 99. MCP/A2A에서 더 어려운 문제

강의에서는 Protocol 연결 방법 자체보다 앞으로 더 중요한 문제를

```text
Permission

Security

Safety

Privacy
```

라고 강조한다.

즉

> **연결하는 것보다 안전하게 연결하는 것이 더 어려울 수 있다.**

---

# 100. Agent 전체 구조를 다시 보면

```text
Environment
      ↓
Perception
      ↓
Observation
      ↓
LLM Controller
      ↓
Reasoning / Planning
      ↓
Action Selection
      ↓
Tool Call
      ↓
Tool Execution
      ↓
Environment 변화
      ↓
New Observation
      ↓
반복
```

이다.

---

# 101. RAG와 Agent를 연결

RAG에서는

```text
Search
```

가 하나의 Tool이었다.

기본 RAG:

```text
항상 Search
↓
항상 Top-K
↓
항상 Generation
```

Agent:

```text
Search가 필요한가?
↓
어떤 Query를 쓸까?
↓
결과가 충분한가?
↓
다시 Search할까?
↓
다른 Tool을 쓸까?
```

를 Model이 판단한다.

---

# 102. Tool Calling과 Agent도 동일하지 않다

Tool Calling이 가능하다고 해서 반드시 Agent는 아니다.

예를 들어

```text
User가 날씨 질문
↓
항상 Weather Function 호출
```

하도록 고정되어 있다면 단순 Tool-enabled Pipeline일 수 있다.

Agent다운 성격은

```text
동적인 State
+
Planning
+
Autonomous Tool Selection
+
Repeated Interaction
```

이 커질수록 강해진다.

---

# 103. Agent의 핵심은 Tool 자체가 아니다

Calculator가 있다고 Agent가 되는 것은 아니다.

핵심은

> **현재 상태와 목표를 바탕으로 언제 어떤 Tool을 어떻게 사용할지 결정하는 Policy**

다.

---

# 104. LLM 역할의 변화

초기 LLM:

```text
Text Generator
```

RAG:

```text
External Knowledge를 받아
답하는 Generator
```

Agent:

```text
Decision Maker / Controller
```

로 역할이 확장된다.

---

# 105. “LLM을 CEO처럼 쓴다”는 강의의 비유

강의에서는 LLM이 직접 모든 실무를 처리하기보다

```text
전문 Module

Specialized Model

Software

Tool
```

에 일을 위임하고 자신은 의사결정을 하는 방향을 Manager/CEO에 비유한다.

핵심은

```text
모든 작업을 LLM이 직접 한다.
```

가 아니라

```text
LLM이 무엇을 누구에게 맡길지 결정한다.
```

는 것이다.

---

# 106. Specialized Model도 Tool이 될 수 있다

예:

```text
General LLM
↓
Medical Image Model 호출
↓
Medical Image Result
↓
LLM Decision
```

즉 Tool은 Calculator나 API만 의미하지 않는다.

다른 AI Model도 Tool이 될 수 있다.

---

# 107. LLM Agent Training 전체 구조

```text
Powerful Base LLM
      ↓

Human Demonstration
or
Synthetic Trajectory
      ↓

Observation
Tool Call
Tool Result
Next Action
Final Answer
      ↓

SFT / Imitation Learning
or
Reinforcement Learning
      ↓

Tool-using Agent
```

---

# 108. Tool-use Synthetic Data 전체 구조

```text
Existing Task / Document
      ↓
Possible Tool Calls 생성
      ↓
Tool 실행
      ↓
Result 삽입
      ↓
Tool 사용이 실제 도움이 되는가?
      ↓
Filtering
      ↓
High-quality Tool-use Dataset
      ↓
Training
```

---

# 109. 많은 Tool을 처리하는 구조

```text
User Task
↓
Tool / API Search
↓
Top-K API Documentation
↓
LLM
↓
API 이해
↓
Function Call
↓
Tool Result
↓
Next Decision
```

이다.

---

# 110. RAG가 Agent 내부에서도 반복된다

앞 강의:

```text
Relevant Knowledge 검색
```

이번 강의:

```text
Relevant Tool 검색
```

이다.

즉 Retrieval은 Agent의 핵심 Building Block이 된다.

---

# 111. Agentic Tool Use

복잡한 Task에서는 한 번의 Tool Call로 끝나지 않는다.

예:

```text
Search
↓
Result 확인
↓
Calculator
↓
다른 Search
↓
Database
↓
Final Answer
```

처럼 여러 Tool을 순차적으로 사용할 수 있다.

---

# 112. Multi-tool Reasoning

따라서 Agent는

```text
Tool 선택
+
Tool 순서
+
Argument 결정
+
Result 해석
+
다음 Tool 결정
```

을 모두 수행해야 한다.

이것이 일반 Function Calling보다 어려운 이유다.

---

# 113. Process Reward가 중요한 이유

Action이 20단계인데 마지막에 실패했다고 하자.

Final Reward만 보면

```text
전체 Trajectory = Failure
```

라는 정보밖에 없다.

어느 Step까지는 잘했고 어디서 틀렸는지 알기 어렵다.

---

# 114. PRM의 역할

```text
Step 1: Good

Step 2: Good

Step 3: Bad

Step 4: Bad
```

처럼 중간 Process를 평가하면 Training Signal을 더 세밀하게 줄 수 있다.

---

# 115. Tool Learning과 MCP의 차이

자주 헷갈릴 수 있다.

### Tool Learning

```text
Model이 Tool을
언제 어떻게 사용할지 학습
```

### MCP

```text
Tool을 Model/Agent에
어떤 공통 방식으로 연결할지 표준화
```

이다.

즉

```text
Skill
vs
Interface
```

차이다.

---

# 116. Function Calling과 MCP의 차이

Function Calling:

```text
Model이 Structured Tool Call을 생성하는 능력
```

MCP:

```text
External Tool/Resource를
Agent와 연결하기 위한 Protocol
```

이다.

서로 관련 있지만 같은 개념은 아니다.

---

# 117. RAG와 MCP의 관계

RAG Retriever도 Tool로 노출될 수 있다.

따라서

```text
Retriever
Database
Search Service
```

등을 MCP 같은 Interface로 Agent에 연결하는 구조를 생각할 수 있다.

하지만 RAG 자체가 MCP는 아니다.

---

# 118. MCP와 API의 관계

각 Software는 기존에 자체 API를 가질 수 있다.

MCP의 강의상 핵심은

```text
각각 다른 API를
AI Agent가 사용할 때의
공통 연결 규칙
```

을 제공하는 것이다.

---

# 119. A2A와 MCP의 관계

강의의 발전 흐름:

```text
현재

Agent
↓
Tools
↓
MCP


향후

Agent ↔ Agent ↔ Agent
↓
A2A
```

이다.

MCP는 Tool Integration 중심,

A2A는 Agent Communication 중심으로 구분한다.

---

# 120. 자주 헷갈리는 점 ① — LLM Agent = LLM인가?

아니다.

LLM은 Agent의 Controller/Policy Model 역할을 할 수 있다.

Agent 전체에는

```text
Perception
LLM
Tools
Memory
Environment
Execution
```

등이 포함될 수 있다.

---

# 121. 자주 헷갈리는 점 ② — Agent는 무조건 Robot인가?

아니다.

Agent는 Environment와 Interaction하면서 Action을 수행하는 넓은 개념이다.

Environment가

```text
Physical World

Web

Desktop

Software

Database
```

일 수 있다.

---

# 122. 자주 헷갈리는 점 ③ — RAG는 Agent인가?

기본적인 고정 Search→Generate Pipeline은 강의에서는 Agent라고 보지 않는다.

Search 여부와 방법을 자율적으로 판단하면 Agentic RAG로 발전할 수 있다.

---

# 123. 자주 헷갈리는 점 ④ — Reasoning을 잘하면 Agent인가?

아니다.

Reasoning은 Agent의 중요한 Component이지만 환경 Interaction과 Action Decision이 필요하다.

---

# 124. 자주 헷갈리는 점 ⑤ — Tool Use = Agent인가?

반드시 아니다.

고정된 Function Call만 수행할 수도 있다.

Agent에서는 Tool 사용이 더 동적이고 반복적인 Decision Process의 일부가 된다.

---

# 125. 자주 헷갈리는 점 ⑥ — Function Call 자체를 LLM이 실행하는가?

아니다.

LLM은 어떤 Function을 어떤 Argument로 호출할지 출력하고, 실제 실행은 외부 Runtime/Application이 한다.

---

# 126. 자주 헷갈리는 점 ⑦ — Tool Result도 LLM이 생성한 것인가?

아니다.

Tool Execution Result는 실제 Tool이 생성한다.

그 결과를 다시 LLM Context에 넣는다.

---

# 127. 자주 헷갈리는 점 ⑧ — Tool Learning은 Prompt Engineering인가?

아니다.

Prompt Engineering은 Training 없이 Tool 설명을 Context에 제공한다.

Tool Learning은 Tool-use Data로 Model 자체를 Training한다.

---

# 128. 자주 헷갈리는 점 ⑨ — Imitation Learning과 SFT는 완전히 다른 Algorithm인가?

강의에서는 Human Behavior Trajectory를 SFT 방식으로 학습할 수도 있다고 설명한다.

SFT는 Training Mechanism이고 Imitation Learning은 Human Behavior를 모방한다는 목적 관점이다.

---

# 129. 자주 헷갈리는 점 ⑩ — Agent 행동에는 항상 하나의 정답이 있는가?

아니다.

하나의 Task를 성공하는 여러 Action Sequence가 존재할 수 있다.

그래서 Human Demonstration을 “정답”보다 “좋은 행동 예시”로 보는 것이 자연스럽다.

---

# 130. 자주 헷갈리는 점 ⑪ — Tool을 많이 알면 좋은 Agent인가?

반드시 아니다.

Tool을 알고 있는 것보다

```text
필요한 Tool을
적절한 순간에
정확한 Argument로
사용
```

하는 것이 중요하다.

---

# 131. 자주 헷갈리는 점 ⑫ — 모든 API를 Model Weight에 외워야 하는가?

아니다.

많은 Tool을 사용할 경우 관련 API를 검색하고 Documentation을 Context로 제공한 뒤 처음 보는 API를 사용하도록 Generalization할 수 있다.

---

# 132. 자주 헷갈리는 점 ⑬ — API Retrieval도 RAG인가?

구조적으로 매우 유사하다.

Knowledge Document 대신 Tool Documentation을 Retrieval한다는 차이가 있다.

---

# 133. 자주 헷갈리는 점 ⑭ — Web Agent와 Computer-use Agent가 같은가?

Web Agent는 Browser라는 특정 Environment에 특화될 수 있다.

Computer-use Agent는 여러 Software를 일반적인 GUI Interface로 조작하는 더 범용적인 방향이다.

---

# 134. 자주 헷갈리는 점 ⑮ — Computer-use Agent는 API를 사용해야 하는가?

반드시 아니다.

강의에서는 Screen Vision + Mouse + Keyboard처럼 사람이 사용하는 일반 Interface만 이용하는 방향을 설명한다.

---

# 135. 자주 헷갈리는 점 ⑯ — Final Reward만 있으면 긴 Agent도 쉽게 학습되는가?

아니다.

Trajectory가 길어질수록 어떤 중간 Step이 좋은지 알기 어려워져 Process Reward가 중요해질 수 있다.

---

# 136. 자주 헷갈리는 점 ⑰ — PRM이 Agent 자체인가?

아니다.

PRM은 Agent의 중간 Reasoning/Action Process를 평가하여 Reward를 제공하는 Model이다.

---

# 137. 자주 헷갈리는 점 ⑱ — MCP는 Tool인가?

아니다.

Tool을 Agent와 연결하기 위한 Protocol/Standard다.

---

# 138. 자주 헷갈리는 점 ⑲ — MCP를 쓰면 Agent가 Tool 사용법을 자동으로 아는가?

Protocol을 연결한다고 Tool Planning 능력 자체가 자동으로 생기는 것은 아니다.

Model에는 Tool Description을 이해하고 Tool을 적절히 선택하는 Capability가 필요하다.

---

# 139. 자주 헷갈리는 점 ⑳ — MCP와 A2A는 같은가?

강의에서는 다르게 설명한다.

```text
MCP
→ Agent ↔ Tool

A2A
→ Agent ↔ Agent
```

로 기억하면 된다.

---

# 140. 앞 차시들과 연결

## 1차시 — Post-training

```text
LLM 자체 Behavior를
사람의 Instruction에 맞춘다.
```

---

## 2차시 — RAG

```text
LLM에게 Search라는 Tool을 붙인다.
```

---

## 3차시 — Agent

이제

```text
Search뿐 아니라
여러 Tool을 붙인다.
```

그리고

```text
어떤 Tool을 언제 사용할지
LLM이 결정한다.
```

---

# 141. LangChain 과정의 현재 발전 흐름

```text
Pre-trained LLM
      ↓
Post-training
      ↓
Instruction을 잘 따름
      ↓
RAG
      ↓
외부 Knowledge 사용
      ↓
Tool Use
      ↓
Calculator / API / Browser /
Software 사용
      ↓
Agent
      ↓
환경을 관찰하고
동적으로 Decision
      ↓
MCP
      ↓
많은 Tool을
표준 방식으로 연결
      ↓
A2A
      ↓
여러 Agent가
서로 협력하는 Ecosystem
```

---

# 142. 이번 강의에서 반드시 기억할 핵심

### 1. Agent 개념은 LLM 이전부터 존재했다.

### 2. 일반적인 Model은 Input→Output Function으로 볼 수 있지만 Agent는 Environment와 지속적으로 Interaction한다.

### 3. Agent는 Observation을 받고 Action을 선택한 뒤 Environment 변화와 새로운 Observation을 반복한다.

### 4. Observation에서 Action을 선택하는 규칙을 Policy라고 한다.

### 5. Policy가 Model로 구현되면 Policy Model이라고 할 수 있다.

### 6. Reinforcement Learning은 Reward를 최대화하도록 Policy를 학습시키는 방법이다.

### 7. Agent의 Action Selection은 Decision Making 문제다.

### 8. 과거 Agent는 Robot, Drone 등 Physical Agent 중심으로 많이 연구됐다.

### 9. LLM의 Reasoning/Planning 능력이 발전하면서 LLM이 Agent의 Controller/Brain 역할을 할 수 있게 됐다.

### 10. 기본 RAG 같은 고정 Pipeline은 강의에서는 Agent로 보지 않는다.

### 11. Reasoning Model이라고 해서 그 자체로 Agent인 것도 아니다.

### 12. Agent의 중요한 특징에는 Tool Use, Planning, Environment Understanding, Interaction 등이 있다.

### 13. LLM Agent에서는 LLM이 Observation을 바탕으로 어떤 Action/Tool을 사용할지 결정하는 Controller가 될 수 있다.

### 14. Perception Module과 Controller는 같은 Model일 수도 있고 다른 Model일 수도 있다.

### 15. 특정 Domain에서는 범용 VLM보다 Specialized Perception Model을 사용하는 것이 유리할 수 있다.

### 16. 강의에서는 LLM을 직접 모든 일을 수행하는 실무자보다 여러 Module에 일을 배분하는 Decision Maker처럼 활용하는 방향을 설명한다.

### 17. Agent에서 Tool은 Function, API, Search, Database, Software뿐 아니라 넓게는 Physical Device나 사람까지 포함할 수 있다.

### 18. 현재 기업 Agent에서 중요한 Tool은 Software Function/API다.

### 19. Web Agent는 Browser 기능을 사용해 실제 Website와 Interaction한다.

### 20. Computer-use Agent는 Screen, Mouse, Keyboard 같은 일반 GUI Interface로 여러 Software를 조작한다.

### 21. Function Calling에서는 LLM이 어떤 Function과 Argument를 사용할지 Structured Output으로 생성한다.

### 22. 실제 Function 실행은 LLM이 아니라 외부 Application/Runtime이 담당한다.

### 23. Tool Result는 다시 LLM Context에 전달되어 다음 Generation/Decision에 사용된다.

### 24. Tool-using Model에서는 Natural Language Generation Mode와 Tool Calling Mode를 적절히 전환해야 한다.

### 25. Tool Use에서 중요한 것은 Tool을 호출할 수 있는가뿐 아니라 언제 호출해야 하는가를 판단하는 것이다.

### 26. Tool Use는 Prompting으로 유도할 수도 있고 Training으로 학습시킬 수도 있다.

### 27. Prompt-based Tool Use에서는 Tool Description과 사용법을 Context에 제공한다.

### 28. Tool Learning에서는 실제 Tool-use Trajectory를 Training Data로 사용할 수 있다.

### 29. Human Demonstration에서 사람이 Search/Scroll/Query Modification 등을 수행한 행동 Sequence를 수집할 수 있다.

### 30. 사람의 Behavior를 따라 배우는 관점을 Imitation Learning이라고 한다.

### 31. Behavior Cloning은 Human Action을 모방하는 대표적인 Imitation Learning 방식이다.

### 32. Agent 행동에는 하나의 절대적 정답이 없는 경우가 많기 때문에 “정답 학습”보다 “좋은 행동 모방” 관점이 자연스럽다.

### 33. Imitation Learning Data를 SFT Mechanism으로 학습할 수도 있다.

### 34. Agent Training에는 Reinforcement Learning 역시 사용할 수 있다.

### 35. Agent Tool-use Trajectory는 기존 Web Data에 충분하지 않을 수 있으므로 Synthetic Data가 중요하다.

### 36. Existing Text 중간에 QA, Calculator, Translation, Search 등의 Tool Call을 Synthetic하게 삽입할 수 있다.

### 37. Tool Execution Result는 다시 Text Context에 삽입되고 LLM이 이후 Generation을 이어간다.

### 38. Tool Call이 실제 Model Performance에 도움이 되는지를 평가하여 Synthetic Sample을 Filtering할 수 있다.

### 39. Agent 시대에는 고품질 Tool-use Synthetic Data를 만드는 능력이 중요하다.

### 40. 현실의 API는 너무 많기 때문에 모든 Tool 사용법을 Model Weight에 암기시키기 어렵다.

### 41. 따라서 “처음 보는 API라도 Documentation을 읽고 사용할 수 있는 Skill”을 학습시키는 방향이 중요하다.

### 42. 강의에서는 수만 개 API를 이용한 Tool-learning 연구 흐름을 소개한다.

### 43. Inference 시 User Task와 관련된 API Documentation을 먼저 Retrieval할 수 있다.

### 44. API Retrieval은 Knowledge RAG와 구조적으로 유사하다.

### 45. 관련 API의 Function Name, Purpose, Parameter Description 등을 LLM에게 제공하면 처음 보는 Tool도 사용할 가능성이 생긴다.

### 46. Tool-use Benchmark에서는 실제 API 여부, Multi-tool Usage, API Retrieval, Multi-step Planning 등의 요소를 평가할 수 있다.

### 47. Web Agent는 Browser-specific Information을 추가로 활용할 수 있다.

### 48. Computer-use Agent는 일반 GUI만 사용하기 때문에 더 범용적이지만 더 어렵다.

### 49. Agent Task는 Action Sequence가 길어질 수 있다.

### 50. 긴 Trajectory에서 Final Success/Failure Reward만 사용하면 어떤 중간 Step이 문제인지 파악하기 어렵다.

### 51. 중간 Process를 평가하는 Reward를 Process Reward라고 한다.

### 52. Process를 평가하는 Model을 Process Reward Model(PRM)이라고 한다.

### 53. Agent Capability를 높이려면 Model의 Tool-use Skill뿐 아니라 사용할 수 있는 좋은 Tool Ecosystem 자체도 중요하다.

### 54. 많은 External Software를 Agent에 연결하려 하면 서로 다른 API/Integration 방식이 문제가 된다.

### 55. MCP는 LLM/Agent와 다양한 External Tool/Software를 연결하기 위한 공통 Protocol을 제공하는 방향으로 강의에서 설명한다.

### 56. MCP의 핵심 장점은 Standardization, Compatibility, Extensibility, Tool Reuse다.

### 57. MCP는 Tool 자체가 아니라 Tool Integration을 위한 Interface/Protocol이다.

### 58. Function Calling 능력과 MCP Protocol은 서로 다른 Concept이다.

### 59. 향후 Software 자체가 Intelligent Agent가 되면 중앙 Agent→Passive Tool 구조에서 Agent↔Agent 구조로 발전할 수 있다.

### 60. 강의에서는 Agent 간 Communication Standard의 방향으로 A2A를 소개한다.

### 61. 강의의 관점에서 MCP는 Agent↔Tool, A2A는 Agent↔Agent로 구분하면 된다.

### 62. Multi-Agent Ecosystem에서는 Agent 간 연결 자체보다 Permission, Security, Safety, Privacy가 매우 중요하다.

### 63. Tool 연결이 쉬워질수록 Agent가 수행할 수 있는 실제 Action도 많아지므로 Action Safety가 중요해진다.

### 64. Agent에게 Tool Permission을 무제한 제공하면 위험하다.

### 65. Authentication, Authorization, Credential Management 같은 Security 설계가 필요하다.

### 66. Personal Agent의 Knowledge를 다른 Agent가 어디까지 접근할 수 있는지도 중요한 Privacy 문제다.

### 67. Agent System의 핵심은 LLM이 모든 일을 직접 하는 것이 아니라 적절한 Tool과 Specialized Module을 선택하고 조정하는 Decision Maker가 되는 것이다.

---

# 143. 이번 강의 전체 논리

```text
지금까지 LLM은
주로 답변을 생성했다.
      ↓
그런데 현실의 문제는
한 번 답하고 끝나지 않는다.
      ↓
Environment와
계속 Interaction해야 한다.
      ↓
Agent


Agent란?
      ↓
Observation
↓
Policy
↓
Action
↓
Environment 변화
↓
New Observation
↓
반복


LLM이 강력해졌다.
      ↓
Policy / Controller로 사용
      ↓
LLM Agent


그럼 Action은 무엇인가?
      ↓
현실에서는
Tool Use
      ↓
Search
Calculator
Database
API
Software
Robot
...


예:
오늘 날씨가 궁금하다.
      ↓
LLM이
Weather Tool 필요 판단
      ↓
Function Call
      ↓
외부 App이 실행
      ↓
Result
      ↓
다시 LLM
      ↓
Natural Language Answer


핵심 문제:
언제 Tool을 써야 하지?
      ↓

방법 1
Prompt에 Tool 설명

방법 2
Tool-use Data로 Training


Training Data는?
      ↓
Human Demonstration
      ↓
Trajectory
      ↓
Imitation Learning

또는
      ↓
Synthetic Tool-use Data
      ↓
유용한 Sample만 Filtering


그런데 Tool이
몇 개가 아니라 수만 개다.
      ↓
모두 암기 불가능
      ↓
관련 Tool을 먼저 검색
      ↓
API Documentation Retrieval
      ↓
LLM에게 설명 제공
      ↓
처음 보는 Tool도 사용


Tool 사용이 더 복잡해짐
      ↓
Web Agent
      ↓
Computer-use Agent
      ↓
긴 Action Trajectory


Final Reward만으로
어디서 실패했는지 알기 어렵다.
      ↓
Process Reward
      ↓
PRM


이제 문제는
Tool Skill만이 아니다.
      ↓
Slack
Gmail
Calendar
Database
...
연결 방식이 모두 다름
      ↓
Integration Hell
      ↓
공통 Protocol이 필요
      ↓
MCP


현재:
LLM Agent
↔
Software Tool


하지만 미래에는
Software도 Agent가 된다.
      ↓
Agent
↔
Agent
      ↓
A2A
      ↓
Multi-Agent Ecosystem


그런데
모두 연결하면 위험하다.
      ↓
Permission
Security
Safety
Privacy
      ↓
Agent 시대의
핵심 Engineering 문제
```

---

# 144. 이번 강의 핵심 한 문장

> **LLM Agent는 단순히 Input에 대한 Text Output을 생성하는 Model이 아니라 Environment의 Observation을 받아 목표를 달성하기 위한 Action을 계획하고 Search·Calculator·API·Browser·Software 같은 Tool을 선택·실행한 뒤 그 결과를 다시 관찰하는 Interaction Loop를 반복하는 Decision-making System이며, 이러한 Tool Use는 Prompting이나 Human/Synthetic Trajectory 기반 Imitation Learning·Reinforcement Learning으로 학습할 수 있고, 수많은 외부 Tool을 실제 Agent에 연결할 때 발생하는 서로 다른 Integration 방식을 표준화하기 위한 방향이 MCP이며, 향후 Tool 자체가 Agent화되면 A2A와 같은 Agent-to-Agent Communication 및 Permission·Security·Safety·Privacy 설계가 더욱 중요해진다.**

가장 짧게 기억하면 다음과 같다.

```text
Agent
= 보고 → 판단하고 → 행동하고 →
  결과를 다시 보고 반복한다.

LLM Agent
= LLM이
  Decision Maker / Controller가 된다.

Tool Use
= LLM이 직접 모든 일을 하지 않고
  필요한 Function·Software에 위임한다.

Function Calling
= 어떤 Tool을 어떤 Argument로
  실행할지를 LLM이 출력한다.

Tool Learning
= 언제 어떤 Tool을 쓸지
  Trajectory를 통해 학습한다.

PRM
= 최종 결과뿐 아니라
  중간 행동 과정도 평가한다.

MCP
= Agent와 Tool을
  공통 규격으로 연결한다.

A2A
= Agent와 Agent가
  서로 소통한다.

Agent 시대의 핵심 문제
= 연결 자체보다
  권한·보안·안전·Privacy다.
```