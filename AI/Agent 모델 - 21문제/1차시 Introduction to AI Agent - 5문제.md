[강의 링크](https://www.youtube.com/watch?v=CkvA4o04fwU)

## 시험 범위에 포함되는 내용
| 시험 토픽                    | 근거                                                                                  |
| ------------------------ | ----------------------------------------------------------------------------------- |
| **AI와 AI Agent**         | 일반 LLM과 목표를 자율적으로 수행하는 Agent의 차이                                                    |
| **LLM Agent의 특성**        | Autonomy, Perception, Memory, Reasoning & Planning, Learning & Adaptation, Tool Use |
| **AI Agent 동작**          | Observe → Think/Plan → Act → Evaluate → Adapt → Repeat                              |
| **AI Agent의 Tool 활용**    | Search, Gmail, DB, Code Executor 등 외부 Capability 활용                                 |
| **AI 스케일링과 Physical AI** | LLM Scaling 발전과 Agentic AI → Physical AI 흐름 모두 설명                                   |

# 0. 강의 전체 개요

이번 강의의 출발점은 단순하다.

```text
LLM은 매우 강력해졌다.
↓
하지만
질문을 받아 답하는 것만으로
현실의 복잡한 문제를 해결할 수 있는가?
↓
아니다.
```

현실의 문제는 보통 한 번의 `Input → Output`으로 끝나지 않는다.

```text
상황을 이해하고
↓
목표를 파악하고
↓
계획을 세우고
↓
실행하고
↓
결과를 확인하고
↓
필요하면 계획을 수정하고
↓
다시 실행
```

해야 한다.

이러한 구조로 LLM을 확장한 것이 **AI Agent**다.

강의 전체 흐름은 다음과 같다.

```text
AI 발전
↓
Perception AI
↓
Generative AI / LLM
↓
Agentic AI
↓
Physical AI


LLM은 똑똑하다.
↓
하지만
수동적이고
단일 Model Capability에는 한계가 있음
↓
Agent 필요


AI Agent
↓
특정 목표를
스스로 추구하는 System
↓
Autonomy
Perception
Memory
Reasoning & Planning
Learning & Adaptation
Tool Use


그리고 실제 Agent 성능은
LLM 하나만으로 결정되지 않는다.
↓
Model
+
Agent Harness
+
System
+
Tools
+
Memory
+
Environment
```

---

# 1. 이번 강의의 목표

강의에서 가장 중요하게 제시한 목표는

> **“AI Agent가 무엇인지, 어떤 핵심 특성을 가지고 있는지 쉽게 설명할 수 있는 것”**

이다.

즉 세부 Algorithm을 외우는 것이 먼저가 아니다.

다른 사람이

```text
AI Agent가 뭐야?
```

라고 물었을 때

```text
AI Agent는
AI를 이용해 목표를 이해하고,
계획·행동·평가를 자율적으로 반복하면서
목표를 달성하는 System이다.
```

정도로 설명할 수 있어야 한다.

---

# 2. Agent가 중요해진 배경

강의에서는 최근 AI 산업의 관심이

```text
좋은 LLM을 만든다.
```

에서

```text
좋은 LLM을
어떻게 실제 문제 해결 System으로 만들까?
```

로 빠르게 이동하고 있다고 설명한다.

특히 Coding Agent를 예로 들며, 최근 Agent 성능이 빠르게 발전해 실제 업무에서 점점 더 많은 부분을 Agent에 맡길 수 있게 되었다는 강사의 경험을 소개한다.

---

# 3. 좋은 AI Service에는 두 가지가 필요하다

강의에서 반복해서 강조하는 중요한 관점이다.

```text
좋은 Model
+
좋은 System
```

이 둘이 모두 필요하다.

---

# 4. Model

Model 자체가 충분히 똑똑해야 한다.

```text
Language Understanding

Reasoning

Generation

Knowledge
```

등의 Capability가 필요하다.

---

# 5. System

하지만 아무리 좋은 Model이라도 실제 User가 안정적으로 사용할 수 있도록

```text
Serving

Infrastructure

Software

Latency

Reliability

Scalability
```

등을 잘 설계하지 않으면 좋은 Service가 될 수 없다.

강의에서는 초기 생성형 AI Service의 성공도 뛰어난 Model뿐 아니라 많은 User가 동시에 안정적으로 사용할 수 있도록 만든 System Engineering이 함께 있었기 때문이라는 관점으로 설명한다.

---

# 6. Model Engineer와 System Engineer의 간극

현업에서는 흔히

```text
Model을 잘 아는 사람
→ System을 잘 모름

System을 잘 아는 사람
→ Model을 Black Box처럼 봄
```

이라는 문제가 생길 수 있다.

그러면 문제가 발생했을 때

```text
Model Team:
"System 문제 아니야?"

System Team:
"Model 문제 아니야?"
```

가 될 수 있다.

---

# 7. 그래서 양쪽을 이해하는 사람이 중요하다

강의에서는 AI 시대에도

```text
AI / Model
+
Traditional CS / System
```

양쪽을 이해하는 역량을 중요하게 본다.

특히 둘 사이의 **Bridge** 역할을 할 수 있는 사람이 실제 Organization에서 가치가 높다는 강사의 산업 경험을 공유한다.

---

# 8. Agent에서는 이 관점이 더 중요하다

Agent는 단순 Model 하나가 아니다.

```text
LLM

Memory

Tools

Workflow

Environment

External Software

Other Agents
```

등을 하나의 System으로 구성해야 한다.

따라서 Agent 시대에는

> **“Model을 얼마나 잘 만들었는가?”뿐 아니라 “Model 주변을 얼마나 잘 설계했는가?”**

가 점점 중요해진다.

---

# 9. Physical AI까지 가면 Model + System 통합은 더 중요해진다

LLM은 주로 Software 안에서 동작한다.

Physical AI는 여기에

```text
Robot Hardware

Sensor

Actuator

Physical Environment
```

까지 추가된다.

따라서

```text
AI Model만 이해
```

해서도 부족하고,

```text
Hardware/System만 이해
```

해서도 부족하다.

Physical AI를 제대로 이해하려면 여러 Layer를 함께 봐야 한다는 것이 강의의 메시지다.

---

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

강의에서는 Agent의 필요성을 의료 Domain으로 설명한다.

사람은 누구나 좋은 의료 Service를 받고 싶다.

하지만

```text
전문가 부족

지역 격차

긴 대기 시간

의료 Resource 제한
```

이 존재한다.

---

# 22. General LLM의 초기 의료 한계

초기의 범용 LLM에게 전문 Medical Case를 질문하면

```text
넓은 설명
추상적인 설명
```

은 가능했지만

```text
전문적인 검사
치료
약물
Domain-specific Decision
```

수준에서는 부족할 수 있었다고 강의 경험을 소개한다.

---

# 23. Generality와 Depth

초기 LLM에서 놀라웠던 것은

```text
하나의 Domain을 매우 깊게
```

라기보다

```text
많은 Domain을 넓게
```

잘한다는 점이었다.

즉

```text
Generality ↑

하지만

Domain Depth는 제한될 수 있음
```

이다.

---

# 24. Domain-specific LLM

이후에는 Medical, Legal 등 특정 Domain Data를 활용하면서 보다 깊은 전문성을 가진 Model들이 발전한다.

즉 발전 방향이

```text
General LLM
↓
General Capability 유지
+
Domain-specific Expertise 강화
```

로 이어진다.

---

# 25. 하지만 한 명의 전문가로도 부족한 문제가 있다

의료에서는 실제로 **다학제 진료**가 존재한다.

하나의 질병도

```text
영상의학과

외과

내과

병리과

종양내과
```

등 서로 다른 전문 관점에서 볼 수 있다.

---

# 26. 왜 여러 전문가가 필요한가?

같은 문제라도 전문 분야에 따라

```text
보는 Evidence

중요하게 생각하는 Risk

진단 과정

치료 Strategy
```

가 다르다.

한 명의 뛰어난 사람만으로 모든 관점을 완벽히 포함하기 어렵다.

---

# 27. Multi-Agent 의료 Example

강의에서는 하나의 Medical Question을 여러 전문 Agent에게 각각 풀게 하는 사례를 소개한다.

```text
Medical Question
↓
Specialist Agent A

Specialist Agent B

Specialist Agent C
...
```

각 Agent가 자신의 전문 관점에서 답한다.

---

# 28. 단순 Majority Vote

가장 쉬운 방법은

```text
각 Agent 답변
↓
Majority Vote
```

다.

하지만 강의 사례에서는 단순 다수결이 항상 최선은 아니었다.

---

# 29. Debate

Agent끼리 서로 자신의 Reasoning을 설명하고 토론하게 한다.

```text
Independent Answers
↓
Agent Discussion
↓
Argument / Counterargument
↓
Final Answer
```

강의에서 소개된 사례에서는 단순 다수결과 다른 결론으로 이동했고 최종적으로 Correct Answer를 얻었다고 설명한다.

---

# 30. 이 사례가 Agent 필요성을 보여주는 이유

핵심은

```text
Single LLM
↓
Capability Limit
```

이 존재한다는 것이다.

더 복잡한 문제를 해결하려면

```text
하나의 LLM을 무조건 크게 만든다.
```

보다

```text
여러 LLM/Agent를
잘 활용하고 조직한다.
```

는 접근도 가능하다.

---

# 31. Agent Harness

여기서 강의가 강조하는 개념이 **Agent Harness**다.

Agent의 성능을 높이기 위해

```text
어떤 Model을 쓰고

어떤 Agent를 구성하고

어떤 Tool을 연결하고

어떤 Workflow를 만들고

어떻게 Feedback을 주고

어떤 Memory를 사용할 것인가
```

를 전체적으로 설계한다.

---

# 32. Physical AI 의료 사례

강의에서는 수술실을 또 다른 사례로 든다.

실제 수술에는

```text
Surgeon

Assistant

Nurse

Other Support Staff
```

가 필요하다.

집도의가 있어도 보조 인력이 부족하면 수술 자체를 진행하지 못할 수 있다.

---

# 33. Robot이 의사를 바로 대체해야 하는가?

가장 어려운 집도의 역할을 바로 Robot으로 대체하는 것보다

```text
수술 보조

도구 전달

조직 고정

조명 조절
```

같은 Support Task를 Physical AI로 먼저 해결하는 방향도 생각할 수 있다.

---

# 34. 수술 보조에는 Context Understanding이 필요하다

숙련된 Assistant는 Surgeon이 모든 것을 길게 말하지 않아도

```text
현재 수술 단계

다음에 필요한 도구

Surgeon의 Intent
```

를 예측할 수 있다.

Robot이 이것을 하려면 긴 수술 Process 전체를 이해해야 한다.

---

# 35. 하나의 작은 Model로 모든 것을 이해하기 어렵다

병원 내부에 매우 거대한 Model을 항상 배치하기에는

```text
Compute

Latency

Hardware

Cost
```

문제가 있을 수 있다.

따라서

```text
Small / Specialized Models
+
Agent System
```

을 이용해 복잡한 Process를 분해하는 방향을 생각할 수 있다.

---

# 36. Complex Task Decomposition

Agent가 전체 수술 Workflow를 분석한다.

```text
Complex Goal
↓
Subtask 1
Subtask 2
Subtask 3
...
↓
각 Specialized Model / Tool에 할당
```

한다.

이러한 **복잡하고 긴 문제를 작은 문제로 분해하여 해결하는 것**이 Agent가 중요한 이유 중 하나다.

---

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

# 87. 이것이 Agent의 핵심 Loop

가장 간단하게 표현하면

```text
Observe
↓
Think
↓
Plan
↓
Act
↓
Evaluate
↓
Learn / Replan
↓
Repeat
```

이다.

---

# 88. Agent Framework

이러한 Component를 직접 처음부터 구현할 수도 있지만 이미 다양한 Agent Framework가 존재한다.

강의에서는 특정 Framework 이름 자체보다

> **어떤 Agent Feature를 특히 잘 지원하는 Framework인가?**

를 보는 것이 중요하다고 설명한다.

---

# 89. Framework마다 강점이 다를 수 있다

예:

```text
Framework A
→ Memory에 강함

Framework B
→ Feedback Loop에 강함

Framework C
→ 특정 Domain에 특화

Framework D
→ Tool Integration에 강함
```

일 수 있다.

---

# 90. Framework 선택 기준

Project Goal을 먼저 본다.

```text
내 Project에서
가장 중요한 Agent Capability는 무엇인가?
```

를 판단한다.

그다음 해당 Capability가 잘 구현된 Framework/Harness를 선택한다.

---

# 91. Agent를 “만드는 것” 자체는 점점 쉬워진다

강의에서는 생성형 AI와 Framework가 발전하면서

```text
Agent Prototype 생성
```

자체는 점점 쉬워지고 있다고 설명한다.

중요한 것은

```text
무엇을 만들 것인가?

어떤 구조로 만들 것인가?

어떤 문제를 풀 것인가?
```

라는 **Direction**이다.

---

# 92. 방향성이 중요한 이유

Agent는 Framework를 실행하면 무엇인가 Output을 낼 수 있다.

하지만

```text
무엇이 Good Output인지

어떤 Workflow가 맞는지

어떤 실패가 위험한지
```

는 Domain Knowledge와 Engineering Judgment가 필요하다.

---

# 93. Agent 시대에도 기본 기술 지식이 중요한 이유

강의에서는 Coding Agent가 아무리 좋아져도 Coding 자체를 이해해야 한다고 말한다.

왜냐하면

```text
Agent가 만든 Code가 맞는지

틀리면 어디가 문제인지

어떤 Risk가 있는지
```

판단하려면 기본기가 필요하기 때문이다.

---

# 94. AI를 사용하는 사람의 역할 변화

단순히 모든 것을 직접 구현하는 것보다

```text
Agent에게 무엇을 시킬지

어떤 Goal을 줄지

결과를 어떻게 평가할지

어떤 방향으로 System을 설계할지
```

결정하는 능력이 중요해진다.

---

# 95. Agent System이 Model Gap을 보완할 수도 있다

강의에서는 Model Benchmark만 약간 뒤처지더라도

```text
Better Tools

Better Memory

Better Workflow

Better Agent Architecture
```

를 이용하면 실제 Application Performance 차이를 보완할 수 있다는 관점을 제시한다.

즉

```text
Best Model
≠
Always Best Agent System
```

이다.

---

# 96. Harness가 중요한 이유

실제 Agent Product의 차이는

```text
LLM 자체
```

뿐 아니라

```text
LLM을 둘러싼 Harness
```

에서도 발생한다.

Harness에는

```text
Tool Routing

Memory

Prompt

Sub-agents

Evaluation

Retry

Context Management

Workflow
```

등이 들어갈 수 있다.

---

# 97. Closed Agent System의 문제

강의에서는 기업 입장에서 내부 Agent Architecture가 완전히 보이지 않는 Service를 사용할 때 몇 가지 문제가 있을 수 있다고 설명한다.

```text
Data가 어떻게 사용되는가?

어떤 Tool이 호출되는가?

어떤 Agent가 Data를 보는가?

어디에서 Failure가 발생했는가?
```

를 확인하기 어려울 수 있다.

---

# 98. Transparency

따라서 Enterprise Agent에서는

```text
Observability

Controllability

Debuggability

Data Governance
```

가 중요하다.

Agent Harness가 Open하거나 내부적으로 제어 가능하면 이런 요소를 관리하기 쉬워질 수 있다.

이 부분에서 강의는 특정 Open Agent/Harness 생태계의 장점을 사례로 설명하지만, 핵심은 특정 제품보다 **Agent 내부 구조의 투명성과 Control**이다.

---

# 99. AI의 네 단계 다시 정리

|단계|핵심 능력|
|---|---|
|Perception AI|세상을 인식|
|Generative AI|새로운 Content 생성|
|Agentic AI|목표를 향해 자율적으로 판단·행동|
|Physical AI|현실 세계에서 실제 Action|

---

# 100. Generative AI vs Agentic AI

### Generative AI

```text
User Request
↓
Generate
↓
Output
```

### Agentic AI

```text
Goal
↓
Understand
↓
Plan
↓
Tool / Action
↓
Evaluate
↓
Replan
↓
Goal
```

이다.

---

# 101. 일반 LLM vs AI Agent

|구분|일반 LLM|AI Agent|
|---|---|---|
|기본 동작|Prompt → Response|Goal → 반복적 행동|
|자율성|낮음|높음|
|Planning|제한적|핵심 기능|
|Environment Interaction|제한적|적극적|
|Memory|Context 중심|장·단기 Memory 가능|
|Tool Use|없을 수도 있음|중요|
|Feedback|한 번 생성 후 종료 가능|평가·수정|
|목표|좋은 Response|실제 Goal 달성|

---

# 102. Agent와 Automation의 차이

### Automation

```text
Predefined Rule
↓
Predefined Action
```

### Agent

```text
Goal
+
Current State
↓
Reasoning
↓
Dynamic Action
```

이다.

Agent가 더 동적인 Decision을 수행한다.

---

# 103. Agent와 Workflow의 차이

Workflow:

```text
A → B → C
```

순서가 개발자에 의해 고정되어 있을 수 있다.

Agent:

```text
A
↓
현재 결과 확인
↓
B?
C?
다시 A?
Tool?
```

를 결정한다.

---

# 104. Agent와 LLM이 완전히 별개의 개념은 아니다

현대 AI Agent의 Controller로 LLM이 많이 사용된다.

따라서

```text
LLM
⊂
Agent System의 중요한 Component
```

라고 볼 수 있다.

하지만

```text
Agent = LLM 하나
```

는 아니다.

Agent System에는 추가로

```text
Memory

Tools

Environment

Planning Logic

Evaluation

Execution
```

등이 들어갈 수 있다.

---

# 105. Agent라는 용어가 넓게 사용되는 문제

강의에서는 최근

```text
AI

LLM

Agent
```

라는 용어가 사회적으로 다소 혼용되고 있다고 설명한다.

실제 Product가 이미 내부적으로 여러 Agent와 Tool을 포함하는 경우가 많기 때문이다.

하지만 개념적으로는 구분해 두는 것이 좋다.

---

# 106. 핵심적인 Agent Definition

강의 흐름을 가장 정확하게 압축하면 다음과 같다.

> **AI Agent는 AI/LLM을 기반으로 Environment와 Context를 인식하고, 주어진 Goal을 달성하기 위한 계획을 스스로 수립하며, 필요한 Tool이나 다른 Agent를 사용해 행동하고, 행동 결과를 평가·기억하여 필요할 경우 계획을 수정하는 과정을 반복하는 자율적 Software System이다.**

---

# 107. 한 줄 Definition

시험에서 짧게 말하면:

```text
AI Agent
=
목표를 이해하고
스스로 계획·행동·평가를 반복해
목표를 달성하는 AI System
```

---

# 108. Agent를 왜 사용하는가?

가장 중요한 이유:

```text
Single LLM이
한 번의 Response만으로 해결하기 어려운

복잡하고

긴

다단계 문제
```

를 해결하기 위해서다.

---

# 109. Long-horizon Task

예:

```text
Research

Software Development

Medical Workflow

Travel Planning

Business Process

Physical Robot Task
```

등은 여러 Step으로 구성된다.

Agent는 이를

```text
Goal
↓
Task Decomposition
↓
Subtasks
↓
Execution
↓
Evaluation
```

으로 처리한다.

---

# 110. Agent의 현실적 한계

강의 후반에서는 Agent가 이미 잘 동작하고 있지만 아직 중요한 기술적 문제가 남아 있다고 설명한다.

대표적인 문제는 다음과 같다.

```text
Efficiency

Memory Management

Context Management

Token Cost

Privacy

Security

Unsafe Autonomous Actions

Guardrails
```

---

# 111. Efficiency

Agent는 한 번 LLM을 호출하는 것이 아니라

```text
Reasoning
↓
Tool
↓
Reasoning
↓
Evaluation
↓
Retry
```

를 반복할 수 있다.

따라서 Token과 Compute를 많이 사용할 수 있다.

---

# 112. Context Management

Agent가 오래 동작하면

```text
Conversation

Tool Results

Memory

Documents

Plans
```

이 계속 쌓인다.

모든 정보를 Context에 넣으면 비용과 Noise가 증가한다.

따라서 어떤 Context를 유지할지 관리해야 한다.

---

# 113. Memory Management

```text
무엇을 저장할지

어떻게 요약할지

언제 잊을지

어떻게 검색할지
```

가 필요하다.

Memory는 뒤 차시에서 더 깊게 다뤄진다.

---

# 114. Privacy

Agent가 Personal/Enterprise Data를 사용하면

```text
어디에 저장되는가?

외부로 전송되는가?

다른 Agent가 볼 수 있는가?
```

가 중요하다.

---

# 115. Security

Tool을 사용할 수 있다는 것은 Agent가 실제 Action까지 할 수 있다는 뜻이다.

따라서

```text
Authentication

Authorization

Permission
```

등이 중요해진다.

---

# 116. Autonomy가 커질수록 Risk도 커진다

Text Generation이 틀리면 잘못된 Answer일 수 있다.

Agent Action이 틀리면

```text
잘못된 Email 발송

잘못된 File 변경

잘못된 Purchase

잘못된 Medical Action
```

처럼 현실적인 피해로 이어질 수 있다.

---

# 117. Guardrail

따라서 Agent의 행동 영역에

```text
Allowed Actions

Forbidden Actions

Approval Requirement

Safety Check
```

등의 Guardrail이 필요하다.

---

# 118. 의료 Agent와 Human Confirmation

강의에서는 특히 의료처럼 High-stakes Domain에서는 Agent가 아무리 강해져도 최종 의사결정에 Human Professional이 참여하는 것이 중요하다는 강사의 관점을 제시한다.

```text
Agent Recommendation
↓
Doctor Review
↓
Final Decision
```

과 같은 구조다.

---

# 119. Human Judgment의 역할

Agent 시대에도

```text
Agent를 얼마나 믿을지

어디까지 자율성을 줄지

언제 Human Approval을 받을지
```

를 결정하는 인간의 판단이 중요하다.

---

# 120. Agent의 Autonomy는 무제한 자유가 아니다

Agent가 자율적이라는 것은

```text
아무 행동이나 마음대로 한다.
```

는 뜻이 아니다.

실제 System에서는

```text
Goal

Rules

Permissions

Safety Constraints

Environment
```

안에서 자율적으로 행동한다.

---

# 121. 이번 강의에서 Agent를 이해하는 가장 중요한 구조

```text
                  Goal
                   ↓
              Autonomy
                   ↓
              Perception
                   ↓
                 State
                   ↓
         Memory ─→ Reasoning
                   ↓
                Planning
                   ↓
          ┌────────┴────────┐
          ↓                 ↓
      Direct Action       Tool Use
          ↓                 ↓
          └────────┬────────┘
                   ↓
               Result
                   ↓
             Evaluation
                   ↓
        Learning / Adaptation
                   ↓
              Replanning
                   ↓
                 반복
```

---

# 122. AI Agent의 여섯 특성을 질문 형태로 기억

### Autonomy

```text
스스로 무엇을 해야 할지
결정할 수 있는가?
```

### Perception

```text
현재 상황을
제대로 이해하는가?
```

### Memory

```text
과거 정보를
기억하고 활용하는가?
```

### Reasoning & Planning

```text
복잡한 Goal을
작은 Step으로 나눌 수 있는가?
```

### Learning & Adaptation

```text
결과와 Feedback을 보고
행동을 수정하는가?
```

### Tool Use

```text
필요하면 외부 Capability를
활용할 수 있는가?
```

---

# 123. 이후 강의들과의 연결

이번 강의는 이후 Agent 모델 과정의 Foundation이다.

## 다음 — Multi-Agent System

```text
Single Agent도 한계가 있다.
↓
여러 Agent를 조직
↓
Cooperation / Competition
↓
Collective Intelligence
```

---

## Memory & Tool

```text
Agent가
모든 것을 기억하거나
직접 할 수 없다.
↓
Memory
+
External Tools
```

---

# 124. 세 강의를 하나로 연결하면

```text
Introduction to AI Agent
↓
Agent란 무엇인가?
↓
Autonomy
Perception
Memory
Planning
Learning
Tool Use


Multi-Agent System
↓
Agent 하나로 부족하면?
↓
여러 Agent를 조직


Memory & Tool
↓
Agent가 모르는 것과
못 하는 것은?
↓
External Memory와 Tool로 확장
```

---

# 125. 자주 헷갈리는 점 ① — AI Agent는 LLM인가?

아니다.

LLM은 Agent의 핵심 Brain/Controller로 사용할 수 있지만 Agent 전체는

```text
LLM
+
Memory
+
Tools
+
Workflow
+
Environment
+
Evaluation
```

등으로 구성될 수 있다.

---

# 126. 자주 헷갈리는 점 ② — AI Agent는 Chatbot인가?

Chatbot은 보통

```text
질문
↓
답변
```

중심이다.

Agent는

```text
Goal
↓
다단계 Planning
↓
Action
↓
Evaluation
↓
반복
```

을 수행한다.

---

# 127. 자주 헷갈리는 점 ③ — Tool을 쓰면 무조건 Agent인가?

아니다.

개발자가

```text
이 질문이면 항상 Search
```

처럼 Tool Call을 완전히 고정할 수도 있다.

Agent의 핵심은 **상황에 따라 스스로 판단하는 자율성**이다.

---

# 128. 자주 헷갈리는 점 ④ — Agent는 사람이 아무것도 말하지 않아도 자기 Goal을 만드는가?

강의의 자율성을 그렇게까지 이해할 필요는 없다.

대부분의 Agent는 User나 System이 준 **Goal** 안에서

```text
어떻게 목표를 달성할지
```

를 자율적으로 결정한다.

---

# 129. 자주 헷갈리는 점 ⑤ — Planning은 그냥 Chain-of-Thought인가?

관련은 있지만 Agent Planning은 특히

```text
실제로 수행할 Action

Tool

Subtask

Execution Order
```

를 결정한다는 점이 중요하다.

---

# 130. 자주 헷갈리는 점 ⑥ — Memory는 Context Window인가?

Context도 일종의 단기 정보 저장 역할을 하지만 Agent Memory는 더 넓다.

```text
Past Conversation

User Preference

Past Experience

External Memory Store
```

까지 포함할 수 있다.

---

# 131. 자주 헷갈리는 점 ⑦ — Memory는 많이 저장할수록 좋은가?

아니다.

강의에서는 **기억만큼 망각도 중요하다**고 설명한다.

---

# 132. 자주 헷갈리는 점 ⑧ — Feedback을 받으면 Model Weight가 매번 바뀌는가?

반드시 아니다.

현재 Run에서

```text
Self-evaluation

Replanning

Memory Update
```

만 해도 Adaptation이 가능하다.

---

# 133. 자주 헷갈리는 점 ⑨ — Agent는 Single Model보다 항상 좋나?

아니다.

단순 Task에서는 Agent의

```text
Latency

Token Cost

Complexity
```

가 오히려 낭비일 수 있다.

Agent는 복잡하고 긴 Process에서 특히 가치가 크다.

---

# 134. 자주 헷갈리는 점 ⑩ — 가장 큰 LLM을 쓰면 Agent가 필요 없나?

아니다.

아무리 강한 LLM도

```text
최신 정보

Private Data

External Software Execution

Long-running Workflow
```

에는 외부 System이 필요할 수 있다.

---

# 135. 자주 헷갈리는 점 ⑪ — Agent Framework를 하나 고르면 모든 문제가 해결되는가?

아니다.

Framework는 Building Block을 제공한다.

실제 성능은

```text
Domain

Workflow

Memory

Tools

Evaluation

Guardrails
```

를 어떻게 설계하느냐에 달려 있다.

---

# 136. 자주 헷갈리는 점 ⑫ — AI 시대에는 System Engineering이 덜 중요해지는가?

강의의 메시지는 반대다.

Agent가 복잡해질수록 Model과 Software/System을 함께 이해하는 것이 더 중요해진다.

---

# 137. 자주 헷갈리는 점 ⑬ — Physical AI는 Robot Model만 잘 만들면 되는가?

아니다.

```text
Model

Agent

Hardware

Sensor

System

Data

Environment
```

를 통합해야 한다.

---

# 138. 자주 헷갈리는 점 ⑭ — Perception AI가 Agentic AI에 의해 대체되는가?

아니다.

Agent가 행동하려면 먼저 Environment를 인식해야 한다.

즉 Perception은 Agentic AI의 기반이다.

---

# 139. 자주 헷갈리는 점 ⑮ — Agentic AI와 Physical AI는 같은가?

아니다.

Agentic AI는 Digital Environment에서도 존재할 수 있다.

Physical AI는 실제 Physical World에서 행동하는 AI까지 포함한다.

---

# 140. 이번 강의에서 반드시 기억할 핵심

### 1. 이번 강의의 가장 중요한 목표는 AI Agent의 정의와 핵심 특성을 이해하는 것이다.

### 2. 좋은 AI Service를 만들기 위해서는 뛰어난 Model뿐 아니라 이를 실제로 운영하는 System Engineering이 중요하다.

### 3. Agent 시대에는 Model과 Model 주변 System을 함께 이해하는 역량이 더욱 중요해진다.

### 4. LLM은 Transformer, Compute, 대규모 Internet Data를 기반으로 빠르게 발전했다.

### 5. Physical AI는 LLM과 달리 대규모 Robot 행동 Data를 확보하기 어렵다는 문제가 있다.

### 6. General LLM은 여러 Domain을 넓게 처리하지만 Domain-specific Depth에는 한계가 있을 수 있다.

### 7. Domain-specific LLM은 특정 Domain Data를 활용해 전문성을 높이는 방향이다.

### 8. 하지만 현실의 복잡한 문제는 한 명의 전문가 또는 하나의 LLM만으로 해결하기 어려울 수 있다.

### 9. 의료의 다학제 협업은 Multi-Agent 필요성을 이해하기 좋은 사례다.

### 10. 여러 Specialized Agent가 독립적으로 판단하고 서로 토론하도록 만들 수 있다.

### 11. 단순 Majority Vote보다 Agent 간 Discussion을 통해 더 좋은 결론에 도달할 가능성도 있다.

### 12. AI Agent의 중요한 목적은 Single LLM Call로 해결하기 어려운 복잡하고 긴 Task를 수행하는 것이다.

### 13. Agent는 복잡한 Process를 작은 Subtask로 분해하여 각각 실행할 수 있다.

### 14. AI Agent는 AI/LLM을 사용해 특정 Goal을 스스로 추구하는 Software System이다.

### 15. Agent는 계획 → 실행 → 결과 분석 → 재계획의 Loop를 반복할 수 있다.

### 16. Agent에서 가장 중요한 핵심 특징으로 강의는 Autonomy를 강조한다.

### 17. Autonomy는 User가 모든 Step을 명시하지 않아도 Goal과 Context를 이용해 다음 행동을 판단하는 능력이다.

### 18. Perception은 Action 전에 Environment와 Context에서 필요한 정보를 파악하는 능력이다.

### 19. Agent는 필요하면 Web이나 다른 Source에서 정보를 추가로 수집할 수 있다.

### 20. Memory는 과거 Context와 Experience를 이후 Decision에 활용하는 능력이다.

### 21. Agent Memory에서는 무엇을 기억할지뿐 아니라 무엇을 잊을지도 중요하다.

### 22. 초기 Memory Architecture에서는 Short-term과 Long-term Memory를 구분할 수 있다.

### 23. 단순히 오래된 Memory를 모두 제거하는 것은 Human Memory와 다를 수 있으며 Importance도 고려해야 한다.

### 24. Reasoning & Planning은 복잡하고 긴 Goal을 작은 Subtask로 분해하고 실행 순서를 설계한다.

### 25. Learning & Adaptation은 Self-evaluation이나 User Feedback으로 결과를 수정하는 능력이다.

### 26. Agent의 Adaptation이 항상 Parameter Update를 의미하는 것은 아니다.

### 27. Agent는 부족한 정보를 User에게 다시 질문하여 Plan을 수정할 수도 있다.

### 28. Tool Use는 Agent가 Search, Gmail, Database, Code Executor 등 외부 Capability를 사용하는 능력이다.

### 29. Tool Use를 통해 Agent는 Text Generation을 넘어 실제 External Action을 수행할 수 있다.

### 30. 강의에서 제시한 AI Agent의 여섯 특징은 Autonomy, Perception, Memory, Reasoning & Planning, Learning & Adaptation, Tool Use다.

### 31. 이 여섯 특징은 서로 독립적인 목록이 아니라 Goal을 향한 반복적인 Agent Loop를 구성한다.

### 32. Agent의 기본 Loop는 Observe → Think/Plan → Act → Evaluate → Adapt → Repeat로 이해할 수 있다.

### 33. Perception AI → Generative AI → Agentic AI → Physical AI는 강의에서 제시하는 중요한 AI 발전 축이다.

### 34. 이 네 기술은 서로를 대체하는 관계가 아니라 상위 단계가 아래 Capability를 사용하는 계층적 관계로 이해할 수 있다.

### 35. Generative AI는 Content를 생성하지만 기본적으로 User Request에 반응하는 수동적 구조를 가진다.

### 36. Agentic AI는 Goal을 바탕으로 필요한 Action을 스스로 선택한다.

### 37. Physical AI는 Agentic AI를 Robot·IoT 등 Physical System과 연결해 현실 세계에서 행동하게 한다.

### 38. AI Scientist 같은 사례는 Agent가 Research처럼 매우 긴 Workflow 전체를 처리하려는 방향을 보여준다.

### 39. 실제 Agent 성능은 Base Model 성능만으로 결정되지 않는다.

### 40. Memory, Tool, Routing, Workflow, Evaluation 등 LLM 주변의 Agent Harness도 중요하다.

### 41. Agent Framework마다 Memory, Feedback, Domain Specialization 등 강점이 다를 수 있다.

### 42. Agent Framework를 선택할 때는 Project에서 어떤 Agent Capability가 중요한지 먼저 판단해야 한다.

### 43. Agent Prototype을 만드는 것보다 어떤 문제를 어떤 구조로 해결할지 방향을 정하는 능력이 중요해지고 있다.

### 44. Coding Agent가 발전해도 Agent가 만든 Code의 품질과 Risk를 판단하려면 Coding 기본 지식은 여전히 필요하다.

### 45. Closed Agent System을 기업에서 사용할 때는 Data Flow와 Tool Call을 확인하기 어려운 Transparency 문제가 생길 수 있다.

### 46. Enterprise Agent에서는 Observability, Controllability, Privacy, Security 등이 중요하다.

### 47. Agent가 복잡해질수록 Token/Compute Cost와 Context Management 문제가 커진다.

### 48. Agent가 Personal/Enterprise Data를 사용하면 Privacy와 Security가 중요한 기술 문제가 된다.

### 49. Agent에게 높은 Autonomy를 줄수록 잘못된 실제 Action이 발생할 Risk도 커진다.

### 50. 따라서 Guardrail과 Permission 설계가 필요하다.

### 51. 의료처럼 High-stakes Domain에서는 Agent의 Recommendation 이후 Human Expert의 최종 판단을 유지하는 것이 중요하다는 것이 강사의 관점이다.

### 52. Agent의 목표는 LLM을 무조건 더 크게 만드는 것이 아니라 LLM의 Capability를 Memory·Tool·System과 결합해 실제 복잡한 Goal을 해결하는 것이다.

---

# 141. 이번 강의 전체 논리

```text
AI가 발전했다.
      ↓
먼저
세상을 인식
      ↓
Perception AI


인식만으로 부족하다.
      ↓
무언가를 생성
      ↓
Generative AI / LLM


그런데
LLM은 User가 질문해야
답하는 수동적 System이다.
      ↓
복잡한 현실 문제는
한 번의 답변으로 안 된다.
      ↓
Agentic AI


Goal을 준다.
      ↓
Agent가 스스로

상황을 인식하고
↓
과거를 기억하고
↓
Reasoning하고
↓
Plan을 세우고
↓
필요한 Tool을 사용하고
↓
실행하고
↓
결과를 평가하고
↓
다시 수정한다.


이것이
AI Agent


그런데
하나의 Agent / LLM도
모든 문제를 해결할 수 없다.
      ↓
Specialized Agent
↓
Multi-Agent


또한
LLM 자체가
모든 정보와 기능을
가지고 있을 수 없다.
      ↓
Memory
+
Tools


Agent가
Digital World를 넘어
Robot과 연결된다.
      ↓
Physical AI


결국 중요한 것은
      ↓
Model만 잘 만드는 것이 아니라

Model
+
System
+
Memory
+
Tools
+
Agent Architecture
+
Safety

를 함께 설계하는 것
```

---

# 142. 이번 강의 핵심 한 문장

> **AI Agent는 LLM과 같은 AI Model을 기반으로 User가 모든 Step을 직접 지시하지 않아도 Environment와 Context를 인식하고 과거 정보를 기억하며, 주어진 Goal을 달성하기 위해 복잡한 문제를 Subtask로 분해하고 계획을 수립한 뒤 필요한 Tool이나 다른 Agent를 활용해 행동하고, 그 결과와 Feedback을 평가해 계획을 수정하는 과정을 자율적으로 반복하는 Software System이며, 실제 Agent의 성능은 Base Model뿐 아니라 Memory·Tool·Workflow·Evaluation·Guardrail 등을 포함한 Agent Harness와 System Architecture에 의해 함께 결정된다.**

가장 짧게 기억하면 다음과 같다.

```text
LLM
= 질문하면 답한다.

AI Agent
= 목표를 주면
  스스로 어떻게 해결할지 판단하고
  행동한다.


Agent의 6가지 특징

1. Autonomy
   = 스스로 판단한다.

2. Perception
   = 현재 상황을 파악한다.

3. Memory
   = 과거를 기억한다.

4. Reasoning & Planning
   = 문제를 나누고 순서를 정한다.

5. Learning & Adaptation
   = 결과를 보고 수정한다.

6. Tool Use
   = 필요한 외부 기능을 사용한다.


Agent의 기본 Loop

보고
↓
생각하고
↓
계획하고
↓
행동하고
↓
평가하고
↓
다시 수정한다.
```