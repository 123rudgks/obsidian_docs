# 31. 가장 단순한 학습 전략

이미

```text
Pre-trained Vision Encoder

Pre-trained LLM
```

이 있다고 하자.

둘 다 이미 매우 큰 Model이다.

처음부터 모두 다시 Training하면 비용이 매우 크다.

그래서 가장 먼저

```text
Vision Encoder → Frozen

LLM → Frozen

Projection Layer → Train
```

만 수행할 수 있다.

---

# 32. 적은 Parameter만 학습한다

Projection Layer가 단순한 Linear Layer라면 Parameter가 상대적으로 매우 적다.

따라서

```text
거대한 Vision Encoder와 LLM은 그대로 두고

작은 Connector만 Training
```

할 수 있다.

이것이 VLM을 효율적으로 만드는 중요한 Idea다.

---

# 33. 대표적인 초기 VLM — LLaVA

강의에서는 이러한 구조를 대표적으로 보여준 Model로 **LLaVA**를 설명한다.

LLaVA의 기본 구조:

```text
Pre-trained Vision Encoder
        ↓
Visual Features
        ↓
Projection Layer
        ↓
Large Language Model
```

이다.

---

# 34. 그런데 VLM은 Image만 설명하면 되는 것이 아니다

우리가 원하는 Model은 단순 Caption Model이 아니다.

```text
Image
+
Instruction
      ↓
Answer
```

를 수행해야 한다.

따라서 Training Data 역시

```text
Image
+
Instruction / Question
+
Answer
```

의 **Triplet** 형태가 필요하다.

---

# 35. Multimodal Instruction Tuning

즉 Text Foundation Model에서 배운 Instruction Tuning을 Multimodal로 확장한다.

Text-only:

```text
Instruction
+
Input
→ Answer
```

VLM:

```text
Image
+
Instruction
→ Answer
```

이다.

---

# 36. LLaVA의 1단계 학습

강의에서는 LLaVA 학습을 크게 단계적으로 설명한다.

첫 번째 단계에서는 이미 잘 학습된 두 Model을 가져온다.

```text
Pre-trained Vision Encoder
→ Frozen

Pre-trained LLM
→ Frozen
```

그리고 중간의 Projection Layer만 학습한다.

---

# 37. Stage 1의 목적

이 단계의 목적은

> **Vision Representation과 Language Representation의 연결 방법을 먼저 학습시키는 것**

이다.

즉 Vision Encoder의 말을 LLM에게 번역하는 Connector를 먼저 만든다.

---

# 38. 왜 이 단계가 빠른가?

Training 대상이 작은 Projection Layer뿐이기 때문이다.

```text
거대한 Vision Encoder Weight X

거대한 LLM Weight X

작은 Projection Weight O
```

이므로 Memory와 Compute 요구량이 상대적으로 적다.

---

# 39. Stage 2

Connector를 먼저 학습하고 나면 Vision Information이 어느 정도 LLM에 전달될 수 있다.

그다음 난이도를 높인다.

```text
Projection
+
LLM 일부/전체
```

를 Fine-tuning하여 실제 Multimodal Instruction을 더 잘 따르도록 한다.

---

# 40. Curriculum 관점

강의에서는 이를 일종의 **Curriculum Learning**처럼 설명한다.

```text
처음:
두 Model을 연결하는 법부터 학습

그다음:
Image를 보고 Instruction에
제대로 답하는 법 학습
```

이다.

---

# 41. 큰 LLM Fine-tuning의 Memory 문제

LLM까지 Update하면 Parameter 수가 급격히 늘어난다.

따라서 Memory 사용량이 커진다.

강의에서는 이 단계에서 낮은 Precision 등을 이용해 Memory 사용량을 절감하면서 큰 Model을 Fine-tuning하는 방법을 설명한다.

---

# 42. 그런데 Multimodal Instruction Data는 어디에서 얻는가?

LLaVA를 학습하려면

```text
Image
+
Question / Instruction
+
Answer
```

Data가 필요하다.

하지만 기존 Vision Dataset에는 이런 Data가 충분하지 않았다.

---

# 43. COCO Dataset

강의에서는 기존 **COCO Dataset**을 예로 든다.

COCO에는

- Image
    
- Image Caption
    
- Object Bounding Box
    

등이 있다.

즉 Image와 그 Image에 대한 Description은 존재한다.

---

# 44. 빠진 것 — User Question

VLM Training에 필요한

```text
Instruction / Question
```

이 없다.

따라서 이것을 새롭게 만들어야 한다.

---

# 45. Synthetic Instruction Data

강의에서 사용한 Idea:

```text
Image의 기존 Description / Answer
      ↓
LLM에 제공
      ↓
"이 Answer가 나오도록 하는
Question을 만들어 줘."
      ↓
Synthetic Question 생성
```

한다.

---

# 46. Answer에서 Question을 역으로 생성

일반적으로

```text
Question
→ Answer
```

을 만들지만 여기서는 이미 Answer 정보가 존재하므로

```text
Answer / Description
→ Question 생성
```

을 한다.

---

# 47. 결과

```text
Image
+
Synthetic Question
+
Existing Answer
```

Triplet을 만들 수 있다.

이것을 Multimodal Instruction Tuning Data로 사용한다.

---

# 48. Synthetic Data의 중요한 의미

