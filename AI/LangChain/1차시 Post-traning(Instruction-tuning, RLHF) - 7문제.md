[강의 링크](https://www.youtube.com/watch?v=wJeg18385MI)
## 시험 범위에 포함되는 내용
| 시험 토픽                        | 매칭 내용                                                                 |
| ---------------------------- | --------------------------------------------------------------------- |
| **지시 학습 데이터**                | 다양한 `Instruction–Input–Output` 데이터, Synthetic Instruction Data        |
| **단일 태스크 파인튜닝**              | 기존 Fine-tuning은 One/Few Task에 적응                                      |
| **파인튜닝과 Instruction-tuning** | 일반 Fine-tuning과 Many Tasks/Many Instructions 기반 Instruction Tuning 비교 |
| **RLHF 강화학습**                | Reward를 이용한 Policy LLM 강화학습                                           |
| **RLHF 학습 절차**               | SFT → Reward Model → RL                                               |
| **RLHF 학습 파이프라인**            | Instruction Data → SFT → Preference → Reward Model → RL 전체 구조         |
| **도메인 특화 AI**                | General Model 대신 Coding/Counseling/Translation Specialist를 만드는 방향     |
## Instruction Tuning, RLHF, DPO, RLVR

# 0. 강의 전체 개요

Language Model의 기본 Pre-training은 보통

```text
Context
↓
Next Token Prediction
```

을 수행한다.

이 과정을 대규모 Text에서 반복하면서 Model은

- Language
    
- Grammar
    
- Knowledge
    
- Pattern
    
- 일부 Reasoning 능력
    

을 학습한다.

하지만 여기서 중요한 문제가 생긴다.

```text
좋은 Language Model
≠
좋은 Assistant
```

이라는 것이다.

Pre-trained Model은

> **“다음에 어떤 Text가 올 확률이 높은가?”**

를 잘 학습했지,

> **“User가 이 말을 명령으로 했으니 그 의도에 맞게 도와줘야 한다.”**

를 직접 학습한 것은 아니다.

그래서 Pre-training 이후에 **Post-training**이 필요하다.

전체 흐름은 다음과 같다.

```text
Massive Text
↓
Pre-training
↓
Language Model
↓
언어와 지식은 풍부

하지만
User Instruction을
항상 올바르게 따르지는 않음
↓
Post-training


1단계
Instruction Tuning / SFT
↓
Instruction Following Capability
↓
User 의도를 이해


하지만
Open-ended Task에서는
정답 하나를 모방하는 SFT에 한계
↓
Preference Learning


2단계
Human Preference Data
↓
Reward Model
↓
어떤 Response가
더 좋은지 Score


3단계
RLHF
↓
PPO
↓
Reward ↑
+
기존 SFT Model에서
너무 멀어지지 않게 제한


하지만
Reward Model 구축이 어렵고
Training Cost가 큼
↓
DPO
↓
Preference Pair를
Policy에 직접 학습


그리고
수학·Code처럼
정답을 자동 검증할 수 있다면
↓
Verifiable Reward
↓
RLVR
↓
Reasoning Model
```

---

# 1. Pre-training 다시 보기

강의에서는 먼저 기존 Language Model Training을 복습한다.

대표적인 Decoder-only Language Model은

```text
w₁, w₂, ..., wₜ
↓
다음 Token wₜ₊₁의
Probability Distribution 예측
```

을 반복한다.

즉 **Next Token Prediction**이다.

---

# 2. Model Architecture에 따라 Pre-training Objective는 다를 수 있다

강의에서는 이전에 배운 Model들도 함께 언급한다.

### Decoder-only Model

```text
Context
↓
Next Token Prediction
```

### BERT 계열

중간 Token을 가리고 복원하는 Masked Language Modeling.

### T5 계열

일부 Span을 제거하고 다시 복원하는 Denoising/Span Corruption 형태.

그러나 현재 상용 Generative LLM의 중심은 Decoder 기반 Next-token Prediction으로 설명한다.

---

# 3. Pre-training으로 무엇을 배우는가?

방대한 Text를 Training하면

```text
Language Pattern

General Knowledge

Translation Pattern

Summarization Pattern

Question Answering Pattern
```

등을 Model 안에 학습할 수 있다.

Model Scale이 커지면서 Few-shot Example만 주어도 새로운 Task Pattern을 따라가는 능력도 나타났다.

---

# 4. Few-shot Learning 예

예를 들어

```text
Movie Review → Positive

Movie Review → Negative
```

Example을 몇 개 보여주고 새로운 Review를 주면

```text
Positive / Negative
```

를 예측할 수 있다.

즉 Model이 Context 안에서 Pattern을 발견한다.

---

# 5. 그런데 Instruction은 다르다

예를 들어

```text
"달에 대해서
6살 어린아이에게 설명해 줘."
```

라고 Pre-trained GPT 계열 Model에게 요청했다고 하자.

현재 우리는 이것을 명백한 **명령**으로 인식한다.

하지만 Pre-trained Language Model 입장에서는

```text
앞 Text 다음에
무슨 Text가 올까?
```

라는 문제일 뿐이다.

---

# 6. Pre-trained Model이 명령을 반복할 수도 있다

강의에서는 초기 GPT 계열에서 이러한 Instruction을 주면

```text
비슷한 질문
비슷한 Prompt
```

를 계속 생성하는 Pattern이 나타날 수 있었다고 설명한다.

왜냐하면 Model은

> **“이 Sentence는 User가 나에게 내린 명령이다.”**

라고 반드시 이해한 것이 아니기 때문이다.

---

# 7. Pre-training 관점에서는 틀린 행동이 아닐 수도 있다

Next-token Prediction Objective에서 보면

```text
Instruction과 비슷한 Sentence를
계속 생성
```

하는 것도 Training Objective를 위반한 것은 아니다.

즉 문제는

```text
Language Modeling Objective
```

와

```text
Helpful Assistant Objective
```

가 완전히 같지 않다는 것이다.

강의에서는 바로 이 Gap을 Post-training으로 해결한다고 설명한다.

---

# 8. Post-training

Pre-training 이후 Model을

- Helpful
    
- Safe
    
- User-aligned
    
- Instruction-following
    

하게 만드는 추가 Training을 넓게 **Post-training**이라고 본다.

대표 방법:

```text
Instruction Tuning

RLHF

DPO

RLVR
```

등이다.

---

# 9. 가장 기본적인 Post-training — Instruction Tuning

Instruction Tuning은 기본적으로 **Fine-tuning**이다.

기존 Fine-tuning 구조:

```text
Large Pre-training Dataset
↓
Pre-trained Model
↓
Small Downstream Dataset
↓
Fine-tuning
↓
Specific Task Model
```

이다.

---

# 10. 일반 Fine-tuning의 목적

예를 들어

```text
Sentiment Classification

Translation

Question Answering
```

같은 특정 Downstream Task Data를 추가 Training한다.

Pre-training에서 얻은 General Language Knowledge를 특정 Task로 Transfer한다.

이를 **Transfer Learning**이라고 볼 수 있다.

---

# 11. Instruction Tuning은 무엇이 다른가?

기본 Training Mechanism 자체는 Fine-tuning과 크게 다르지 않다.

가장 큰 차이는 Dataset의 범위다.

일반 Fine-tuning:

```text
One / Few Tasks
```

Instruction Tuning:

```text
Many Tasks
+
Many Instructions
```

이다.

---

# 12. Instruction Tuning Dataset

예:

```text
Instruction:
영어를 프랑스어로 번역하라.

Input:
...

Output:
...


Instruction:
다음 문장을 요약하라.

Input:
...

Output:
...


Instruction:
다음 질문에 답하라.

Input:
...

Output:
...
```

처럼 다양한 Task를 하나의 Text Generation 형태로 통일한다.

---

# 13. 다양한 Task를 동시에 학습

강의에서는 예시로 약

```text
16,000 Tasks
```

규모의 다양한 Instruction을 Training하는 사례를 설명한다.

중요한 것은 정확한 Task 숫자를 외우는 것보다

```text
Many Heterogeneous Tasks
↓
Instruction이라는 공통 Interface
```

로 통합한다는 것이다.

---

# 14. Instruction Following Capability

Instruction Tuning의 더 중요한 목표는 새로운 Knowledge를 많이 추가하는 것만이 아니다.

강의에서는

> **Model은 이미 Translation, QA, Classification 등을 수행할 기본 Knowledge를 가지고 있다고 가정하고, User의 명령을 세밀하게 이해하는 능력을 길러준다.**

고 설명한다.

이를

**Instruction Following Capability**

라고 한다.

---

# 15. 천재 조수 비유

강의에서는 이를 직관적으로 설명한다.

어떤 사람이 매우 똑똑하더라도

```text
내 말을 이해하지 못하거나
내 명령을 무시한다.
```

면 좋은 Assistant가 아니다.

즉

```text
Knowledge
+
Instruction Understanding
```

이 모두 필요하다.

---

# 16. 같은 Task라도 Instruction이 다를 수 있다

예를 들어 Question Answering에서도

```text
답만 써라.
```

와

```text
Step-by-step Reasoning 후 답하라.
```

는 다른 Instruction이다.

또

```text
근거를 먼저 제시하라.

답을 먼저 쓰고 설명하라.

한 문장으로 답하라.
```

도 다르다.

Instruction Tuning은 이런 미세한 요구 차이에 민감하게 대응하는 능력을 학습한다.

---

# 17. Unseen Instruction으로 Generalization

Instruction Tuning의 궁극적인 목표는

```text
Training에서 본
16,000개 Instruction
```

만 잘 수행하는 것이 아니다.

그 다양한 Pattern을 이용하여

```text
Training에서 보지 못한
새로운 Instruction
```

에도 잘 대응하는 것이다.

---

# 18. Multitask Learning과 차이

강의에서는 기존 T5 Multitask Training과 비교한다.

기존 Multitask Training:

```text
Task A
Task B
Task C
...
```

를 모두 잘하는 것이 직접 목적일 수 있다.

Instruction Tuning:

```text
많은 Task
↓
범용적인
Instruction Understanding 능력
↓
Unseen Task까지 Generalization
```

이 더 중요한 목표다.

---

# 19. FLAN 계열

강의에서는 Instruction Tuning Dataset과 Model의 대표 사례로

```text
FLAN Dataset

FLAN-T5
```

등을 언급한다.

핵심은 다양한 Task를 자연어 Instruction 형태로 변환해 Model을 추가 Training한다는 것이다.

---

# 20. Rationale

강의에서 `Rationale`은 Answer를 제외한 중간 Reasoning Process를 의미한다.

예:

```text
Question
↓
Rationale / Reasoning
↓
Final Answer
```

Chain-of-Thought에서 중간 생성 과정이 Rationale에 해당한다.

---

# 21. Instruction Tuning의 효과 평가

강의에서는 여러 분야의 Knowledge/Problem Solving을 측정하는 Benchmark로 **MMLU**를 소개한다.

다양한 Subject의 객관식 문제를 포함한다.

---

# 22. MMLU

강의에서는 약 50여 개 이상의 분야를 포함한

- High School
    
- College
    
- Professional Knowledge
    

수준의 문제로 설명한다.

4지선다라면 Random Guess는 약

```text
25%
```

다.

---

# 23. Model 발전과 Benchmark

강의에서는 GPT-2 → GPT-3 → Chinchilla/PaLM → Instruction-tuned Models → GPT-4/Gemini 계열로 이어지면서 성능이 크게 올라간 흐름을 설명한다.

세부 숫자보다 중요한 것은

```text
Scale

Better Data

Instruction Tuning

Test-time Computation
```

등이 모두 성능 향상에 기여했다는 흐름이다.

---

# 24. Test-time Computation도 성능에 영향을 준다

강의에서는

- Few-shot Examples
    
- Chain-of-Thought
    
- Self-consistency
    

등을 Test Time에 활용하면 Model Parameter 자체를 바꾸지 않고도 추가 Compute를 사용하여 성능을 높일 수 있다고 설명한다.

---

# 25. Sovereign AI와 Benchmark

영어 MMLU만 있는 것이 아니라 한국어에 맞는 Benchmark/Data를 구축하는 것도 중요하다.

강의에서는 Sovereign AI를 단순히

```text
한국어 Model
```

만 만드는 문제로 보지 않는다.

```text
한국어 Training Data

한국어 Benchmark

한국 환경에 맞는 Evaluation
```

도 포함할 수 있다고 설명한다.

---

# 26. Instruction Tuning Data는 사람이 전부 만들어야 하는가?

초기에는 사람이 Instruction–Response Pair를 많이 작성해야 한다고 생각하기 쉽다.

하지만 강의에서는 첫 번째 고정관념을 깬다.

> **사람이 모든 Data를 직접 만들 필요는 없다.**

---

# 27. Synthetic Instruction Data

강력한 기존 LLM에게

```text
새 Instruction을 만들어라.

그 Instruction에 대한
좋은 Answer도 만들어라.
```

라고 시킬 수 있다.

즉

```text
Strong Teacher LLM
↓
Synthetic Instruction Data
↓
Smaller Model Fine-tuning
```

이 가능하다.

---

# 28. Alpaca 사례

강의에서는 Stanford Alpaca를 예로 든다.

대략적인 강의 흐름:

```text
약 175개 Seed Instructions
↓
Strong LLM 활용
↓
약 50,000개의
Synthetic Instruction Data
↓
LLaMA 7B Fine-tuning
↓
Alpaca 7B
```

이다.

---

# 29. Alpaca의 의미

중요한 의미는

> **대규모 Foundation Model을 처음부터 Training하지 않더라도 공개 Base Model + Synthetic Data를 이용해 유용한 Instruction-following Model을 만들 수 있다.**

는 점이다.

즉 Big Tech뿐 아니라 연구실이나 작은 조직도 Post-training 연구를 할 수 있는 가능성을 보여준다.

---

# 30. General Model vs Specialized Model

강의에서는 작은 Model을 Fine-tuning할 때

```text
모든 것을 잘하는 General Model
```

보다는

```text
Coding Specialist

Counseling Specialist

Translation Specialist
```

처럼 특정 Domain에 강한 Expert Model을 만드는 것도 현실적인 방향이라고 설명한다.

---

# 31. PEFT

과거에는 Model 전체 Parameter를 Fine-tuning하는 **Full Fine-tuning**이 일반적이었다.

하지만 LLM이 커지면서

```text
Parameter 전체 Update
```

는 매우 비싸다.

그래서

**Parameter-Efficient Fine-Tuning(PEFT)**

을 사용한다.

---

# 32. PEFT 예

강의에서는

```text
LoRA

Adapter
```

등을 언급한다.

전체 7B Parameter를 모두 Update하지 않고 일부 작은 Parameter만 Training한다.

따라서 제한된 GPU Resource에서도 Fine-tuning 가능성이 커진다.

---

# 33. 두 번째 고정관념 — Data가 많아야 하는가?

Pre-training에서는

```text
Data Scale ↑
```

가 매우 중요했다.

그래서 Post-training에도 수십만·수백만 Data가 반드시 필요하다고 생각하기 쉽다.

하지만 강의에서는 **LIMA**를 소개한다.

---

# 34. LIMA — Less Is More for Alignment

강의에서 설명하는 핵심은

> **아주 많은 저품질 Data보다 약 1,000개의 고품질 Alignment Data만으로도 상당히 좋은 결과를 낼 수 있었다.**

는 것이다.

---

# 35. “적다”의 의미

여기서

```text
Data 적음
```

은

```text
아무 Data나 조금 사용
```

이라는 뜻이 아니다.

핵심은

```text
High-quality

Diverse

Carefully Selected
```

Data다.

---

# 36. Alignment 관점

왜 적은 Data도 가능할 수 있을까?

강의의 직관은 다음과 같다.

```text
Pre-trained LLM
↓
이미 Knowledge는 풍부하다.
```

Post-training의 역할은

```text
새 지식을 처음부터 대량 학습
```

하기보다

```text
이미 가지고 있는 능력을
User가 원하는 방식으로
꺼내도록 방향을 맞춤
```

에 가깝다는 것이다.

---

# 37. 교육 비유

사람이 수년간 공부한 뒤 회사에 입사했다고 하자.

회사에서 며칠간 Orientation을 받는다고

```text
모든 지식을 새롭게 학습
```

하는 것은 아니다.

이미 가진 Knowledge를

```text
우리 회사가 원하는
업무 방식
```

에 맞추는 것이다.

강의에서는 Alignment를 이와 비슷하게 설명한다.

---

# 38. Instruction Tuning의 장점

```text
구조가 직관적

Training 방식이 단순

Instruction–Response Dataset만 있으면
일반 Supervised Fine-tuning으로 가능
```

하다.

---

# 39. 그런데 Instruction Tuning에는 한계가 있다

핵심 이유:

> **결국 Supervised Learning이다.**

Supervised Learning은 주어진 정답을 최대한 모방한다.

---

# 40. Open-ended Task에서는 정답이 하나가 아니다

예:

```text
"How are you?"
```

에 대해

```text
I'm fine.

I'm good.

Not bad.

I'm doing well.
```

모두 가능하다.

그러나 SFT Dataset에 하나의 Response만 있으면 Model은 그것을 정답으로 학습한다.

---

# 41. Creative Task는 더 어렵다

예:

```text
Essay

Advice

Conversation

Creative Writing

Open-ended QA
```

는 하나의 절대적인 Correct Answer가 존재하지 않는다.

따라서 하나의 Reference Answer를 정확히 모방하도록 하는 Training은 구조적인 한계를 가진다.

---

# 42. Token-level Cross Entropy의 한계

Language Model SFT에서는 Ground-truth Token의 Probability를 높이도록 학습한다.

예를 들어 정답이

```text
fantasy TV show
```

라면 각 Step에서

```text
P(fantasy | context)

P(TV | context)

P(show | context)
```

를 높인다.

---

# 43. 모든 Wrong Token을 의미적으로 구분하지 않는다

예:

```text
fantasy
```

대신

```text
adventure
```

를 생성한 것과

```text
musical
```

을 생성한 것은 의미적으로 잘못된 정도가 다를 수 있다.

하지만 Token-level Supervised Loss의 핵심은

```text
Correct Token의 Probability
```

다.

잘못된 후보 간의

```text
어느 답이 더 낫고
어느 답이 훨씬 나쁜가
```

를 직접적으로 표현하는 Objective는 아니다.

---

# 44. Sequence 전체 품질과도 Gap이 있다

전체 Answer를 보면 충분히 좋은 답인데 Reference Sentence와 Token Alignment가 다르면 Loss가 클 수 있다.

즉

```text
Human Quality Judgment
```

과

```text
Token-level Likelihood
```

가 완전히 일치하지 않는다.

---

# 45. 그래서 Preference Signal을 사용한다

정답 하나를 지정하는 대신

```text
Response A
vs
Response B
```

를 보여주고

```text
A가 더 좋다.
```

는 정보를 제공한다.

즉 절대적인 정답 대신 **Relative Preference**를 학습한다.

---

# 46. Preference Learning

```text
Instruction
↓
Response A
Response B
↓
Human:
A > B
```

형태의 Data다.

Model은

```text
어떤 Response가
사람에게 더 선호되는가?
```

를 배우게 된다.

---

# 47. Reinforcement Learning으로 연결

Model이 여러 Response를 직접 생성해 보고

```text
Response
↓
Reward
```

를 받은 다음

```text
Reward가 높은 Response를
더 많이 생성
```

하도록 Policy를 Update한다.

강의에서는 이 구조가 경험을 통해 배우는 Reinforcement Learning과 연결된다고 설명한다.

---

# 48. Reward

어떤 Response가 얼마나 좋은지를 숫자로 표현한다.

예를 들어 요약 Task에서

```text
Input Article
```

과 Context에 맞는 Summary `x₁`은 높은 Reward,

관련 없는 Weather 이야기 `x₂`는 낮은 Reward를 받을 수 있다.

---

# 49. RL Objective

Policy Model을 `π_θ`라고 할 때 개념적으로

```text
x
↓
π_θ
↓
y Sampling
↓
Reward r(x,y)
```

한다.

목표는

`E[r(x,y)]`

를 최대화하는 Parameter `θ`를 찾는 것이다.

즉

> **Model이 생성하는 Response들의 기대 Reward를 높인다.**

---

# 50. RLHF

**RLHF = Reinforcement Learning from Human Feedback**

사람의 Preference를 Reward Signal로 바꾸어 Language Model을 강화학습하는 Framework다.

강의에서는 크게 3단계로 나눈다.

```text
Step 1
SFT

Step 2
Reward Model Training

Step 3
RL with Reward Model
```

---

# 51. RLHF Step 1 — SFT

먼저 사람에게 좋은 Instruction–Response Pair를 작성하도록 한다.

예:

```text
Instruction:
달에 대해
6살에게 설명해라.

Human Response:
...
```

이런 고품질 Data로 Instruction Tuning/SFT를 수행한다.

---

# 52. SFT Model의 역할

이 단계에서 이미 Model은

```text
User Instruction 이해
+
Reasonable Response 생성
```

능력을 얻는다.

RLHF는 아무것도 못하는 Model에서 시작하는 것이 아니다.

이미 꽤 좋은 SFT Model에서 시작한다.

---

# 53. RLHF Step 2 — 여러 Response 생성

SFT Model에 하나의 Prompt를 주고 여러 Candidate Response를 생성한다.

```text
Prompt
↓
SFT Model
├─ Response A
├─ Response B
├─ Response C
└─ Response D
```

---

# 54. Human Ranking

사람이 Candidate를 비교한다.

예:

```text
D > C > A ≈ B
```

처럼 Preference Order를 만든다.

---

# 55. 왜 사람에게 절대 점수를 요청하지 않는가?

예를 들어

```text
A = 4.7점

B = 3.1점
```

처럼 평가하도록 할 수도 있다.

하지만 사람마다 Score Scale이 다르다.

어떤 사람은 최고의 답에도 3점만 줄 수 있고 다른 사람은 쉽게 5점을 줄 수 있다.

---

# 56. 상대 비교가 더 안정적이다

사람에게

```text
A와 B 중
어느 것이 더 좋은가?
```

를 묻는 것이 절대 숫자 Score를 요구하는 것보다 상대적으로 안정적일 수 있다.

그래서 Preference Pair를 사용한다.

---

# 57. Ranking에서 Pair를 만든다

예:

```text
y₂ > y₁ > y₃
```

같은 Ranking이 있다면

```text
(y₂, y₁)

(y₂, y₃)

(y₁, y₃)
```

처럼 여러 Pair를 만들 수 있다.

---

# 58. Preferred / Rejected

선호되는 Response를 흔히

```text
Chosen

Preferred

Winning
```

이라고 부른다.

덜 선호되는 Response는

```text
Rejected

Dispreferred

Losing
```

등으로 부른다.

강의에서는 여러 이름이 존재함을 설명한다.

---

# 59. Reward Model

이 Preference Pair를 이용해

```text
Prompt + Response
↓
Reward Model
↓
Scalar Reward
```

를 출력하도록 학습한다.

예:

```text
Reward(D) > Reward(C)
> Reward(A) ≈ Reward(B)
```

가 되길 원한다.

---

# 60. Reward Model도 보통 Language Model 기반

일반적으로 잘 Pre-trained된 Language Model을 Backbone으로 사용하고 최종적으로 Response Quality를 나타내는 Scalar를 출력하도록 학습한다.

---

# 61. Reward Model의 역할

Human이 RL Training 동안 매번

```text
이 Response는 4점.

이 Response는 2점.
```

평가하는 것은 현실적으로 불가능하다.

그래서

```text
Human Preference
↓
Reward Model 학습
↓
Reward Model이
Human Judge를 근사
```

하도록 한다.

---

# 62. Bradley–Terry Model

강의에서는 Pairwise Preference Training을 **Bradley–Terry Model**과 연결한다.

핵심 목적은

```text
Reward(chosen)
>
Reward(rejected)
```

가 되도록 만드는 것이다.

---

# 63. 절대 Reward 값 자체가 중요한 것은 아니다

예:

```text
Chosen = 5.3
Rejected = 2.1
```

이어야 한다고 미리 정하는 것이 아니다.

중요한 것은

```text
Chosen Score
>
Rejected Score
```

라는 **Relative Ordering**이다.

---

# 64. Reward Difference

Preference Pair에 대해

```text
r(x, y_w)
-
r(x, y_l)
```

의 차이가 커지는 방향으로 Reward Model을 학습한다.

여기서

```text
y_w = Winning / Chosen

y_l = Losing / Rejected
```

이다.

---

# 65. RLHF Step 3 — Policy Optimization

이제 Reward Model을 고정한다.

그리고 다시 SFT Model을 가져와 실제 Response를 생성한다.

```text
Prompt
↓
Policy LLM
↓
Response
↓
Reward Model
↓
Reward
```

한다.

---

# 66. Reward를 이용해 Policy를 Update

```text
High Reward Response
→ 생성 Probability ↑

Low Reward Response
→ 생성 Probability ↓
```

방향으로 Model을 학습한다.

이 단계가 실제 Reinforcement Learning이다.

---

# 67. RLHF 전체 구조

```text
[1]
Instruction–Response Data
↓
SFT
↓
Instruction-following Model


[2]
SFT Model
↓
Multiple Responses
↓
Human Preference
↓
Preference Pairs
↓
Reward Model


[3]
SFT Model
↓
Response Sampling
↓
Reward Model
↓
Reward
↓
RL
↓
Aligned Policy Model
```

---

# 68. 왜 SFT만으로 끝내지 않는가?

강의에서는 실제 Preference Evaluation에서

```text
Pre-trained Model

SFT Model

RLHF Model
```

을 비교한 사례를 설명한다.

SFT는 Human Demonstration을 모방하지만 Human Reference 자체를 크게 넘어서는 데 한계가 있을 수 있다.

반면 RL은

```text
Model이 직접 여러 Response를 생성
↓
Reward Feedback
↓
자신의 Experience를 통해 개선
```

할 수 있다.

강의는 이를 “단순 모방을 넘어 경험으로 성장한다”는 직관으로 설명한다.

---

# 69. 강화학습의 세 흐름

강의에서는 RL 역사를 크게 세 흐름으로 구분해서 설명한다.

### 1. Deep Learning 이전 RL

다양한 Optimization/Decision 분야에서 사용.

### 2. Deep Reinforcement Learning

Deep Neural Network를 Value Function/Policy 등의 Approximation에 사용.

대표적인 사례로 AlphaGo와 Atari 등을 언급한다.

### 3. LLM + RL

이미 강력한 Language Model에 RL을 적용하여

- Alignment
    
- Safety
    
- Preference
    
- Reasoning
    

등을 개선한다.

---

# 70. LLM RL은 과거 RL과 출발점이 다르다

과거 RL:

```text
Agent가 Task를 거의 못함
↓
Interaction으로
Task 자체를 배움
```

LLM RL:

```text
이미 매우 강한 LLM
↓
조금 더 선호되고
안전하고
효율적인 Behavior로 Alignment
```

한다.

즉 강력한 Base Model 위에서 시작한다.

---

# 71. Safety와 RLHF

Instruction Tuning만 아주 잘하면 User의 위험한 Instruction도 잘 따를 수 있다.

따라서 상용 Assistant에서는

```text
Helpful
```

뿐 아니라

```text
Harmless / Safe
```

도 필요하다.

강의에서는 RLHF가 이러한 Safety Alignment에도 사용된다고 설명한다.

---

# 72. Human-in-the-loop

Machine Learning에는 이전부터 사람이 Model Development Cycle에 계속 참여하는 **Human-in-the-loop** 개념이 존재했다.

예:

```text
Model Training
↓
Uncertain Data Selection
↓
Human Labeling
↓
Retraining
↓
반복
```

이다.

RLHF 역시 Human Feedback을 Training Cycle에 넣는 연장선에서 이해할 수 있다.

---

# 73. 좋은 Reward Model을 만드는 것이 어렵다

RLHF의 핵심 Bottleneck 중 하나다.

Reward Model이 사람의 Preference를 잘 반영하지 못하면

```text
Policy가 Reward를 높이는 방향
```

과

```text
실제 사람이 좋아하는 방향
```

이 달라질 수 있다.

---

# 74. Reward Model Scaling

강의에서는 Language Model Scale이 커지면서 Reward Model의 Preference Classification 성능도 좋아지는 경향을 설명한다.

즉 강력한 LLM의 발전이

```text
좋은 Policy
```

뿐 아니라

```text
좋은 Reward Model
```

을 만드는 데도 기여했다는 것이다.

---

# 75. Reward Hacking

Agent는 우리가 의도한 Goal이 아니라

> **정의된 Reward Function을 최대화한다.**

이 둘이 다르면 문제가 생긴다.

이를 **Reward Hacking**이라고 한다.

---

# 76. 게임 예

Goal:

```text
Stage를 진행해
게임을 잘 플레이
```

인데,

Reward:

```text
특정 Item을 먹으면 +
```

라고 정의했다고 하자.

그 Item이 계속 Respawn한다면 Agent가

```text
게임 진행 X

같은 자리에서
Item 반복 획득
```

할 수 있다.

Reward는 높지만 우리가 원하는 Behavior는 아니다.

---

# 77. LLM의 Reward Hacking

Language Model에서도 Reward Model이 특정 스타일을 과도하게 좋아하면 Model이

```text
그럴듯한 말

과도한 확신

아부

장황한 표현
```

등으로 Reward Model을 공략할 수 있다.

---

# 78. 모른다고 하는 것보다 Hallucination이 유리할 수도 있다

예를 들어 Reward Model이

```text
Answer 없음
→ Reward 0
```

을 주고

```text
그럴듯한 Answer
→ Reward > 0
```

를 줄 가능성이 조금이라도 있다면 Policy는

```text
모르면 모른다고 말하기
```

보다

```text
아무 답이나 생성
```

하는 방향을 배울 수 있다.

강의에서는 이를 Reward Design의 어려움과 연결한다.

---

# 79. Reward Model Overoptimization

RL Training이 진행될수록

```text
Reward Model Score ↑
```

하지만 어느 시점부터

```text
Real Human Preference ↓
```

할 수 있다.

Model이 실제 사람을 만족시키기보다 Reward Model의 약점을 이용하기 시작하는 것이다.

이를 강의에서는

**Reward Model Overoptimization**

이라고 설명한다.

---

# 80. Sycophancy 예

초기에는

```text
제가 잘못했습니다.
당신이 맞습니다.
```

가 예의바른 Response일 수 있다.

하지만 최적화가 지나치면

```text
User가 사실적으로 틀렸는데도
무조건 동의
```

하는 방향으로 갈 수 있다.

Reward Model은 좋아할 수 있지만 실제 User Utility는 낮아진다.

---

# 81. Supervised Overfitting과 비교

Supervised Learning에서는 Training Data에 지나치게 맞춰지는 현상을 **Overfitting**이라고 한다.

RLHF에서는 Reward Model을 지나치게 최적화하는 문제를

```text
Reward Overoptimization

Reward Hacking
```

관점으로 설명한다.

---

# 82. 그래서 Policy가 너무 멀리 변하면 안 된다

RL Policy가 Reward만 쫓다 보면 기존의 좋은 Language 능력을 잃을 수 있다.

따라서

```text
Reward ↑
```

뿐 아니라

```text
Original SFT Policy와
너무 달라지지 않음
```

도 필요하다.

---

# 83. PPO

초기 RLHF에서 대표적으로 사용된 Algorithm이

**PPO = Proximal Policy Optimization**

이다.

`Proximal`은

```text
가까운
근접한
```

이라는 의미다.

---

# 84. PPO의 핵심 직관

Policy를 Update할 때

```text
Reward를 높이되
기존 Policy에서
너무 멀리 가지 마라.
```

는 것이다.

---

# 85. Reference Model

RL Training 이전의 SFT Model을 Reference Model로 유지한다.

```text
Reference SFT Model
→ Frozen

RL Policy Model
→ Trainable
```

이다.

---

# 86. KL Divergence

두 Probability Distribution 사이의 차이를 측정하는 방법으로 **KL Divergence**를 사용한다.

개념적으로

```text
Current RL Policy
vs
Reference SFT Policy
```

의 차이를 측정한다.

---

# 87. PPO/RLHF Objective의 직관

```text
Reward가 높으면 좋음

하지만

KL(Current || Reference)가
너무 크면 Penalty
```

를 준다.

즉

```text
Reward Maximize
-
Policy Drift Penalty
```

형태로 이해한다.

---

# 88. 왜 KL Penalty가 필요한가?

SFT Model은 이미

- Language
    
- Instruction Following
    
- Quality
    

면에서 꽤 좋은 Model이다.

RL이 Reward를 쫓으면서 이 Model에서 완전히 다른 Distribution으로 이동하면 Behavior가 붕괴할 수 있다.

따라서

> **좋은 출발점 근처에서 개선하라.**

는 제한을 둔다.

강의에서도 PPO의 `Proximal`을 SFT Reference와 지나치게 달라지지 않도록 하는 구조와 연결한다.

---

# 89. PPO Training이 무거운 이유

강의의 단순화된 설명에서는 Training 시 대략 다음 Model들을 동시에 다뤄야 한다.

```text
Reward Model
→ Frozen

Reference SFT Model
→ Frozen

RL Policy Model
→ Trainable
```

모두 매우 큰 LLM일 수 있다.

---

# 90. Memory Cost

예를 들어 거대한 Model 하나만 GPU에 올리기도 어려운데 여러 Model을 동시에 유지해야 한다.

따라서 PPO 기반 RLHF는

```text
GPU Memory

Compute

Engineering Complexity
```

가 크다.

강의에서는 이러한 Resource Cost가 DPO를 설명하는 중요한 배경이라고 한다.

---

# 91. DPO

**DPO = Direct Preference Optimization**

핵심 Idea:

> **별도의 Reward Model과 Online RL 과정 없이 Preference Pair를 이용해 Policy Model을 직접 Training하자.**

이다.

---

# 92. RLHF/PPO의 구조

```text
Preference Data
↓
Reward Model Training
↓
Reward Model
↓
Policy가 Response Sampling
↓
Reward 계산
↓
PPO
↓
Policy Update
```

---

# 93. DPO의 구조

```text
Preference Data
↓
Chosen / Rejected Pair
↓
DPO Loss
↓
Policy Model 직접 Update
```

한다.

즉 중간 Reward Model 단계를 명시적으로 제거한다.

---

# 94. Preference Data를 직접 사용

예:

```text
Prompt x

Chosen y_w

Rejected y_l
```

를 Model에 제공한다.

그리고 Model이

```text
P(y_w | x)
>
P(y_l | x)
```

가 되도록 학습한다.

---

# 95. 생성하지 않고 Probability를 평가할 수 있다

Language Model은 반드시 Text를 Autoregressive하게 생성해야만 사용할 수 있는 것이 아니다.

이미 주어진 Sentence를 넣으면

```text
이 Sentence가
현재 Model에서 얼마나 높은
Likelihood를 갖는가?
```

를 계산할 수 있다.

DPO에서는 이러한 Probability를 Preference Pair Training에 활용한다.

---

# 96. DPO의 장점

```text
Reward Model 별도 Training 부담 ↓

Online Sampling/RL Complexity ↓

Memory/Compute 부담 ↓

일반 Fine-tuning과 유사한
Offline Training 가능
```

하다.

---

# 97. 왜 Direct가 가능한가?

강의에서는 DPO가 단순한 Engineering Trick이라기보다

> 기존 Reward Model + KL-regularized RL Objective를 수학적으로 변형하면 Preference Data에서 직접 Policy를 학습할 수 있는 Objective가 유도된다는 발견

이라고 설명한다.

세부 수식 유도는 강의에서 생략한다.

---

# 98. Online vs Offline 관점

강의에서는 PPO와 DPO를 경험 관점에서도 비교한다.

### PPO

```text
현재 Policy가 직접
새 Response 생성
↓
현재 상태에서
Reward를 받음
↓
Update
```

즉 현재 Policy의 **Experience**를 이용한다.

### DPO

```text
미리 만들어둔
Preference Dataset
↓
Policy Training
```

이다.

즉 Offline Data 중심이다.

---

# 99. On-policy / Off-policy 직관

강의의 비유를 정리하면

### On-policy에 가까운 Training

```text
내가 직접 행동해 보고
그 행동을 평가받음
```

### Offline / Off-policy Data 활용

```text
다른 사람이 만든
좋은/나쁜 행동 Example을 보고
배움
```

이다.

---

# 100. DPO의 잠재적 한계

Preference Dataset이 현재 Policy가 실제로 생성하는 Distribution과 너무 다르면 Training Signal이 잘 맞지 않을 수 있다.

즉

```text
Dataset Distribution
vs
Current Policy Distribution
```

Gap이 생길 수 있다.

강의에서는 Mentor의 조언이 현재 Learner의 상태와 너무 다르면 잘 먹히지 않을 수 있다는 비유를 사용한다.

---

# 101. PPO와 DPO 중 하나가 무조건 우월한가?

강의에서는 그렇지 않다고 설명한다.

### PPO

장점:

```text
Current Policy가
직접 경험하며 학습
```

단점:

```text
Training 복잡
Resource Cost 높음
Reward Model 필요
```

### DPO

장점:

```text
간단
Offline Preference Data로 학습
효율적
```

단점:

```text
Data Quality와
Policy–Data Distribution Gap에
영향 받을 수 있음
```

상황에 따라 선택한다.

---

# 102. RLHF 이후 새로운 흐름 — RLVR

강의 마지막에서는 **RLVR**을 소개한다.

**RLVR = Reinforcement Learning with Verifiable Rewards**

핵심은

> **Human Preference Reward Model 없이 정답을 자동으로 검증할 수 있는 Task에서는 검증 가능한 Reward를 직접 사용하자.**

이다.

---

# 103. Verifiable Reward

예를 들어 Math Problem이 있다.

```text
Model Answer
↓
Correct?
```

를 자동으로 확인할 수 있다.

맞으면

```text
Reward = 1
```

틀리면

```text
Reward = 0
```

처럼 할 수 있다.

---

# 104. Code도 검증 가능하다

Code Generation이라면

```text
Generated Code
↓
Unit Test
↓
Pass / Fail
```

로 평가할 수 있다.

즉 Human Preference Model을 거치지 않고 Environment가 Reward를 제공할 수 있다.

---

# 105. 왜 이전에는 단순해 보이는 Reward를 경계했는가?

Final Answer만 맞았는지 보는 Reward는

```text
중간 Reasoning Quality
```

를 전혀 평가하지 않는다.

따라서 Sparse하고 불완전한 Reward라고 생각하기 쉽다.

---

# 106. 그런데 대규모 LLM에서는 잘 작동할 수 있다

강의에서는 이미 강한 Base LLM에서

```text
Correct / Incorrect
```

같이 단순하지만 정확히 검증 가능한 Reward를 이용한 RL이 강력한 Reasoning 성능을 보여준 사례들이 등장하면서 RLVR이 크게 주목받았다고 설명한다.

---

# 107. DeepSeek 계열과 RLVR

강의에서는 DeepSeek 계열 Reasoning Model의 발전을 RLVR 흐름의 대표적인 산업적 사례 중 하나로 설명한다.

세부 Hardware/Implementation 혁신도 함께 있었지만 이 강의에서는

```text
Verifiable Reward를 이용한 RL
```

이라는 Training Idea에 집중한다.

---

# 108. RLHF와 RLVR 차이

### RLHF

Reward Source:

```text
Human Preference
↓
Reward Model
```

### RLVR

Reward Source:

```text
Automatic Verification
```

이다.

---

# 109. 어떤 Task가 RLVR에 적합한가?

대표적으로

```text
Math

Code

Logic Problem

Formal Reasoning

정답 검증이 가능한 일부 Task
```

다.

반면

```text
좋은 Essay인가?

친절한 답변인가?

창의적인 Story인가?
```

는 단순 자동 검증이 어렵다.

이러한 영역에서는 Preference 기반 Alignment가 더 필요할 수 있다.

---

# 110. Post-training을 크게 두 축으로 볼 수 있다

## ① Imitation

```text
좋은 Example을 보여준다.
↓
그 Example을 따라한다.
```

대표:

```text
Instruction Tuning / SFT
```

## ② Optimization by Feedback

```text
여러 행동을 해본다.
↓
좋고 나쁨의 Feedback을 받는다.
↓
더 좋은 행동으로 이동한다.
```

대표:

```text
RLHF / RLVR
```

이다.

---

# 111. Instruction Tuning과 RLHF의 본질적 차이

### Instruction Tuning

```text
Input
↓
Target Response
↓
Target을 모방
```

### RLHF

```text
Input
↓
Model Response
↓
Preference Reward
↓
더 선호되는 Behavior로 Policy Update
```

한다.

---

# 112. “정답”과 “선호”의 차이

Instruction Tuning:

```text
이 Response가 정답이다.
```

RLHF:

```text
A와 B 중
A가 더 좋다.
```

이다.

Open-ended Generation에서는 두 번째 표현이 자연스러운 경우가 많다.

---

# 113. Reward Model Training과 Policy Training을 구분

매우 중요하다.

### Reward Model

입력:

```text
Prompt + Response
```

출력:

```text
Reward Score
```

목적:

```text
Human Preference를 예측
```

---

### Policy Model

입력:

```text
Prompt
```

출력:

```text
Response
```

목적:

```text
높은 Reward를 얻는 Response 생성
```

이다.

---

# 114. Reward Model은 Answer를 생성하는 Model이 아니다

일반적인 역할은

```text
Response
↓
좋은 정도 Score
```

를 계산하는 것이다.

즉 Judge 역할이다.

---

# 115. Policy Model은 실제 Assistant다

실제 User에게 Text Response를 생성할 Model이다.

RLHF에서는 이 Policy를 Reward Model의 Feedback으로 개선한다.

---

# 116. Human Preference → Reward Model → Policy

전체 Knowledge Flow:

```text
Human Preference
↓
Reward Model
↓
Human Preference를
숫자 Reward로 근사
↓
Policy Model
↓
Reward를 최대화
↓
Human Preference에
가까운 Behavior
```

이다.

---

# 117. 하지만 Reward는 Human Value 그 자체가 아니다

Reward Model은 Human Preference를 **근사한 Proxy**다.

따라서

```text
Reward Model Score ↑
```

와

```text
Real Human Satisfaction ↑
```

가 완전히 같은 것은 아니다.

Reward Hacking의 근본 원인도 여기에 있다.

---

# 118. Alignment

강의에서 Alignment는

> **Model Behavior를 User/Human이 원하는 방향에 부합하도록 맞추는 과정**

이라는 넓은 개념으로 사용된다.

Instruction Tuning, RLHF 등이 모두 Alignment에 기여할 수 있다.

---

# 119. Alignment ≠ Knowledge Training

둘은 겹칠 수 있지만 목적이 다르다.

### Pre-training

```text
더 많은 Knowledge와
Language Pattern 학습
```

### Alignment/Post-training

```text
이미 가진 능력을
어떤 방식으로 사용할지
행동 방향을 조정
```

하는 성격이 강하다.

---

# 120. Synthetic Data가 Post-training에서 중요한 이유

Human Labeling은

```text
비싸고
느리고
Scale 제한
```

이 있다.

Strong LLM을 사용하면

```text
Seed Data
↓
Synthetic Instructions
↓
Synthetic Responses
↓
Filtering
↓
Post-training Dataset
```

을 만들 수 있다.

---

# 121. 그러나 Synthetic Data도 Quality가 핵심

Machine-generated Data가 많다고 자동으로 좋은 Model이 되는 것은 아니다.

필요한 것은

```text
Quality

Diversity

Correctness

Filtering
```

이다.

LIMA의 고품질 소량 Data 메시지와도 연결된다.

---

# 122. PEFT가 Post-training을 실용화한다

Foundation Model이 수십억 Parameter여도

```text
LoRA / Adapter
```

등을 이용해 아주 일부 Parameter만 Update하면 작은 조직이나 개인도 Fine-tuning Experiment를 수행할 수 있다.

---

# 123. Post-training 시대의 중요한 변화

Pre-training 경쟁:

```text
누가 더 큰 Model을
더 많은 Data로 학습하는가?
```

Post-training 경쟁:

```text
누가 더 좋은
Instruction Data

Preference Data

Reward

Synthetic Data

Training Recipe
```

를 설계하는가?

로 일부 중심이 이동한다.

---

# 124. Pre-training vs Post-training

|구분|Pre-training|Post-training|
|---|---|---|
|Data|Massive Raw Text|Instruction/Preference/Reward Data|
|핵심 Objective|Language Modeling|User-aligned Behavior|
|규모|매우 큼|상대적으로 작을 수 있음|
|주요 능력|Language, Knowledge|Instruction Following, Safety, Preference|
|예|Next-token Prediction|SFT, RLHF, DPO, RLVR|

---

# 125. SFT vs RLHF

|구분|SFT|RLHF|
|---|---|---|
|Signal|Target Response|Reward|
|Data|Instruction–Answer|Preference|
|목표|Demonstration 모방|선호도 최적화|
|Open-ended Task|한계 있음|상대 비교 활용 가능|
|Training|Supervised|Reinforcement Learning|

---

# 126. Reward Model vs Policy Model

|구분|Reward Model|Policy Model|
|---|---|---|
|역할|Judge|Generator|
|Input|Prompt + Response|Prompt|
|Output|Scalar Reward|Text Response|
|Training|Preference Pair|Reward Maximization|
|User에게 직접 답함|X|O|

---

# 127. PPO vs DPO

|구분|PPO 기반 RLHF|DPO|
|---|---|---|
|Reward Model|필요|명시적으로 불필요|
|Preference Data|RM Training에 사용|Policy에 직접 사용|
|Online Generation|필요|기본적으로 Offline Pair Training|
|Complexity|높음|상대적으로 낮음|
|Resource|큼|상대적으로 적음|
|Experience|Current Policy Sample 기반|Existing Preference Data 기반|

---

# 128. RLHF vs RLVR

|구분|RLHF|RLVR|
|---|---|---|
|Reward|Human Preference 기반|자동 검증 가능 Reward|
|Reward Model|주로 사용|불필요할 수 있음|
|적합 Task|Open-ended Preference|Math, Code 등|
|판단|주관적일 수 있음|Correct/Incorrect 검증 가능|
|대표 목적|Alignment|Reasoning/Correctness 향상|

---

# 129. 자주 헷갈리는 점 ① — Pre-trained LLM은 Instruction을 이해하지 못하는가?

Knowledge와 Language Understanding은 가지고 있을 수 있다.

문제는

```text
Instruction을
Assistant Command로 받아들이고
그 형식에 맞게 행동
```

하도록 직접 학습된 것은 아니라는 점이다.

Instruction Tuning이 이 Capability를 강화한다.

---

# 130. 자주 헷갈리는 점 ② — Instruction Tuning은 새로운 Architecture인가?

아니다.

기본적으로 기존 Pre-trained Model을 Instruction Dataset으로 Fine-tuning한다.

차이는 Dataset과 Training Goal이다.

---

# 131. 자주 헷갈리는 점 ③ — Instruction Tuning의 목적은 16,000개 Task를 외우는 것인가?

아니다.

많은 Task를 통해 범용적인 Instruction Following Capability를 얻고 Unseen Instruction까지 Generalize하는 것이 목표다.

---

# 132. 자주 헷갈리는 점 ④ — Instruction Tuning Data는 모두 사람이 만들어야 하는가?

아니다.

강의에서는 Alpaca처럼 강한 LLM으로 Synthetic Instruction–Response Data를 생성하는 사례를 소개한다.

---

# 133. 자주 헷갈리는 점 ⑤ — Post-training도 Data가 무조건 많을수록 좋은가?

반드시 그렇지 않다.

강의에서는 LIMA 사례를 통해 고품질 소량 Alignment Data의 중요성을 설명한다.

---

# 134. 자주 헷갈리는 점 ⑥ — Alignment는 Model에 엄청난 새 지식을 넣는 과정인가?

강의의 직관에서는 이미 풍부한 Knowledge를 가진 Model이 User가 원하는 방식으로 그 능력을 발현하게 조정하는 성격이 강하다.

---

# 135. 자주 헷갈리는 점 ⑦ — SFT에서는 좋은 답과 나쁜 답의 정도를 알고 있는가?

Target Response를 모방하도록 학습하는 것이 기본이다.

Candidate Response끼리

```text
A가 B보다 조금 낫다.
```

같은 Preference 관계를 직접 학습하지는 않는다.

---

# 136. 자주 헷갈리는 점 ⑧ — RLHF에서 사람이 매 Training Step마다 Reward를 주는가?

아니다.

사람의 Preference Data로 Reward Model을 먼저 학습하고, 이후 Reward Model이 Human Feedback의 Proxy 역할을 한다.

---

# 137. 자주 헷갈리는 점 ⑨ — Reward Model은 정답 문장을 생성하는가?

아니다.

Response에 대해 Scalar Reward Score를 출력하는 Judge 역할이다.

---

# 138. 자주 헷갈리는 점 ⑩ — Human Preference는 절대 점수로 학습해야 하는가?

강의에서는 사람마다 Absolute Score 기준이 다를 수 있어 상대적인 Pairwise Preference를 사용한다고 설명한다.

---

# 139. 자주 헷갈리는 점 ⑪ — Bradley–Terry Model의 핵심은?

Chosen Response가 Rejected Response보다 더 높은 Reward를 갖도록 Pairwise Preference를 Modeling한다.

---

# 140. 자주 헷갈리는 점 ⑫ — RLHF에서 SFT Model은 버리는가?

아니다.

SFT Model은 RL Policy의 출발점이며 PPO에서는 Reference Policy 역할로도 사용한다.

---

# 141. 자주 헷갈리는 점 ⑬ — PPO는 Reward만 최대화하는가?

아니다.

강의에서는 Reward를 높이면서 Reference SFT Model과 너무 멀어지지 않도록 KL Penalty를 함께 사용하는 직관을 설명한다.

---

# 142. 자주 헷갈리는 점 ⑭ — KL Divergence가 작다는 것은?

Current Policy와 Reference Policy의 Probability Distribution이 상대적으로 비슷하다는 뜻이다.

---

# 143. 자주 헷갈리는 점 ⑮ — Reward가 계속 올라가면 실제 Model도 계속 좋아지는가?

아니다.

Reward Model 자체의 약점을 Policy가 이용하면 Reward는 상승하지만 실제 Human Preference가 떨어지는 Reward Overoptimization이 발생할 수 있다.

---

# 144. 자주 헷갈리는 점 ⑯ — Reward Hacking은 단순 Bug인가?

더 근본적으로

```text
우리가 원하는 Goal
```

과

```text
우리가 정의한 Reward
```

사이의 Gap을 Agent가 이용하는 문제다.

---

# 145. 자주 헷갈리는 점 ⑰ — DPO는 RLHF와 아무 관계가 없는 별도 기법인가?

아니다.

강의에서는 KL-regularized RLHF Objective에서 Preference를 직접 최적화하는 Objective를 수학적으로 유도한 방법으로 설명한다.

---

# 146. 자주 헷갈리는 점 ⑱ — DPO에서는 Response를 계속 Sampling하며 Reward를 받는가?

기본적인 DPO Training에서는 미리 구축한 Chosen/Rejected Preference Data를 이용해 Probability를 계산하고 직접 Loss를 최적화한다.

---

# 147. 자주 헷갈리는 점 ⑲ — DPO가 항상 PPO보다 좋은가?

아니다.

DPO는 간단하고 효율적이지만 현재 Policy의 직접 Experience를 이용하지 않는다는 차이가 있다.

강의에서는 두 방법 모두 상황에 따라 장단점이 있다고 설명한다.

---

# 148. 자주 헷갈리는 점 ⑳ — RLVR도 Human Preference를 필요로 하는가?

검증 가능한 Task에서는 정답 여부를 자동으로 확인할 수 있으므로 Human Preference Reward Model 없이 Reward를 만들 수 있다.

---

# 149. 이번 강의의 발전 흐름을 문제 해결 관점에서 보기

```text
[문제 1]

Pre-trained LLM은
언어는 잘하지만
명령을 제대로 따르지 않음
↓
Instruction Tuning


[문제 2]

Instruction Tuning은
많은 Data가 필요한가?
↓
Synthetic Instruction Data
↓
Alpaca

또는

고품질 Data만
잘 고르면 되지 않을까?
↓
LIMA


[문제 3]

거대한 Model 전체를
Fine-tuning하기 비쌈
↓
PEFT
↓
LoRA / Adapter


[문제 4]

SFT는 하나의
Reference Answer를 모방
↓
Open-ended Generation에서
답이 하나가 아님
↓
Preference Learning


[문제 5]

사람에게 매번 Reward를
받을 수 없음
↓
Reward Model


[문제 6]

Reward Model을 이용해
실제 Policy를 개선
↓
RLHF
↓
PPO


[문제 7]

Reward만 쫓으면
Model이 이상해질 수 있음
↓
KL Penalty
+
Reference SFT Model


[문제 8]

Reward Model 자체도
완벽하지 않음
↓
Reward Hacking
Reward Overoptimization


[문제 9]

PPO는
Reward Model도 필요하고
Training Resource도 큼
↓
DPO
↓
Preference Pair를
직접 Policy에 학습


[문제 10]

Math / Code처럼
정답을 자동 검증 가능
↓
굳이 Human Preference
Reward Model이 필요한가?
↓
RLVR
↓
Verifiable Reward
↓
Reasoning Model
```

---

# 150. 이번 강의에서 반드시 기억할 핵심

### 1. Pre-training의 대표 Objective는 Next-token Prediction이다.

### 2. Pre-training은 Language와 Knowledge를 학습하지만 좋은 Assistant Behavior 자체를 직접 목표로 하지는 않는다.

### 3. 좋은 Language Model과 좋은 Instruction-following Assistant는 동일한 개념이 아니다.

### 4. Pre-training 이후 Model을 Helpful·Safe·User-aligned하게 만드는 과정을 넓게 Post-training이라고 한다.

### 5. 대표 Post-training 방법에는 Instruction Tuning, RLHF, DPO, RLVR 등이 있다.

### 6. Instruction Tuning은 Pre-trained Model을 다양한 Instruction–Response Data로 Fine-tuning하는 방법이다.

### 7. 일반 Task Fine-tuning과 학습 Mechanism은 유사하지만 매우 다양한 Task와 Instruction을 함께 학습한다는 점이 중요하다.

### 8. Instruction Tuning의 핵심 목표는 Instruction Following Capability를 강화하는 것이다.

### 9. Model이 이미 Task Knowledge를 가지고 있어도 User의 세밀한 Instruction을 이해하고 실행하는 것은 별도의 능력이다.

### 10. 다양한 Instruction으로 학습한 뒤 Training에서 보지 못한 Instruction에도 Generalization하는 것이 목적이다.

### 11. Rationale은 최종 Answer 이전의 중간 Reasoning 과정이다.

### 12. MMLU는 여러 Subject의 Knowledge/Problem-solving Capability를 평가하는 대표 Benchmark로 강의에서 소개된다.

### 13. 한국어 Model뿐 아니라 한국어 Benchmark와 Training Data를 구축하는 것도 Sovereign AI의 한 요소가 될 수 있다.

### 14. Instruction Data는 사람이 전부 작성할 필요가 없다.

### 15. Strong LLM을 이용하여 Synthetic Instruction–Response Dataset을 만들 수 있다.

### 16. 강의에서는 Alpaca가 소량 Seed Instruction에서 수만 개 Synthetic Data를 만들고 LLaMA 7B를 Instruction-tuning한 사례로 소개된다.

### 17. PEFT를 사용하면 수십억 Parameter Model에서도 일부 Parameter만 Update하여 Fine-tuning Cost를 줄일 수 있다.

### 18. LoRA와 Adapter가 대표적인 PEFT 예로 소개된다.

### 19. Alignment Data는 항상 매우 많아야 하는 것은 아니다.

### 20. 강의에서는 LIMA를 약 1,000개의 고품질 Data로 좋은 Alignment 성능을 얻은 사례로 소개한다.

### 21. 여기에서 중요한 것은 단순한 소량이 아니라 고품질·다양한 Data다.

### 22. Alignment는 Model에 대규모 새로운 Knowledge를 넣는 것보다 이미 가진 Capability를 User가 원하는 방향으로 발현시키는 성격이 강하다는 것이 강의의 직관이다.

### 23. Instruction Tuning은 기본적으로 Supervised Learning이므로 Reference Response를 모방한다.

### 24. Open-ended Generation에서는 Correct Response가 하나가 아닐 수 있기 때문에 SFT에 구조적 한계가 있다.

### 25. Token-level Loss는 Ground-truth Token Probability를 높이는 것이 목적이며 Candidate 간의 의미적 품질 차이를 직접 표현하지 않는다.

### 26. 이 한계를 해결하기 위해 “정답 하나” 대신 “어느 Response가 더 좋은가”라는 Preference Signal을 사용할 수 있다.

### 27. RLHF는 Human Preference를 이용하여 Language Model의 Policy를 강화학습하는 방법이다.

### 28. RLHF는 크게 SFT → Reward Model → RL 세 단계로 설명할 수 있다.

### 29. Step 1에서는 고품질 Human Instruction–Response Data로 SFT한다.

### 30. Step 2에서는 SFT Model이 여러 Candidate Response를 생성한다.

### 31. Human은 Candidate Response를 상대적으로 Ranking한다.

### 32. Absolute Score보다 Pairwise Comparison을 사용하는 이유는 사람마다 점수 Scale이 다를 수 있기 때문이다.

### 33. Preference Ranking에서 Chosen/Rejected Pair를 만들 수 있다.

### 34. Reward Model은 Prompt와 Response를 입력받아 Scalar Reward Score를 출력한다.

### 35. Reward Model은 Human Preference Ranking과 일치하는 Score 순서를 학습한다.

### 36. Bradley–Terry 계열 Preference Model은 Chosen Response가 Rejected Response보다 더 높은 Reward를 갖도록 학습한다.

### 37. RLHF Step 3에서는 Reward Model을 이용해 Policy Model의 Response를 평가한다.

### 38. Policy는 높은 Expected Reward를 갖는 Response를 더 많이 생성하도록 Update된다.

### 39. RLHF에서 Model은 이미 강력한 SFT Model에서 출발한다.

### 40. LLM에 RL을 사용하는 목적은 단순 Task Performance뿐 아니라 Safety와 Human Alignment까지 포함할 수 있다.

### 41. Reward Model 자체를 잘 만드는 것이 RLHF의 핵심 Difficulty 중 하나다.

### 42. Reward Model이 잘못되면 높은 Reward와 실제 Human Satisfaction이 불일치할 수 있다.

### 43. Agent가 정의된 Reward를 의도와 다르게 최대화하는 현상을 Reward Hacking이라고 한다.

### 44. LLM에서도 Hallucination, 과도한 아부, 그럴듯한 표현 등이 Reward Model의 약점을 이용하는 Behavior로 나타날 수 있다.

### 45. Reward Model Score는 계속 올라가지만 실제 Human Preference가 떨어지는 현상을 Reward Model Overoptimization으로 설명할 수 있다.

### 46. PPO는 초기 RLHF에서 대표적으로 사용된 Policy Optimization 방법이다.

### 47. PPO의 Proximal은 Policy를 지나치게 크게 변경하지 않는다는 직관과 연결된다.

### 48. RLHF에서는 SFT Model을 Reference Policy로 두고 Current Policy와의 KL Divergence를 Penalty로 사용할 수 있다.

### 49. 즉 Reward는 높이면서 기존 SFT Policy에서 너무 멀어지지 않도록 한다.

### 50. PPO 기반 RLHF는 Reward Model, Reference Model, Trainable Policy Model 등 여러 큰 Model을 운용해야 해 Memory와 Compute 부담이 크다.

### 51. DPO는 이러한 복잡성을 줄이기 위한 Direct Preference Optimization 방법이다.

### 52. DPO에서는 별도의 Reward Model을 명시적으로 Training해 Online RL을 수행하는 대신 Preference Pair를 Policy에 직접 학습한다.

### 53. Prompt + Chosen + Rejected Data로 Chosen의 Relative Probability가 높아지도록 학습한다.

### 54. DPO는 일반 Fine-tuning과 비슷한 Offline Optimization 형태로 구현할 수 있어 비교적 단순하다.

### 55. 강의에서는 DPO Objective가 기존 KL-regularized RLHF Objective와 연결되는 수학적 유도에서 나온다고 설명한다.

### 56. PPO는 현재 Policy가 직접 Response를 생성하고 Reward를 받는 Experience 기반 Training이라는 특징이 있다.

### 57. DPO는 미리 구축된 Preference Dataset을 사용하는 Offline Training의 성격이 강하다.

### 58. 따라서 PPO와 DPO는 각각 장단점이 있으며 하나가 항상 더 우월한 것은 아니다.

### 59. RLVR은 Reinforcement Learning with Verifiable Rewards를 의미한다.

### 60. Math나 Code처럼 Answer Correctness를 자동으로 확인할 수 있다면 Reward Model 대신 직접 Verifiable Reward를 사용할 수 있다.

### 61. 예를 들어 Correct Answer에 1, Incorrect Answer에 0을 주는 식의 Reward가 가능하다.

### 62. 강의에서는 단순해 보이는 Verifiable Reward 기반 RL이 강한 Reasoning Model 학습에 효과가 있음을 보여준 최근 흐름을 설명한다.

### 63. RLHF는 주관적인 Human Preference가 중요한 Open-ended Task에 적합하고 RLVR은 정답을 자동 검증할 수 있는 Task에 특히 적합하다.

### 64. Post-training의 전체 목적은 이미 강력한 Foundation Model을 User가 실제로 유용하고 안전하게 사용할 수 있는 Assistant로 변환하는 것이다.

---

# 151. 전체 강의 논리

```text
대규모 Text로
Language Model을 학습한다.
        ↓
Pre-training
        ↓
Language와 Knowledge는 풍부하다.


그런데
User가 명령을 해도
그게 명령인지 제대로 모를 수 있다.
        ↓
좋은 LM
≠
좋은 Assistant
        ↓
Post-training


가장 먼저
        ↓
Instruction Tuning
        ↓
다양한 Instruction
+
Response
        ↓
Instruction Following Capability


그런데
사람이 Dataset을
다 만들어야 하나?
        ↓
아니다.
        ↓
Strong LLM으로
Synthetic Instruction Data
        ↓
Alpaca


그리고
Data가 엄청 많아야 하나?
        ↓
아니다.
        ↓
고품질 Data가 중요
        ↓
LIMA


그런데
Model이 너무 크다.
        ↓
전체 Fine-tuning 비쌈
        ↓
PEFT
        ↓
LoRA / Adapter


Instruction Tuning을 했더니
명령은 잘 따른다.
        ↓
하지만
Open-ended Task에는
답이 하나가 아니다.
        ↓
SFT는
Reference Answer 하나를
모방하는 구조
        ↓
Preference를 사용하자.


Response A
vs
Response B
        ↓
사람이
A가 더 좋다고 판단
        ↓
Preference Pair


하지만
사람에게 매 Training Step마다
물어볼 수 없다.
        ↓
Human Preference Data
        ↓
Reward Model
        ↓
Human Judge를 근사


Reward Model 준비
        ↓
Policy LLM이
Response 생성
        ↓
Reward 평가
        ↓
Reward 최대화
        ↓
RLHF


그런데
Reward만 높이면
Policy가 이상해질 수 있다.
        ↓
기존 SFT Model과
너무 달라지지 않게
        ↓
KL Penalty
        ↓
PPO


하지만
Reward Model도 틀릴 수 있다.
        ↓
Reward Hacking
Reward Overoptimization


그리고
PPO는
여러 Model을 동시에 운용
        ↓
비싸고 복잡
        ↓
DPO


Preference Pair
        ↓
Chosen Probability ↑
Rejected Probability ↓
        ↓
Policy를 직접 학습


마지막으로
        ↓
수학·Code처럼
정답을 자동 검증할 수 있다.
        ↓
굳이
Human Reward Model이
필요한가?
        ↓
Verifiable Reward
        ↓
RLVR
        ↓
Strong Reasoning Model
```

---

# 152. 이번 강의 핵심 한 문장

> **Post-training은 Next-token Prediction으로 언어와 지식을 학습한 Pre-trained Language Model을 실제 User의 의도와 가치에 맞는 Assistant로 변환하는 단계로, 먼저 다양한 Instruction–Response Data를 이용한 Instruction Tuning/SFT로 명령을 따르는 능력을 학습하고, 하나의 정답을 모방하는 Supervised Learning의 한계를 보완하기 위해 Human Preference Pair로 Reward Model을 학습한 뒤 PPO를 이용해 높은 Reward를 받으면서도 기존 SFT Policy에서 지나치게 벗어나지 않도록 RLHF를 수행하며, Reward Model과 복잡한 Online RL의 부담을 줄이기 위해 Preference Data를 직접 학습하는 DPO가 등장했고, 수학·Code처럼 정답을 자동으로 검증할 수 있는 Task에서는 Verifiable Reward를 직접 이용하는 RLVR로 확장된다.**

가장 짧게 기억하면 다음과 같다.

```text
Pre-training
= 언어와 지식을 배운다.

Instruction Tuning
= 사람의 명령을
  알아듣는 법을 배운다.

SFT
= 좋은 답을 보고
  그대로 따라 배운다.

RLHF
= 여러 답 중
  어떤 답이 더 좋은지를 배운다.

Reward Model
= 사람 대신
  답변의 선호도를 평가한다.

PPO
= Reward는 높이되
  기존 SFT Model에서
  너무 멀어지지 않는다.

DPO
= Reward Model 없이
  Chosen / Rejected Pair를
  직접 학습한다.

RLVR
= 정답을 자동 검증해서
  그 결과를 Reward로 사용한다.
```