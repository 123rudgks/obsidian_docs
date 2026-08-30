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