사람이 수많은 Image를 다시 보고 Question과 Answer를 모두 작성하지 않아도 된다.

```text
기존 Labelled Vision Dataset
+
Powerful LLM
      ↓
Multimodal Instruction Dataset
```

으로 변환할 수 있다.

이는 앞 「텍스트 파운데이션 모델」에서 배운 **Synthetic Data Generation**과 직접 연결된다.

---

# 49. LLaVA의 전체 학습 구조

```text
[이미 준비된 Model]

Vision Foundation Model
+
LLM


[Stage 1]

Vision Encoder Frozen

LLM Frozen

Projection Layer만 Training
      ↓
Vision → Language 연결 학습


[Stage 2]

Image
+
Instruction
+
Answer
      ↓
Multimodal Instruction Tuning
      ↓
LLM까지 Fine-tuning
      ↓
Image를 보고 질문에
답할 수 있는 VLM
```

---

# 55. Qwen2-VL의 확장

강의에서는 이후 Qwen2-VL 계열이

- Multilingual Text
    
- 다양한 Image Resolution
    
- Multiple Images
    
- Video
    
- Agent 관련 기능
    

등을 더 잘 처리하는 방향으로 확장됐다고 설명한다.

---

# 56. 기존 ViT의 Resolution 제약

전통적인 ViT에서는

```text
고정 Patch 구조
+
Position Embedding
```

때문에 Training 때 사용한 Image Resolution과 다른 Resolution을 처리하는 것이 제한될 수 있다.

---

# 57. Native / Dynamic Resolution

최근 VLM에서는 다양한 크기의 Image를 입력받기 위해

```text
작은 Image

큰 Image

Web Page Screenshot
```

등을 반드시 하나의 고정 크기로 단순 Resize하지 않고 보다 유연하게 처리하려는 Architecture를 사용한다.

---

# 58. 여기에서 Position Encoding 문제가 생긴다

Transformer의 Self-Attention 자체에는 순서 정보가 자동으로 들어 있지 않는다.

즉 Position Information을 따로 주지 않으면

```text
Token 1
Token 2
Token 3
```

의 위치/순서를 명시적으로 알기 어렵다.

---

# 59. 기존 Positional Embedding

고전적인 방식은

```text
Token Embedding
+
Position Embedding
```

처럼 Position 정보를 더한다.

---

# 60. RoPE

강의에서는 이후 발전된 방식으로 **Rotary Positional Embedding(RoPE)** 을 설명한다.

RoPE에서는 단순히 Token Embedding에 Position Vector를 더하는 방식 대신 Attention의

```text
Query
Key
```

Representation에 Position에 따른 Rotation을 적용한다.

---

# 61. Position을 Attention 자체에 녹인다

개념적으로

```text
Position
↓
Q / K Rotation
↓
Q·K Attention Score
↓
Position Relation 반영
```

이다.

즉 두 Token 사이 Attention을 계산할 때 Position 관계가 반영된다.

---

# 62. Text에서는 1D 위치

Sentence:

```text
Token 1
Token 2
Token 3
...
```

는 기본적으로 한 개의 Sequence 축을 가진다.

따라서 Position Index 하나로 표현하기 쉽다.

---

# 63. Image에서는 2D

Image Patch는

```text
Width
Height
```

두 공간축이 필요하다.

단순 Text처럼 하나의 위치 Index만으로 보는 것보다 Spatial Structure를 표현할 필요가 있다.

---

# 64. Video에서는 3D 관계

Video는

```text
Width
Height
Time
```

가 모두 필요하다.

즉 Spatial Position뿐 아니라 Frame의 시간 정보도 존재한다.

---

# 65. Multimodal RoPE

강의에서는 Qwen 계열에서 **Multimodal Rotary Position Embedding(mRoPE)** 을 소개한다.

큰 Idea:

```text
Text
→ Sequence Position

Image
→ Height + Width

Video
→ Time + Height + Width
```

등 서로 다른 Position Axis를 Attention에 반영한다.

---

# 66. Multimodal Position Information

즉 Token이

```text
문장의 몇 번째인가?

Image의 어느 위치인가?

Video의 어느 Frame인가?
```

라는 정보를 함께 다룰 수 있도록 확장한다.

---

# 67. 여러 Video를 넣으면?

강의에서는 여기에서 흥미로운 질문을 던진다.

Video 하나라면

```text
Time
Height
Width
```

로 표현할 수 있다.

그런데 Video 자체가 여러 개라면

```text
어느 Video인가?
```

라는 추가 구분 정보가 필요할 수도 있다.

즉 Multimodal Position Design은 Input Structure가 복잡해질수록 더 어려워질 수 있다.

---

# 68. Qwen2.5-VL

강의에서는 Qwen2.5-VL 수준부터 일반 사용자가 Download해서 사용할 때도 상당히 좋은 성능을 보이는 Model로 설명한다.

대표 기능:

```text
Document Parsing

Document Understanding

Chart Understanding

Formula Understanding

Object Grounding

Object Detection

Long Video Understanding
```

등이다.

---

# 69. Document Understanding

VLM은 단순 자연 사진만 보는 Model이 아니다.

예:

```text
PDF Page

Table

Chart

Equation

Screenshot
```

같은 Visual Document도 분석해야 한다.

High-resolution / Dynamic-resolution 처리 기술이 이 영역에서 특히 중요하다.

---

