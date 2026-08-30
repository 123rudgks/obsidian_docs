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

