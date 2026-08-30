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

