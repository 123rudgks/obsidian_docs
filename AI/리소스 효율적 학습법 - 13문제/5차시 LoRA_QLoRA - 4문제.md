[강의 링크](https://www.youtube.com/watch?v=bYItXnb4kqI)

## 시험 범위에 포함되는 내용

| 시험 토픽                | 강의 내용                                                        |
| -------------------- | ------------------------------------------------------------ |
| **사전학습과 파인튜닝**       | Pretrained Foundation Model → Domain Dataset → Fine-tuning   |
| **단일 태스크 파인튜닝**      | 특정 Task/Domain에 맞춰 하나의 Specialized Model 생성                  |
| **도메인 특화 AI**        | General Foundation Model → Legal/Finance/Medical 등 Domain 특화 |
| **저비트 Quantization** | QLoRA에서 Base Model을 4-bit 등으로 Quantize                       |
# 0. 이번 강의의 핵심부터

앞 차시까지 배운 것은 **Model Compression**이었다.

```text
Large Model
↓
성능은 최대한 유지하면서
↓
Model Size / Memory / Compute 감소
```

대표적으로

```text
Quantization
Pruning
Knowledge Distillation
```

이었다.

하지만 **LoRA의 출발점은 다르다.**

```text
Large Pretrained Model
↓
특정 Domain / Task에 맞게
다시 학습하고 싶다.
↓
Fine-tuning 필요

그런데
전체 Parameter를 다시 학습하면
너무 비싸다.
↓
PEFT
↓
LoRA
```

즉

> **LoRA의 핵심 목표는 Model Compression이 아니라 Fine-tuning Cost를 줄이는 것이다.**

---

# 1. Model Compression과 Fine-tuning의 목적 차이

## Model Compression

이미 좋은 Model이 있다.

목표:

```text
Model Quality 최대한 유지
+
Model Size ↓
Memory ↓
Latency ↓
Energy ↓
```

이다.

---

## Fine-tuning

이미 범용 Model이 있다.

목표:

```text
General Model
↓
Specific Task / Domain
↓
Specialized Model
```

이다.

예:

```text
General Foundation Model
↓
Legal Data로 Fine-tuning
↓
Legal Expert Model
```

이다.

강의에서는 범용 Foundation Model을 특정 Domain으로 특화시키기 위해 처음부터 다시 학습하는 대신 Fine-tuning을 사용한다고 설명한다.

---

# 2. Fine-tuning의 예 — 법률 Agent

Foundation Model이

```text
법률
금융
상담
일반 지식
```

등을 두루두루 잘한다고 하자.

그런데 회사가 원하는 것은

```text
법률 전용 Chatbot
```

이다.

그러면

```text
Foundation Model
+
Legal Dataset
↓
Fine-tuning
↓
Legal-specialized Model
```

을 만들 수 있다.

---

# 3. Fine-tuning의 Trade-off

특정 Domain 성능은 좋아질 수 있지만

```text
Legal Performance ↑
```

대신 다른 일반 능력이 일부 감소할 수도 있다.

하지만 Application 목적이 법률 서비스라면

```text
Domain Specialization
```

이 더 중요할 수 있다.

---

# 4. 가장 가벼운 적응 방법 — Prompt / In-context Learning

Model Parameter를 건드리지 않고

```text
너는 법률 전문가다.

판례를 고려해 답해라.

이 Domain 규칙을 따라라.
```

등을 Prompt에 넣는 방법이 있다.

```text
Parameter Update
= 없음
```

이다.

---

# 5. Prompt 기반 방식의 장점

```text
Training 필요 없음

빠르게 적용

Task 전환 쉬움

Model 원본 유지
```

이다.

---

# 6. 하지만 한계가 있다

Model Parameter 자체가 바뀐 것은 아니다.

따라서 강의에서는 Prompt만으로는 특정 Domain의 **진짜 전문가 수준으로 충분히 특화하는 데 한계가 있을 수 있다**고 설명한다.

---

# 7. Full Fine-tuning

가장 직접적인 방법은 Pretrained Model 전체를 다시 학습하는 것이다.

```text
Pretrained Weight
↓
Domain Dataset
↓
모든 Weight Update
↓
Specialized Model
```

이다.

---

# 8. Full Fine-tuning의 장점

전체 Model Parameter를 조정할 수 있으므로

```text
Domain Adaptation Capacity ↑
```

가 크다.

충분한 Data와 Compute가 있다면 특정 Domain 성능을 크게 높일 수 있다.

---

# 9. Full Fine-tuning의 문제 ① Training Cost

Foundation Model이 매우 크면

```text
Parameter 수 ↑
↓
Gradient 저장 ↑
Optimizer State ↑
Training Memory ↑
Compute ↑
```

한다.

따라서 조금 Fine-tuning하는 것조차 상당히 비쌀 수 있다.

---

# 10. Full Fine-tuning의 문제 ② 모든 Weight를 건드린다

전체 Weight가 바뀐다.

그 결과 특정 Domain에는 좋아지더라도 기존 General Capability 일부가 변하거나 약해질 수 있다.

강의에서는 이를 기존 Knowledge를 잊는 문제와 연결해 설명한다.

---

# 11. 그래서 PEFT

이 문제에서 등장하는 것이

**PEFT — Parameter-Efficient Fine-Tuning**

이다.

핵심 질문:

> **Model 전체를 다시 학습하지 않고 일부 Parameter만 학습해서 원하는 Domain에 특화할 수 없을까?**

---

# 12. PEFT의 “Efficient”는 무엇이 효율적인가?

매우 중요하다.

앞의 Model Compression에서 Efficient의 대상:

```text
Inference

Model Size

Runtime

Memory
```

였다.

PEFT에서 Efficient의 대상:

```text
Fine-tuning / Re-training Process
```

다.

즉

```text
PEFT
=
Fine-tuning에 필요한
Trainable Parameter와 Training Resource를 줄이는 방법
```

이다.

---

# 13. PEFT의 핵심 철학

```text
Original Model 전체
→ Freeze

일부 Parameter만
→ Train
```

한다.

문제는

> **“어떤 일부 Parameter를 학습할 것인가?”**

이다.

---

# 14. 초기 PEFT 방법 — Adapter Layer

한 가지 방법은 기존 Transformer Block 사이 또는 뒤에 작은 Layer를 추가하는 것이다.

```text
Original Layer
↓
Adapter
↓
Original Layer
↓
Adapter
```

처럼 구성한다.

---

# 15. Adapter Fine-tuning

```text
Original Transformer Weight
→ Freeze

Added Adapter Weight
→ Train
```

한다.

따라서 Full Fine-tuning보다 학습해야 하는 Parameter가 훨씬 줄어든다.

---

# 16. Adapter의 장점

```text
Original Weight 유지

Trainable Parameter ↓

Full Fine-tuning보다 Training Cost ↓
```

이다.

---

# 17. Adapter의 한계

충분한 Domain Adaptation 성능을 얻으려면 생각보다 Adapter Layer가 많이 필요할 수 있다.

또한

```text
Original Architecture
```

에 새로운 Layer를 넣기 때문에 Model Structure 자체가 바뀐다.

강의에서는 Adapter가 Full Fine-tuning보다는 효율적이지만 여전히 추가 Layer 부담이 있다고 설명한다.

---

# 18. 더 가벼운 방향 — Prompt / Embedding Tuning

다른 방향은 Model 전체를 Freeze하고 Input 쪽의

```text
Prompt Embedding

Token-level Embedding
```

등 일부만 학습하는 것이다.

---

# 19. 장점

Trainable Parameter가 매우 적다.

따라서

```text
Training 빠름

Task별 Adapter 저장량 ↓

같은 Backbone 재사용
```

이 가능하다.

---

# 20. 여러 Domain을 빠르게 바꾸기도 쉽다

예:

```text
Same Foundation Model
├─ Legal Prompt Parameters
├─ Finance Prompt Parameters
├─ Security Prompt Parameters
└─ Counseling Prompt Parameters
```

처럼 하나의 Backbone에 Domain-specific 작은 Parameter만 교체할 수 있다.

---

# 21. 하지만 표현력이 제한된다

Model 내부 Weight Matrix를 직접 충분히 바꾸는 것이 아니므로 특정 Domain에 깊이 특화시키는 능력은 제한될 수 있다.

강의에서는 이를

```text
전문가가 된다
```

보다는

```text
일반인이 해당 Domain을
조금 더 잘 알게 된다
```

에 가까운 직관으로 설명한다.

---

# 22. Adapter와 Prompt Tuning 사이의 빈 공간

정리하면:

### Adapter

```text
Adaptation Capacity ↑

하지만
추가 Parameter / Structure 부담 ↑
```

### Prompt Tuning

```text
Training Cost ↓↓↓

하지만
Adaptation Capacity 제한
```

이다.

그 사이의 좋은 절충점이 필요했다.

---

# 23. 여기서 LoRA 등장

**LoRA = Low-Rank Adaptation**

강의에서는 LoRA가 PEFT 계열에서 매우 중요한 대표 기법이 되었다고 설명한다.

핵심:

> **원본 Weight Matrix는 Freeze하고, 그 Weight에 더해질 작은 Low-rank Update만 학습한다.**

---

# 24. Full Fine-tuning부터 수식으로 보자

Original Weight Matrix를

```text
W
```

라고 하자.

Full Fine-tuning에서는

```text
W
↓
W'
```

로 Weight 전체를 업데이트한다.

즉

```text
W' = W + ΔW
```

라고 볼 수 있다.

여기서 `ΔW` 자체도 `W`와 같은 큰 Matrix다.

---

# 25. LoRA의 핵심 관찰

Fine-tuning에서 필요한 Weight 변화 `ΔW`가 반드시 Full-rank의 거대한 Matrix일 필요가 없을 수 있다고 본다.

즉

```text
ΔW
```

를 작은 두 Matrix의 곱으로 표현한다.

```text
ΔW = BA
```

처럼 둔다.

---

# 26. Low-rank decomposition

Original Weight:

```text
W ∈ R^(d × k)
```

라고 하자.

LoRA에서는

```text
A ∈ R^(r × k)

B ∈ R^(d × r)
```

를 사용한다.

여기서

```text
r << d, k
```

인 작은 Rank `r`을 선택한다.

그러면

```text
BA ∈ R^(d × k)
```

가 되어 Original Weight와 같은 Shape의 Update를 만들 수 있다.

---

# 27. LoRA Forward

기본 구조는

```text
y
=
Wx
+
BAx
```

라고 이해하면 된다.

Original Path:

```text
x
↓
W
↓
Wx
```

LoRA Path:

```text
x
↓
A
↓
B
↓
BAx
```

두 결과를 더한다.

---

# 28. Training에서는 무엇을 업데이트하는가?

```text
W
→ Freeze

A, B
→ Train
```

한다.

즉 거대한 `W` 대신 작은 Low-rank Matrix 두 개만 학습한다.

---

# 29. 왜 Parameter가 줄어드는가?

Original Weight Parameter 수:

```text
d × k
```

LoRA Parameter:

```text
r × k
+
d × r
```

즉

```text
r(k+d)
```

이다.

`r`이 매우 작으면

```text
r(k+d)
<<
dk
```

가 된다.

---

# 30. 예시로 직관 잡기

Original Matrix:

```text
4096 × 4096
```

이라면 Parameter 수는 약

```text
16.8 million
```

이다.

LoRA Rank가

```text
r = 8
```

이면

```text
4096×8 + 8×4096
```

만 학습한다.

약

```text
65 thousand
```

수준이다.

즉 학습 Parameter가 극단적으로 줄어든다.

---

# 31. LoRA의 핵심 철학

```text
Original Model Knowledge
→ W에 그대로 유지

Domain-specific Change
→ 작은 Low-rank ΔW에 저장
```

한다.

강의의 표현대로 보면

```text
Original General Intelligence
+
Small Domain-specific Essence
```

를 결합하는 구조다.

---

# 32. LoRA의 중요한 장점 ① Original Weight Freeze

기존 Weight를 직접 바꾸지 않는다.

따라서 Full Fine-tuning보다 기존 Model Knowledge를 크게 훼손할 위험을 줄일 수 있다.

---

# 33. 장점 ② Trainable Parameter 감소

```text
Full Fine-tuning
→ Billions of trainable parameters

LoRA
→ 작은 Low-rank parameters
```

이다.

따라서 Training Memory와 Compute를 크게 줄일 수 있다.

---

# 34. 장점 ③ Task별 작은 LoRA만 저장

하나의 Foundation Model에

```text
Legal LoRA

Finance LoRA

Medical LoRA

Coding LoRA
```

를 별도로 저장할 수 있다.

Base Model은 공통으로 재사용한다.

---

# 35. 장점 ④ Input/Output Dimension 유지

LoRA Update `BA`가 Original `W`와 같은 Shape을 만들어 내므로

```text
Input Shape

Output Shape
```

는 변하지 않는다.

즉 외부 관점에서 Layer Interface를 유지할 수 있다.

---

# 36. LoRA는 모델 압축인가?

**아니다.**

강의에서 가장 반복해서 강조하는 지점이다.

LoRA는

```text
Original Model
+
LoRA Parameter
```

를 사용한다.

따라서 엄밀히 보면 Model Parameter가 조금 **늘어난다.**

---

# 37. LoRA의 목표

```text
Model Size ↓
```

가 아니다.

목표는

```text
Trainable Parameter ↓

Fine-tuning Memory ↓

Fine-tuning Compute ↓
```

이다.

---

# 38. LoRA와 Quantization을 혼동하면 안 된다

### Quantization

```text
Original Model 자체의
Bit-width ↓
↓
Model Size ↓
```

### LoRA

```text
Original Model Freeze
+
Small Trainable Parameters
↓
Fine-tuning Cost ↓
```

이다.

---

# 39. 그런데 두 세계가 만난다

강의에서는

```text
Model Compression
```

과

```text
Parameter-efficient Fine-tuning
```

이 만나는 지점으로 **QLoRA**를 소개한다.

---

# Part 2. QLoRA

# 40. QLoRA의 출발점

Full-precision Model이 있다고 하자.

예:

```text
FP16 / FP32 Model
```

이를 아주 강하게 Quantize한다.

```text
FP16
↓
4 bit
```

로 줄인다.

---

# 41. 강한 Quantization의 문제

Model Size는 크게 줄었다.

하지만

```text
Quantization Error ↑
↓
Model Quality ↓
```

가 발생할 수 있다.

즉

```text
Compression 성공
Performance 손실
```

상태다.

---

# 42. 그러면 LoRA를 붙인다

Quantized Base Model은 Freeze한다.

```text
Quantized Base Weight
→ Freeze
```

그리고

```text
LoRA A, B
```

만 더 높은 Precision으로 학습한다.

예:

```text
Base
→ 4 bit

LoRA
→ FP16 / BF16 등
```

이다.

---

# 43. QLoRA의 핵심 구조

```text
Large Pretrained Model
↓
Aggressive Quantization
↓
4-bit Base Model
↓
Freeze
+
Higher-precision LoRA
↓
Fine-tuning
```

한다.

즉

> **Quantized Backbone + Trainable LoRA**

가 QLoRA의 핵심이다.

---

# 44. 왜 효과가 있는가?

거대한 Base Model 전체를 High Precision으로 유지하지 않는다.

대부분의 Parameter는

```text
4 bit
```

로 저장한다.

반면 Fine-tuning에서 필요한 Adaptation 정보는 매우 적은 LoRA Parameter에

```text
16 bit / higher precision
```

으로 충분히 저장할 수 있다.

---

# 45. 전체 평균 Precision

Base Parameter가 압도적으로 많고 LoRA Parameter가 매우 적다면

```text
대부분 = 4 bit

아주 작은 일부 = 16 bit
```

여도 전체 Memory는 거의 4-bit Model에 가까워진다.

강의에서는 이러한 구조 때문에 강한 Compression과 Fine-tuning을 동시에 얻을 수 있다고 설명한다.

---

# 46. LoRA와 QLoRA의 목적 차이

매우 중요하다.

## LoRA

```text
Base Model
→ 원래 Precision 유지

Base Weight
→ Freeze

Low-rank Parameter
→ Train

목적
= Fine-tuning Efficiency
```

---

## QLoRA

```text
Base Model
→ Low-bit Quantization

Base Weight
→ Freeze

Low-rank Parameter
→ Higher Precision으로 Train

목적
= Fine-tuning Efficiency
+
Memory Efficiency
```

이다.

---

# 47. 강의에서 설명하는 QLoRA 활용 방향

Quantized Model의 성능을 보완하는 데 사용할 수도 있고,

```text
Quantized Base
+
LoRA
↓
General Capability 보완
```

특정 Domain에 맞춰

```text
Quantized Base
+
Legal LoRA
↓
Legal Expert
```

처럼 사용할 수도 있다.

즉 QLoRA에서도 Fine-tuning Dataset에 따라 목적은 달라질 수 있다.

---

# 48. QLoRA는 Quantization Error를 완전히 없애는가?

아니다.

Base Model 자체가 이미 Low-bit로 Quantized되어 있으므로 표현력 일부를 잃는다.

LoRA가 그 손실을 보완할 수 있지만

```text
Full-precision LoRA
```

와 완전히 동일한 Accuracy를 항상 보장하는 것은 아니다.

---

# 49. LoRA vs QLoRA Accuracy Trade-off

개념적으로:

```text
LoRA
→ Base Precision 높음
→ Accuracy 보존에 유리

QLoRA
→ Base Quantized
→ Memory에 유리
→ Accuracy 조금 손실 가능
```

이다.

강의에서는 QLoRA가 LoRA와 완전히 동일한 성능을 항상 얻는 것이 목표가 아니라, **조금의 성능 저하를 감수하고 큰 Memory 절감을 얻는 Trade-off**라고 설명한다.

---

# 50. QLoRA는 Mixed Precision 구조다

Base:

```text
4 bit
```

LoRA:

```text
16 bit
```

처럼 서로 다른 Precision이 함께 존재한다.

따라서 넓은 의미에서

```text
Mixed Precision
```

System이다.

---

# 51. 이것이 Hardware에서 왜 문제가 될 수 있는가?

모든 Tensor가 동일하게 INT4라면

```text
INT4 Kernel
```

만 사용할 수 있다.

하지만 QLoRA에서는

```text
4-bit Base

16-bit LoRA
```

가 함께 존재한다.

따라서 Hardware가 서로 다른 Format을 처리해야 한다.

---

# 52. QLoRA의 Runtime Trade-off

```text
Memory
↓ 크게 감소

하지만

Arithmetic / Kernel Switching
Mixed Precision Handling
Dequantization
```

등의 Overhead가 생길 수 있다.

그래서

> **QLoRA의 핵심 장점은 특히 Training Memory 절약이지, 모든 연산이 동일하게 4 bit가 되어 무조건 최대 속도를 얻는다는 뜻은 아니다.**

강의에서도 모든 Parameter가 동일한 4/8 bit가 아니므로 GPU에서는 순수 Low-bit Model보다 실행이 복잡해질 수 있다고 설명한다.

---

# 53. LoRA를 이해할 때 반드시 구분할 세 가지

## Full Fine-tuning

```text
전체 W
→ Train
```

---

## Adapter

```text
W
→ Freeze

추가 Layer
→ Train
```

---

## LoRA

```text
W
→ Freeze

Low-rank ΔW = BA
→ Train
```

한다.

---

# 54. Full Fine-tuning vs Adapter vs LoRA

|방식|Base Weight|추가 구조|Trainable Parameter|Domain Adaptation|
|---|---|---|---|---|
|Full FT|Update|없음|매우 많음|강함|
|Adapter|Freeze|Adapter Layer|중간|강한 편|
|Prompt Tuning|Freeze|Prompt/Embedding|매우 적음|제한될 수 있음|
|LoRA|Freeze|Low-rank Update|매우 적음|강한 편|

---

# 55. LoRA가 “Sweet Spot”으로 설명되는 이유

강의의 흐름을 정리하면:

```text
Prompt Tuning
→ 너무 가벼움
→ 표현력 부족

Adapter
→ 표현력 좋음
→ 추가 Parameter/Structure 부담

LoRA
→ 작은 Parameter
+
강한 Adaptation
```

이라는 절충점이다.

---

# 56. LoRA에서 Rank란?

Low-rank Matrix의 중간 Dimension이다.

```text
A: r × k

B: d × r
```

의 `r`이다.

---

# 57. Rank가 작으면

```text
Trainable Parameter ↓

Training Memory ↓

Adaptation Capacity ↓ 가능
```

이다.

---

# 58. Rank가 크면

```text
Trainable Parameter ↑

Adaptation Capacity ↑ 가능

Training Cost ↑
```

이다.

따라서 Rank는 LoRA의 핵심 Hyperparameter다.

---

# 59. 강의의 실습 관점

강의에서는 실제 Library 수준에서 LoRA 설정 시

```text
rank

alpha

target modules
```

등을 설정하게 된다고 설명한다.

실습에서는 이러한 Parameter의 의미를 확인하는 것이 중요하다고 언급한다.

---

# 60. Target Module

Transformer의 모든 Weight Matrix에 반드시 LoRA를 붙이는 것은 아니다.

특정 Layer/Matrix만 선택할 수 있다.

예:

```text
Attention Projection

Q / K / V

Output Projection

MLP
```

등이 Target이 될 수 있다.

강의에서는 구체적 최적 Target을 제시하기보다 Library에서 이를 지정할 수 있다는 수준으로 설명한다.

---

# 61. Alpha

LoRA Update를 Original Output에 어느 정도 Scale로 반영할지 조절하는 Parameter로 이해할 수 있다.

강의에서는 세부 수식보다는 실습에서 조절하는 주요 LoRA Parameter라는 점을 언급한다.

---

# 62. Fine-tuning에서 Model Size는 어떻게 되는가?

LoRA:

```text
Base Model
+
A/B Parameter
```

이므로 엄밀히

```text
Model Size
→ 아주 조금 증가
```

한다.

---

# 63. 그런데 왜 효율적인가?

Base Model 전체의

```text
Gradient

Optimizer State

Trainable Weight
```

를 관리할 필요가 없다.

즉 저장된 최종 Weight 크기보다 **Fine-tuning 과정에 필요한 Training Resource**가 핵심이다.

---

# 64. QLoRA에서는 Size가 실제로 줄어든다

QLoRA에서는 Base Model 자체를 Quantize하기 때문에

```text
Base Model Memory ↓↓↓
```

한다.

여기에 작은 LoRA Parameter만 High Precision으로 추가한다.

따라서 전체 Memory Footprint도 큰 폭으로 줄일 수 있다.

---

# 65. LoRA vs QLoRA

|구분|LoRA|QLoRA|
|---|---|---|
|Base Model|Full/Higher Precision|Quantized Low-bit|
|Base Weight|Freeze|Freeze|
|Trainable|Low-rank A/B|Low-rank A/B|
|LoRA Precision|일반적으로 높은 Precision|높은 Precision 유지|
|Fine-tuning Cost|Full FT보다 낮음|더 낮은 Memory 가능|
|Base Model Size|그대로|크게 감소|
|Accuracy|상대적으로 유리|일부 손실 가능|
|핵심 목표|PEFT|PEFT + Quantization|

---

# 66. Quantization vs LoRA vs QLoRA

## Quantization

```text
Goal
= Model Compression

FP16
↓
INT4
```

이다.

---

## LoRA

```text
Goal
= Efficient Fine-tuning

Base Freeze
+
Low-rank Update Train
```

이다.

---

## QLoRA

```text
Goal
= Quantized Model을 이용한
Memory-efficient Fine-tuning

Quantized Base
+
LoRA
```

다.

---

# 67. QLoRA가 앞 강의들과 만나는 이유

지금까지 배운 내용을 하나로 연결한다.

```text
Floating Point
↓
Low Precision
↓
Quantization

+

Fine-tuning
↓
PEFT
↓
LoRA

=

QLoRA
```

이다.

그래서 강의에서는 QLoRA를 이해하면 앞에서 배운 **Number Representation·Quantization·Fine-tuning Efficiency**가 하나의 System으로 연결된다고 설명한다.

---

# 68. LoRA는 왜 Low-rank인가?

핵심 가정은

> **특정 Task에 Fine-tuning하면서 필요한 Weight 변화가 원본 Weight Matrix 전체의 모든 자유도를 반드시 사용할 필요는 없다.**

는 것이다.

따라서 작은 Rank `r`의 Matrix 두 개만으로도 필요한 Adaptation의 핵심 방향을 표현할 수 있다고 본다.

---

# 69. 이를 직관적으로 보면

Full Fine-tuning:

```text
거대한 Weight 공간 전체를
자유롭게 변경
```

LoRA:

```text
그 거대한 공간에서
Task에 필요한 몇 개의
중요한 변화 방향만 학습
```

한다.

---

# 70. 그래서 Low Rank가 Efficiency를 만든다

```text
Task-specific Change
≈ Low-dimensional Structure
```

라고 보고 전체 `ΔW` 대신 작은 `A`, `B`만 학습한다.

---

# 71. LoRA의 Training Flow

```text
Pretrained Model
↓
Base Weight Freeze

↓

LoRA A/B 추가

↓

Domain Dataset

↓

Forward
W x + BA x

↓

Backpropagation

↓

W는 그대로

A/B만 Update
```

한다.

---

# 72. QLoRA Training Flow

```text
Pretrained Model
↓
Low-bit Quantization
↓
Quantized Base Weight Freeze

↓

High-precision LoRA A/B 추가

↓

Fine-tuning Dataset

↓

Forward

Quantized Base Path
+
LoRA Path

↓

Backpropagation

↓

Quantized Base는 Freeze

LoRA만 Update
```

한다.

---

# 73. 왜 QLoRA는 큰 Model에서 특히 의미가 있는가?

Large Model Fine-tuning의 가장 큰 문제 중 하나는

```text
GPU Memory
```

다.

Full Model을 High Precision으로 올려놓기만 해도 많은 VRAM이 필요하다.

---

# 74. Quantized Backbone

Base Model을

```text
16 bit
→
4 bit
```

로 줄이면 Weight Memory를 크게 줄일 수 있다.

따라서 더 작은 GPU에서도 Fine-tuning이 가능해질 수 있다.

---

# 75. 그리고 Trainable Parameter도 적다

LoRA를 사용하므로 Gradient를 계산하고 Update할 Parameter도 작다.

즉 QLoRA는 두 방향에서 Resource를 줄인다.

```text
Base Weight Memory
↓
Quantization

Trainable Parameter
↓
LoRA
```

이다.

---

# 76. QLoRA의 핵심 Resource Benefit

```text
Quantization
→ Static Backbone Memory 감소

LoRA
→ Trainable Parameter 감소
```

두 효과가 동시에 존재한다.

---

# 77. 하지만 완벽한 최적화는 없다

강의 마지막의 중요한 메시지다.

```text
LoRA보다 QLoRA가
무조건 좋다.
```

가 아니다.

QLoRA는 Base를 강하게 Quantize했기 때문에 Representation Capacity 일부를 희생한다.

---

# 78. 따라서 선택 문제

```text
더 높은 Accuracy
→ LoRA

더 낮은 Memory
→ QLoRA
```

처럼 Application Constraint에 따라 선택한다.

물론 실제 차이는 Model·Task·Quantization 방식에 따라 달라진다.

---

# 79. 쉬운 Task에서는 차이가 거의 없을 수도 있다

문제가 충분히 단순하거나 Quantization Robustness가 높다면

```text
LoRA
≈
QLoRA
```

수준의 성능이 나올 수도 있다.

---

# 80. 복잡한 Task에서는 차이가 나타날 수 있다

Base Quantization Error가 중요하게 작용하면

```text
QLoRA Quality
<
LoRA Quality
```

가 될 수 있다.

강의에서는 이러한 Accuracy 감소 자체가 비정상이 아니라 **Memory Efficiency를 얻기 위해 받아들이는 Trade-off**라고 설명한다.

---

# 81. Compression 세계와 PEFT 세계의 차이

```text
Compression
→ 이미 있는 Model을
  더 작고 싸게 실행

PEFT
→ 이미 있는 Model을
  더 싸게 재교육
```

한다.

---

# 82. QLoRA가 특이한 이유

```text
Compression
+
PEFT
```

를 동시에 사용하기 때문이다.

---

# 83. 앞 차시와 전체 연결

## Quantization

```text
모든 Weight는 유지

Bit-width ↓
```

---

## Pruning

```text
일부 Weight 제거
```

---

## Distillation

```text
Large Teacher
↓
Small Student
```

---

## LoRA

```text
Base 유지

Low-rank Update만 학습
```

---

## QLoRA

```text
Base Quantization
+
Low-rank Update 학습
```

이다.

---

# 84. 각 기법의 목적 비교

|기법|주요 목적|
|---|---|
|Quantization|Model Memory / Arithmetic Cost 감소|
|Pruning|Parameter 개수·Sparsity 활용|
|Distillation|작은 Student Model 생성|
|LoRA|Fine-tuning Training Cost 감소|
|QLoRA|Low-memory Fine-tuning|

---

# 85. 자주 헷갈리는 점 ① — LoRA는 Model Compression인가?

아니다.

엄밀히는 **PEFT 기법**이다.

Base Model은 그대로 존재하며 Low-rank Parameter가 추가된다.

---

# 86. 자주 헷갈리는 점 ② — LoRA를 적용하면 Model Size가 줄어드는가?

기본 LoRA 자체는 아니다.

엄밀히는 작은 LoRA Parameter가 추가되므로 Parameter Storage가 조금 증가한다.

---

# 87. 자주 헷갈리는 점 ③ — LoRA에서 무엇을 Freeze하는가?

Original Base Weight를 Freeze한다.

Train되는 것은 Low-rank Matrix `A`, `B`다.

---

# 88. 자주 헷갈리는 점 ④ — LoRA는 Weight Matrix 자체를 Low-rank로 바꾸는가?

강의의 핵심 구조에서는 Original `W`는 그대로 유지한다.

Low-rank인 것은 **추가 Update `ΔW`** 다.

```text
ΔW = BA
```

이다.

---

# 89. 자주 헷갈리는 점 ⑤ — Original Weight를 두 Matrix로 분해하는 것인가?

정확히는 LoRA에서 학습하는 **Update Matrix를 Low-rank Product로 표현**한다고 이해하는 것이 좋다.

```text
W' = W + BA
```

이다.

---

# 90. 자주 헷갈리는 점 ⑥ — Rank가 작으면 무조건 좋은가?

아니다.

Rank가 작으면 Parameter는 줄지만 Adaptation Capacity도 부족할 수 있다.

---

# 91. 자주 헷갈리는 점 ⑦ — Adapter와 LoRA는 같은가?

둘 다 PEFT지만 방식이 다르다.

Adapter:

```text
추가 Layer
```

LoRA:

```text
기존 Linear Transformation에
Low-rank Update 추가
```

다.

---

# 92. 자주 헷갈리는 점 ⑧ — Prompt Tuning과 LoRA의 차이

Prompt Tuning:

```text
Input / Embedding 쪽 Parameter만 학습
```

LoRA:

```text
Model 내부 Weight Transformation에
Low-rank Update 학습
```

한다.

따라서 일반적으로 LoRA가 Model 내부 Representation을 더 직접적으로 바꿀 수 있다.

---

# 93. 자주 헷갈리는 점 ⑨ — QLoRA = LoRA Parameter를 Quantize하는 것인가?

핵심은 아니다.

강의에서 설명하는 QLoRA의 중심 구조는

```text
Base Model
→ Quantize

LoRA
→ 높은 Precision으로 Train
```

이다.

---

# 94. 자주 헷갈리는 점 ⑩ — QLoRA에서는 Base Weight도 Training하는가?

아니다.

Quantized Base는 Freeze하고 LoRA Parameter만 학습한다.

---

# 95. 자주 헷갈리는 점 ⑪ — QLoRA는 단순 Quantization인가?

아니다.

Quantization에 LoRA Fine-tuning을 결합한 것이다.

---

# 96. 자주 헷갈리는 점 ⑫ — QLoRA는 LoRA보다 항상 Accuracy가 같다?

아니다.

Base Model을 Aggressive하게 Quantize했기 때문에 약간의 Accuracy/Quality Loss가 발생할 수 있다.

---

# 97. 자주 헷갈리는 점 ⑬ — QLoRA는 왜 Mixed Precision인가?

대부분의 Base Weight는 Low-bit인데 LoRA Parameter는 Higher Precision이기 때문이다.

---

# 98. 자주 헷갈리는 점 ⑭ — QLoRA이면 모든 연산도 4 bit인가?

아니다.

Low-bit Base와 Higher-precision LoRA가 함께 존재하고 필요한 내부 변환·연산이 있기 때문에 전체 Runtime이 순수 4-bit Arithmetic과 동일하다고 보면 안 된다.

---

# 99. 자주 헷갈리는 점 ⑮ — QLoRA의 가장 큰 장점은 속도인가?

강의의 중심은 특히

```text
Memory-efficient Fine-tuning
```

이다.

Runtime Speed-up은 Hardware와 구현에 따라 별도 문제다.

---

# 100. 자주 헷갈리는 점 ⑯ — Full Fine-tuning보다 LoRA가 Domain 특화를 못 하나?

반드시 그렇다고 할 수 없다.

강의에서는 매우 적은 Parameter만 추가하면서도 상당한 Specialization Performance를 얻을 수 있다는 점을 LoRA의 중요한 장점으로 설명한다.

---

# 101. 자주 헷갈리는 점 ⑰ — 하나의 LoRA만 Base Model에 붙일 수 있는가?

개념적으로는 Task/Domain별 LoRA를 따로 학습해 같은 Backbone에 교체하여 사용할 수 있다.

---

# 102. 자주 헷갈리는 점 ⑱ — PEFT는 Inference Compression이 핵심인가?

아니다.

핵심은 **Fine-tuning Process의 효율화**다.

---

# 103. 이번 강의에서 반드시 기억할 핵심

### 1.

LoRA는 엄밀히 Model Compression 기법이 아니라 Parameter-Efficient Fine-Tuning 기법이다.

### 2.

Model Compression의 목표가 Model Size·Inference Cost를 줄이는 것이라면 PEFT의 목표는 Fine-tuning에 필요한 Trainable Parameter와 Training Resource를 줄이는 것이다.

### 3.

Fine-tuning은 범용 Pretrained/Foundation Model을 특정 Task나 Domain에 특화하는 과정이다.

### 4.

Prompt/In-context 방식은 Parameter를 변경하지 않아 매우 가볍지만 Model 자체의 깊은 Domain Adaptation에는 한계가 있을 수 있다.

### 5.

Full Fine-tuning은 모든 Weight를 Update하므로 높은 Adaptation Capacity를 가지지만 Training Memory와 Compute Cost가 매우 크다.

### 6.

Full Fine-tuning에서 전체 Weight를 변경하면 기존 General Capability가 변하거나 약해질 수 있다.

### 7.

PEFT는 Base Model 전체를 Update하지 않고 일부 작은 Parameter만 학습하는 방향이다.

### 8.

Adapter Fine-tuning은 Base Weight를 Freeze하고 추가 Adapter Layer를 학습한다.

### 9.

Adapter는 Full Fine-tuning보다 효율적이지만 추가 Layer Parameter와 Model Structure 변경 부담이 있다.

### 10.

Prompt/Embedding Tuning은 매우 적은 Parameter만 학습할 수 있지만 Adaptation Capacity가 제한될 수 있다.

### 11.

LoRA는 이러한 PEFT 방식 사이에서 높은 Efficiency와 Adaptation Capacity를 함께 얻기 위한 대표 방법이다.

### 12.

LoRA는 Original Weight `W`를 Freeze한다.

### 13.

Fine-tuning에서 필요한 Weight Update `ΔW`를 Low-rank Matrix Product로 표현한다.

### 14.

개념적으로 `W' = W + BA`로 이해할 수 있다.

### 15.

`A`와 `B`의 중간 Dimension `r`이 LoRA Rank다.

### 16.

`r`을 Original Weight Dimension보다 훨씬 작게 두면 Trainable Parameter 수를 크게 줄일 수 있다.

### 17.

LoRA Forward는 개념적으로 `Wx + BAx`다.

### 18.

Original Model의 General Knowledge는 Frozen `W`에 유지되고 Task-specific Adaptation은 작은 `A/B`에 저장된다고 이해할 수 있다.

### 19.

LoRA는 Full Fine-tuning보다 훨씬 적은 Gradient와 Optimizer State만 필요하므로 Training Memory를 절약할 수 있다.

### 20.

Task마다 작은 LoRA Parameter만 별도로 저장하고 같은 Base Model을 재사용할 수 있다.

### 21.

LoRA는 Original Model의 Input/Output Dimension을 유지하면서 내부 Transformation에 Update를 추가한다.

### 22.

LoRA 자체는 Model Size를 줄이는 Compression이 아니다.

### 23.

엄밀하게는 Base Model에 LoRA Parameter가 추가되므로 Parameter Storage는 조금 증가한다.

### 24.

LoRA의 핵심 Efficiency Target은 Model Size가 아니라 Fine-tuning Cost다.

### 25.

QLoRA는 Quantization과 LoRA를 결합한다.

### 26.

QLoRA에서는 Base Model을 INT4 등 매우 낮은 Precision으로 Quantize할 수 있다.

### 27.

Quantized Base Weight는 Freeze한다.

### 28.

추가 LoRA Parameter는 FP16/BF16 등 상대적으로 높은 Precision에서 학습할 수 있다.

### 29.

대부분의 Parameter가 Low-bit Base에 존재하고 LoRA Parameter는 매우 적기 때문에 전체 Memory Footprint를 크게 줄일 수 있다.

### 30.

따라서 QLoRA는 Quantization으로 Base Weight Memory를 줄이고 LoRA로 Trainable Parameter를 줄이는 두 가지 Efficiency를 결합한다.

### 31.

LoRA가 주로 Fine-tuning Efficiency라면 QLoRA는 Fine-tuning Efficiency와 Memory Efficiency를 동시에 노린다.

### 32.

QLoRA에서 Fine-tuning Data는 일반 Domain Adaptation Data가 될 수도 있고 Quantized Model의 Capability를 보완하는 방향으로 구성될 수도 있다는 것이 강의의 설명이다.

### 33.

QLoRA는 Base Model이 Quantized되어 있기 때문에 Full-precision LoRA보다 일부 Quality Loss가 발생할 수 있다.

### 34.

따라서 QLoRA는 완벽한 Accuracy 유지가 아니라 작은 Quality Loss와 큰 Memory 절감 사이의 Trade-off를 선택한다.

### 35.

QLoRA에서는 Low-bit Base와 Higher-precision LoRA가 함께 존재하므로 Mixed Precision System으로 볼 수 있다.

### 36.

Mixed Precision 때문에 GPU Arithmetic이나 Kernel Handling이 순수하게 모든 Tensor를 Low-bit로 실행하는 경우보다 복잡할 수 있다.

### 37.

따라서 QLoRA의 Model Memory Reduction이 동일한 비율의 Runtime Speed-up을 의미하지는 않는다.

### 38.

LoRA의 주요 Hyperparameter로 Rank, Alpha, Target Module 등을 설정할 수 있다.

### 39.

Rank가 작으면 Trainable Parameter와 Memory가 줄지만 Adaptation Capacity도 제한될 수 있다.

### 40.

Rank가 커지면 더 많은 Adaptation Capacity를 얻을 수 있지만 Fine-tuning Cost도 증가한다.

### 41.

Target Module을 이용해 Transformer 내부에서 LoRA를 적용할 Weight Matrix를 선택할 수 있다.

### 42.

QLoRA는 앞에서 배운 Floating Point·Low Precision·Quantization과 이번 Fine-tuning/PEFT 개념이 만나는 대표 기술이다.

### 43.

실제 효율화에서는 LoRA와 QLoRA 중 어느 것이 절대적으로 더 좋다고 볼 수 없으며 Accuracy와 Memory Constraint에 따라 선택해야 한다.

### 44.

Model이 너무 커 GPU Memory에 올리기 어려운 상황에서는 QLoRA의 Memory Reduction이 특히 중요하다.

### 45.

충분한 Memory가 있고 최대 Accuracy가 더 중요하다면 Full-precision LoRA가 더 적합할 수 있다.

### 46.

궁극적으로 LoRA/QLoRA 역시 이전 경량화 기법과 마찬가지로 Accuracy와 Resource Efficiency 사이의 Trade-off를 고려해야 한다.

---

# 104. 시험에 가장 중요한 비교표

|구분|Full Fine-tuning|LoRA|QLoRA|
|---|---|---|---|
|Base Weight|Update|Freeze|Quantize + Freeze|
|학습 대상|전체 Parameter|Low-rank A/B|Low-rank A/B|
|Base Precision|원래 Precision|원래 Precision|Low-bit|
|Training Memory|매우 큼|작음|더 작게 가능|
|Model Compression|X|X|Base Quantization 효과 O|
|Domain Adaptation|O|O|O|
|Accuracy|가장 유리할 수 있음|높게 유지 가능|일부 손실 가능|
|핵심|전체 재학습|PEFT|Quantization + PEFT|

---

# 105. Adapter vs Prompt Tuning vs LoRA

|방식|Trainable 대상|장점|한계|
|---|---|---|---|
|Adapter|추가 Layer|Adaptation 강함|추가 Parameter/구조|
|Prompt Tuning|Prompt/Embedding|매우 가벼움|표현력 제한|
|LoRA|Low-rank Weight Update|적은 Parameter + 강한 Adaptation|Rank/Target 선택 필요|

---

# 106. Quantization vs LoRA vs QLoRA

|기법|무엇을 효율화?|핵심|
|---|---|---|
|Quantization|Model Storage / Arithmetic|Bit-width 감소|
|LoRA|Fine-tuning|Low-rank Parameter만 학습|
|QLoRA|Fine-tuning + Memory|Quantized Base + LoRA|

---

# 107. 수식으로 최소한 이것만 기억

## Full Fine-tuning

```text
W → W + ΔW
```

`ΔW` 전체를 자유롭게 학습한다.

---

## LoRA

```text
ΔW = BA
```

이므로

```text
W' = W + BA
```

다.

Forward:

```text
y = Wx + BAx
```

그리고

```text
W = Frozen
A, B = Trainable
```

이다.

---

# 108. LoRA 수식을 외우지 않고 이해하기

원래 Fine-tuning:

```text
거대한 Weight Matrix 전체의
변화량을 학습해야 함
```

이다.

LoRA의 생각:

```text
특정 Task에 필요한 변화는
전체 방향을 다 쓰지 않을 수 있다.

↓

변화량만 작은 차원의
두 Matrix로 표현하자.

↓

학습 Parameter 급감
```

이다.

---

# 109. QLoRA를 한 장으로 보기

```text
                Pretrained Model
                       ↓
                  Quantization
                       ↓
                4-bit Base Model
                       ↓
                    Freeze
                       │
             ┌─────────┴─────────┐
             │                   │
         Base Path           LoRA Path
         Low-bit W           A → B
             │             High Precision
             │                   │
             └─────────+─────────┘
                       ↓
                     Output
```

Training:

```text
Base
→ Update X

LoRA
→ Update O
```

이다.

---

# 110. 이 강의를 문제 해결 흐름으로 보기

```text
[문제 1]

Foundation Model은 좋은데
특정 Domain에 조금 부족하다.
↓
Fine-tuning


[문제 2]

전체 Model을 Fine-tuning하면
너무 비싸다.
↓
PEFT


[문제 3]

Adapter를 추가하면
성능은 좋지만
추가 Layer 부담이 있다.


Prompt Tuning은
매우 싸지만
표현력이 부족하다.
↓
그 사이의 좋은 방법?
↓
LoRA


[LoRA]

Original W는 Freeze

Fine-tuning 변화 ΔW만

BA라는
Low-rank Matrix로 표현

↓

Trainable Parameter 급감


[문제 4]

LoRA는 Fine-tuning은 싸지만

거대한 Base Model 자체는
여전히 Memory를 많이 차지한다.
↓
Base Model도 줄이고 싶다.


Quantization
+
LoRA

↓

QLoRA


[QLoRA]

Base Model
→ 4 bit 등으로 Quantize
→ Freeze

LoRA
→ 높은 Precision에서 Train

↓

Base Memory ↓↓↓
Trainable Parameter ↓↓↓


하지만

Base Quantization
↓
표현력 일부 손실

↓

LoRA보다 Accuracy가
조금 떨어질 수도 있음.


결국

Accuracy
vs
Memory

Trade-off
```

---

# 111. 앞 강의 전체와 연결

```text
수 체계
↓
Bit-width가 Resource에 영향을 준다.

↓

Quantization
↓
Model의 Bit-width를 줄인다.

↓

Fine-tuning
↓
Large Model 전체를 다시 학습하면 비싸다.

↓

PEFT
↓
일부 Parameter만 학습한다.

↓

LoRA
↓
Low-rank Update만 학습한다.

↓

QLoRA
↓
Quantized Base
+
LoRA

↓

큰 Model도
상대적으로 작은 Memory로
Fine-tuning 가능
```

---

# 112. 이번 강의 핵심 한 문장

> **LoRA는 Model Compression 기법이 아니라 Parameter-Efficient Fine-Tuning 기법으로, Pretrained Model의 거대한 Original Weight `W`를 Freeze한 채 Fine-tuning에서 필요한 변화량 `ΔW`를 `BA`라는 작은 Low-rank Matrix Product로 표현하여 `W' = W + BA` 형태로 학습함으로써 Full Fine-tuning보다 훨씬 적은 Trainable Parameter·Gradient·Optimizer State만으로 특정 Task나 Domain에 Model을 특화한다. QLoRA는 여기에 Quantization을 결합해 Base Model 대부분을 4-bit와 같은 Low Precision으로 압축·Freeze하고 작은 LoRA Parameter만 높은 Precision으로 학습함으로써 Base Weight Memory와 Fine-tuning Resource를 동시에 줄이는 방식이며, 그 대신 Quantized Base의 표현력 손실과 Mixed Precision 처리로 인해 Full-precision LoRA보다 일부 Accuracy 감소나 Runtime Overhead가 발생할 수 있으므로 결국 Accuracy와 Memory Efficiency 사이의 Trade-off로 이해해야 한다.**

가장 짧게 기억하면:

```text
Full Fine-tuning
= 전체 Weight를 다시 학습


PEFT
= 일부만 학습


LoRA
= W는 Freeze

ΔW만
Low Rank로 학습

ΔW = BA

W' = W + BA


LoRA 목적
= Model Compression X
= Fine-tuning 효율화 O


QLoRA
= Quantization + LoRA

Base
→ 4 bit
→ Freeze

LoRA
→ 16 bit 등으로 Train


LoRA
= 학습은 싸지만
  Base Model은 큼

QLoRA
= Base도 작게 해서
  Memory까지 절약


LoRA vs QLoRA

LoRA
→ Accuracy 쪽 유리

QLoRA
→ Memory 쪽 유리


핵심:
LoRA = PEFT
QLoRA = Quantized Base + PEFT
```