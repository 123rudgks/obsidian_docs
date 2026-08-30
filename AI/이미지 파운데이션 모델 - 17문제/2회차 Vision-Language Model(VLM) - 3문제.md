[강의 링크](https://www.youtube.com/watch?v=A3tEBq2WQ7U)

## 시험 범위에 포함되는 내용
| 시험 토픽                        | 강의 내용                                                                  |
| ---------------------------- | ---------------------------------------------------------------------- |
| **VLM 학습 절차**                | LLaVA Stage 1 → Stage 2의 단계적 VLM 학습                                    |
| **문서 이해 VLM**                | PDF, Table, Chart, Formula, Screenshot 등 Visual Document Understanding |
| **ViT Positional Embedding** | 기존 ViT Position Embedding의 Resolution 제약과 RoPE/mRoPE로의 확장              |
# 0. 강의 전체 개요

앞 1차시에서는

```text
Image
↓
Image Encoder

Text
↓
Text Encoder

두 Embedding을
같은 Semantic Space에 정렬
↓
CLIP
```

을 배웠다.

CLIP의 핵심은

```text
Matching Image–Text
→ 가깝게

Non-matching Image–Text
→ 멀게
```

만드는 **Multimodal Alignment**였다.

이번 차시는 여기에서 한 단계 더 나아간다.

```text
CLIP
      ↓
Contrastive Learning의 한계
      ↓
SigLIP
      ↓
Image와 Text의 Alignment
      ↓
그 Representation을
Language Model과 연결
      ↓
Vision-Language Model
      ↓
Image를 보고
질문에 자연어로 답변
      ↓
LLaVA
      ↓
더 복잡한 학습 Recipe
      ↓
Qwen-VL / InternVL
      ↓
Image뿐 아니라
Video / Audio / 3D / Action
      ↓
Multimodal / Omni / Physical AI
```

---

# 1. 왜 Vision-Language Model이 필요한가?

기존 Computer Vision에서는 Task별로 Model을 따로 만들었다.

예:

```text
Image Classification Model

Object Detection Model

Segmentation Model

OCR Model

Document Model
```

문제는 각 Model의 **Output이 미리 정해져 있다는 것**이다.

예를 들어 Image Classifier라면

```text
Dog
Cat
Car
...
```

처럼 정해진 Class만 출력한다.

Object Detector라면

```text
Class + Bounding Box
```

라는 정해진 구조를 출력한다.

---

# 2. 사람의 요구는 훨씬 다양하다

실제 User는 Image를 보고 단순히

```text
"이게 강아지인가?"
```

만 묻지 않는다.

예:

```text
이 Chart를 분석해 줘.

이 문서를 읽고 요약해 줘.

여기에서 이상한 부분을 찾아 줘.

왜 이런 결함이 생긴 것 같아?

이 화면에서 다음에 어디를 눌러야 해?

이 사진을 보고 나와 대화해 줘.
```

처럼 매우 다양한 요청을 한다.

---

# 3. 해결책 — Language Interface

Natural Language를 Interface로 사용하면 Output 형식에 대한 제한이 크게 줄어든다.

```text
Image
+
Natural Language Question
        ↓
Model
        ↓
Natural Language Answer
```

이 된다.

즉 Image를 보고 무엇을 해야 할지까지 Language로 자유롭게 지정할 수 있다.

이것이 **Vision-Language Model(VLM)** 의 중요한 출발점이다.

---

# 4. Vision 하나에서 Multimodal로

과거:

```text
Vision
→ Vision Model

Language
→ Language Model
```

최근:

```text
Vision
+
Language
      ↓
One Multimodal Model
```

이다.

강의에서는 Computer Vision이라는 분야 자체도 최근에는 Language가 결합되면서 Multimodal 방향으로 빠르게 확장되고 있다고 설명한다.

---

# 5. 먼저 CLIP의 개선형 — SigLIP

본격적인 VLM에 들어가기 전에 1차시 CLIP의 개선형으로 **SigLIP**을 설명한다.

SigLIP의 큰 Idea는 매우 단순하다.

```text
CLIP
→ Softmax-based Contrastive Loss

SigLIP
→ Sigmoid-based Loss
```

이다.

---

# 6. CLIP Softmax의 특징 다시 보기

CLIP에서는 한 Image를 기준으로 Batch 안의 여러 Text Candidate를 비교한다.

예:

```text
Dog Image

"a dog"       ← Positive
"an elephant" ← Negative
"a car"       ← Negative
"a building"  ← Negative
```

그리고 Similarity를 Softmax에 넣는다.

---

# 7. Softmax의 상대 경쟁 구조

Softmax에서는 Probability의 합이 1이어야 한다.

따라서 하나의 Score를 높이면 다른 Score는 상대적으로 낮아진다.

```text
Positive ↑
      ↓
Others ↓
```

라는 경쟁 구조가 발생한다.

---

# 8. 강의에서 지적하는 CLIP의 문제

Negative Pair가 이미 충분히 멀어진 경우를 생각해 보자.

```text
Dog ↔ Elephant
```

가 이미 매우 다른 Embedding이 되었다.

그런데 Softmax에서는 Positive를 상대적으로 높이기 위해 Negative들을 계속 낮추는 압력이 존재할 수 있다.

강의에서는 이를

> **이미 충분히 멀어진 Negative까지 계속 더 멀리 보내려고 할 수 있다.**

는 문제로 설명한다.

---

# 9. Negative Pair에 지나치게 집중할 수 있다

Loss를 줄이는 입장에서는

```text
Positive를 조금 더 가까이
```

하는 것보다

```text
많은 Negative를 더 멀리
```

보내는 것이 더 쉬운 방향이 될 수도 있다.

그 결과 강의에서는

```text
Negative Pair에 과도한 압력
+
Positive Alignment에 상대적으로 덜 집중
```

하는 문제가 생길 수 있다고 설명한다.

---

# 10. SigLIP의 해결책

SigLIP에서는 모든 Candidate를 하나의 Softmax Competition에 넣지 않는다.

각 Image–Text Pair를 보다 **독립적으로 판단**한다.

```text
Pair가 Positive인가?

Pair가 Negative인가?
```

를 각각 판단한다.

---

# 11. Positive / Negative Label

강의에서는 Pair마다

```text
Positive Pair
→ +1

Negative Pair
→ -1
```

과 같은 Sign을 주는 구조로 설명한다.

같은 Similarity `S_ij`를 사용하지만 Positive인지 Negative인지에 따라 원하는 방향이 달라진다.

---

# 12. Positive Pair

Matching Pair라면

```text
Similarity ↑
```

방향을 선호한다.

즉 Image와 Text가 Semantic Space에서 가까워지도록 한다.

---

# 13. Negative Pair

Non-matching Pair라면

```text
Similarity ↓
```

방향을 선호한다.

즉 서로 멀어지도록 한다.

---

# 14. Sigmoid Saturation이 중요하다

Sigmoid는 어느 정도 큰 양수 또는 음수 영역으로 이동하면 점점 포화된다.

```text
충분히 Positive
→ Gradient ↓

충분히 Negative
→ Gradient ↓
```

하게 된다.

강의에서는 이것을

> **이미 충분히 잘 분리된 Pair는 더 이상 크게 신경 쓰지 않는다.**

는 직관으로 설명한다.

---

# 15. CLIP과 SigLIP 차이의 핵심

### CLIP

```text
한 Candidate를 높이면
다른 Candidate들이 상대적으로 낮아지는
Softmax Competition
```

### SigLIP

```text
각 Image–Text Pair를
독립적으로 Positive / Negative 판단
```

이다.

---

# 16. 왜 SigLIP이 VLM에 많이 활용됐는가?

강의에서는 SigLIP이 이러한 Loss 변경을 통해 전반적으로 좋은 성능을 보였고 이후 VLM의 Vision Encoder로 널리 사용되었다고 설명한다.

다만 최근에는 Vision Encoder와 Language Model 등을 더 통합적으로 함께 학습하는 방향으로도 발전하고 있다고 덧붙인다.

---

# 17. CLIP과 SigLIP의 본질 — Alignment

CLIP과 SigLIP에서 더 중요한 개념은 Loss 함수 이름 자체가 아니다.

핵심은 **Multimodal Alignment**다.

```text
Image Modality
↓
Image Embedding

Text Modality
↓
Text Embedding

        ↓

같은 Semantic Space
```

에 올려놓는다.

---

# 18. Multimodal Alignment

서로 다른 Modality에서 같은 Concept을 나타내는 Data가 비슷한 Representation을 갖도록 만드는 것이다.

예:

```text
사자 Image

"사자"라는 Text

사자 울음소리
```

는 Input 형태는 전혀 다르다.

하지만 모두

```text
Lion
```

이라는 하나의 Concept을 표현한다.

---

# 19. Common Concept Space

강의에서는 사람도

- 사자 사진을 보고
    
- “사자”라는 말을 듣고
    
- 사자의 울음소리를 들어도
    

공통된 Concept을 떠올리는 것처럼 설명한다.

Multimodal Alignment 역시

```text
서로 다른 Sensor / Modality Input
        ↓
공통 Semantic Concept
```

으로 Mapping하려는 것이다.

---

# 20. Alignment의 장점

Image와 Text가 같은 Space에 올라오면

```text
Image ↔ Text Similarity
```

를 직접 계산할 수 있다.

즉

```text
"이 Image는
이 Text와 얼마나 관련되어 있는가?"
```

를 Vector Similarity로 비교할 수 있다.

---

# 21. ImageBind

이 Idea는 Image–Text 두 Modality에서 끝나지 않는다.

강의에서는 Meta의 **ImageBind**를 소개한다.

목표는 여러 Modality를 하나의 Shared Space로 연결하는 것이다.

예:

```text
Image

Text

Audio

Depth

기타 Sensor Modality
        ↓
Shared Embedding Space
```

---

# 22. 모든 Modality를 한 번에 맞추기는 어렵다

문제는 각 Modality가 가지고 있는 정보가 다르다는 것이다.

예를 들어 Image와 Text는 상대적으로 의미를 설명하기 쉽다.

하지만

```text
환경음

Sensor Waveform
```

은 Text로 완벽하게 설명하기 어려울 수 있다.

---

# 23. Modality Gap

즉

```text
Image ↔ Text
```

사이의 Gap과

```text
Sensor ↔ Text
```

사이의 Gap이 동일하지 않다.

강의에서는 이를 서로 다른 Modality 사이의 **정보 Gap**으로 설명한다.

---

# 24. ImageBind의 직관적 해결법

이미 비교적 잘 Alignment된 Space가 있다면 이를 Anchor처럼 활용한다.

```text
Image ↔ Text
이미 잘 Alignment

      ↓

Audio 추가

Depth 추가

Sensor 추가
```

처럼 하나씩 기존 Shared Space에 연결한다.

---

# 25. VLM의 Vision Encoder

현재까지 배운

```text
CLIP
SigLIP
```

같은 Model은 VLM에서 **눈(Eye)** 역할을 할 수 있다.

즉

```text
Image
↓
CLIP / SigLIP Vision Encoder
↓
Visual Representation
```

을 얻는다.

---

# 26. 그런데 눈만 있다고 대화할 수 있는 것은 아니다

CLIP은 Image와 Text의 의미 관계를 비교할 수 있지만

```text
Image를 보고
자유롭게 긴 Answer 생성
```

하는 Language Generator는 아니다.

그래서 강력한 LLM과 결합해야 한다.

---

# 27. VLM의 기본 구조

가장 기본적인 VLM은 다음처럼 생각할 수 있다.

```text
Image
↓
Vision Encoder
↓
Visual Features
↓
Projection / Connector
↓
LLM이 이해할 수 있는 Representation
↓
Large Language Model
↓
Natural Language Response
```

---

# 28. 왜 Projection Layer가 필요한가?

Vision Encoder Output:

```text
Visual Feature Space
```

LLM Input:

```text
Language Token Embedding Space
```

는 서로 다를 수 있다.

그냥 Vision Feature를 LLM에 바로 넣으면 LLM은 그것이 무엇을 의미하는지 알지 못한다.

---

# 29. Projection = 번역기

따라서 중간에

```text
Visual Feature
      ↓
Projection Layer
      ↓
LLM-compatible Feature
```

를 둔다.

강의에서는 이 Projection Layer를

> **Vision Encoder가 사용하는 표현을 Language Model이 이해할 수 있는 표현으로 번역하는 번역기**

처럼 설명한다.

---

# 30. Visual Feature를 Soft Prompt처럼 사용

Projection된 Image Feature는 LLM 입장에서 일종의 **추가 Token / Soft Prompt**처럼 취급할 수 있다.

개념적으로

```text
[Visual Token]
[Visual Token]
[Visual Token]

"이 사진에서 무엇이 보이나요?"
```

가 하나의 LLM Input Context가 되는 것이다.

---

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

# 50. 이제 더 발전된 Open VLM — Qwen-VL

강의에서는 LLaVA 이후 대표적인 공개형 VLM 흐름으로 Alibaba의 **Qwen-VL 계열**을 소개한다.

큰 그림은 여전히 비슷하다.

```text
Vision
+
Language Model
```

을 연결한다.

하지만 Training Data와 학습 Recipe가 훨씬 복잡해진다.

---

# 51. Qwen-VL이 다루는 Task

강의에서는 다음과 같은 기능을 예로 든다.

```text
Image Understanding

OCR / Text Reading

Multilingual Text

Grounding

Multiple Images

기타 Multimodal Task
```

즉 단순 Image 하나에 대한 Caption만을 학습하지 않는다.

---

# 52. “Model보다 Recipe도 중요하다”

VLM이 발전하면서

```text
어떤 Architecture인가?
```

뿐 아니라

```text
어떤 순서로
무슨 Data를
어떤 Resolution으로
어느 Component까지
Training하는가?
```

가 매우 중요해진다.

강의에서는 이를 요리의 **Recipe**에 비유한다.

---

# 53. Curriculum / Stage Training

예를 들어 Qwen-VL 계열에서는 강의상

```text
Low-resolution
↓
High-resolution

일부 Component Training
↓
더 많은 Component Training

Pre-training
↓
Multi-task
↓
Fine-tuning
```

처럼 여러 Stage로 Training한다.

---

# 54. 왜 처음부터 High-resolution을 전부 사용하지 않는가?

Image Resolution이 증가하면 Visual Token 수도 증가한다.

따라서 Compute가 크게 증가한다.

먼저 낮은 Resolution으로 빠르게 기본 Alignment와 Representation을 학습하고 점차 난이도를 높이는 방식이 효율적일 수 있다.

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

# 70. Vision-Language에서 Omni로

이후 강의에서는 **Qwen2.5-Omni**를 소개한다.

`Omni`는 VLM보다 Modality 범위를 더 확장한 Model로 설명된다.

---

# 71. VLM

기본:

```text
See
+
Read / Understand Language
```

---

# 72. Omni Model

강의에서 말하는 Omni의 범위는 대략

```text
읽기

쓰기

보기

듣기

말하기
```

까지 확장된다.

즉 Vision + Language뿐 아니라 Audio까지 Input/Output으로 사용한다.

---

# 73. Audio Encoder

Omni Model에는

```text
Vision Encoder
+
Audio Encoder
+
Language Model
```

같은 구조가 들어갈 수 있다.

즉 AI에게

```text
눈
+
귀
+
언어적 사고
```

를 함께 제공한다.

---

# 74. Thinker와 Talker

강의에서는 Qwen Omni 계열 구조를 설명하면서

### Thinker

```text
상황 이해
+
언어적 사고
+
무엇을 말할지 결정
```

### Talker

```text
생성된 내용을
실제 Speech Output으로 변환
```

하는 식으로 역할을 나눈 구조를 소개한다.

---

# 75. 실시간 대화 구조의 직관

개념적으로

```text
Input Audio / Image
      ↓
Thinker
      ↓
Reasoning
      ↓
Talker
      ↓
Speech

Talker가 말하는 동안에도
Thinker는 다음 내용을 처리
```

하는 형태로 이해할 수 있다.

---

# 76. Qwen3 계열에서의 추가 확장

강의에서는 이후 Model에서

- Chart / Graph
    
- Coding
    
- Drawing
    
- 3D Detection
    
- 3D Grounding
    

등 기능이 더 확장되는 흐름을 설명한다.

정확한 Version별 세부 Feature를 외우기보다

> **VLM이 Image Question Answering에서 점차 Document·Video·Agent·3D까지 확장되고 있다.**

는 흐름이 중요하다.

---

# 77. InternVL

강의에서는 Qwen 외 다른 Open VLM 사례로 **InternVL**도 소개한다.

기본 구조 자체는 크게 다르지 않다.

```text
Vision Encoder
+
Connector
+
LLM
```

이다.

차이는

- Model Scale
    
- Training Recipe
    
- Training Stage
    
- Data
    
- Resolution 처리 방식
    

등에 있다.

---

# 78. Vision Encoder와 LLM Scale을 함께 키운다

강의에서는 InternVL의 특징 중 하나로 Language Model이 커질 때 Vision Model Scale도 함께 증가시키는 방향의 Recipe를 소개한다.

즉

```text
Huge LLM
+
너무 작은 Vision Encoder
```

보다는

```text
Language Capacity ↑
Vision Capacity ↑
```

를 함께 고려한다.

---

# 79. VLM의 현재 한계 — 전체 장면은 잘 보지만 위치는 약하다

VLM은

```text
이 장면이 무엇인가?

이 Image가 무엇을 의미하는가?
```

같은 **Global Semantic Understanding**에는 강하다.

하지만

```text
정확히 어느 Pixel인가?

어느 작은 Button인가?

왼쪽에서 세 번째 Object가 무엇인가?
```

같은 **Fine-grained Spatial Perception**은 어려울 수 있다.

---

# 80. GUI Agent에서 이 문제가 중요하다

Computer-use Agent를 생각해 보자.

Agent가

```text
Browser Screenshot
↓
"로그인 버튼을 눌러라."
```

라는 Task를 수행하려면

1. Login Button이 무엇인지 이해하고
    
2. 정확한 위치를 찾고
    
3. 그 Coordinate를 Click
    

해야 한다.

---

# 81. 한 Step의 Error가 전체 Task를 망친다

여러 Click이 연속으로 필요한 Task라면 각 Step의 성공률이 충분히 높아야 한다.

강의에서는 당시 VLM/Agent들이 Button Location 같은 세밀한 Spatial Perception에서 아직 낮은 성공률을 보이는 사례를 설명한다.

즉 Reasoning보다 **Perception 자체가 Bottleneck**이 될 수 있다.

---

# 82. 해결 Trick — Set-of-Mark

강의에서는 이런 Spatial 문제를 도와주는 단순하지만 효과적인 Trick으로 **Set-of-Mark**를 소개한다.

Image를 그대로 주지 않고 먼저 Object들을 찾아 표시한다.

예:

```text
[1] Button
[2] Laptop
[3] Cup
[4] Window
```

처럼 번호나 Mark를 Overlay한다.

---

# 83. Segmentation / Detection Model을 먼저 사용

Pipeline:

```text
Original Image
      ↓
Detection / Segmentation Foundation Model
      ↓
Object별 Mark / Number Overlay
      ↓
Annotated Image
      ↓
VLM
```

이다.

---

# 84. 왜 이것이 도움이 되는가?

Original Image에서는 VLM이

```text
"어디를 봐야 하지?"
```

부터 판단해야 한다.

하지만 Object에 Mark가 붙으면

```text
"11번 Object 왼쪽에는 무엇이 있는가?"
```

처럼 Language와 Visual Location을 명확하게 연결할 수 있다.

---

# 85. 사람에게 Annotation하는 것과 비슷하다

강의에서는 교사가 화면에

```text
여기!
```

라고 Pen으로 표시하면 사람이 이해하기 쉬워지는 것과 같은 Idea로 설명한다.

즉 Perception을 다른 Foundation Model로 보완한다.

---

# 86. 중요한 통찰 — LLM의 Reasoning만 강화하면 충분한가?

아니다.

VLM의 성능은

```text
Visual Perception
+
Language Understanding
+
Reasoning
```

세 요소가 모두 필요하다.

Reasoning Model이 아무리 강해도 Image에서 필요한 정보를 정확하게 읽지 못하면 올바르게 추론할 수 없다.

---

# 87. Perception Bottleneck

강의에서는 현재 VLM에서

```text
Reasoning 부족
```

뿐 아니라

```text
Vision Perception 자체의 부족
```

도 중요한 Bottleneck이라고 강조한다.

마찬가지로 Audio Model에서도 Audio Perception 능력 자체가 중요하다.

---

# 88. VLM의 Domain Specialization

범용 VLM을 특정 Domain에 추가로 Adapt할 수도 있다.

예:

```text
Medical VLM

Manufacturing VLM
```

이다.

---

# 89. 의료 VLM

Medical Image:

- X-ray
    
- CT
    
- MRI
    
- 기타 검사 Image
    

와 Medical Knowledge를 결합해

```text
Image
+
Question
↓
Medical VLM
↓
Domain-specific Answer
```

를 만들 수 있다.

---

# 90. Visual Annotation으로 질문 위치를 명확하게 한다

강의에서는 Image 위에 Arrow 등을 Overlay하고

```text
"여기 부분을 봐라."
```

라고 알려주는 방식도 설명한다.

즉 VLM이 어떤 위치를 봐야 하는지 명확하게 한다.

---

# 91. Mark 자체에도 Model Preference가 있을 수 있다

강의에서는 경험적인 활용 Tip으로 특정 VLM이

```text
Red Arrow

Circle
```

같은 표시를 다른 색이나 도형보다 더 잘 인식하는 경우가 있다고 설명한다.

이는 **강의에서 제시한 Model 사용상 경험적 특성**으로 이해한다.

---

# 92. 제조업에서도 활용

예:

```text
제품 Image
↓
어디에 결함이 있는가?

왜 이런 결함이 발생했는가?

정상/불량인가?
```

같은 Task다.

---

# 93. 제조 AI가 중요한 이유에 대한 강의의 관점

강의에서는 제조업은 여전히 사람이 직접 Vision Inspection하는 일이 많고

```text
사람이 찾기 어려운 Defect
```

도 존재하기 때문에 AI 적용 Market이 크다고 설명한다.

즉 Image Foundation Model의 중요한 실무 응용 분야로 제조업을 강조한다.

---

# 94. 2D Image만으로 실제 세계 전체를 이해할 수는 없다

우리는 실제로 **3D World**에서 살아간다.

Robot이 실제 환경과 Interaction하려면

```text
Object가 무엇인가?
```

뿐 아니라

```text
어디에 있는가?

얼마나 떨어져 있는가?

어떤 방향인가?

어떻게 잡아야 하는가?
```

도 알아야 한다.

그래서 VLM은 3D Understanding 방향으로도 확장된다.

---

# 95. Vision-Language-Action(VLA)

여기서 한 단계 더 나아가면 Output이 Text가 아니라 **Action**이 된다.

```text
Image / Video
+
Language Instruction
      ↓
Vision-Language-Action Model
      ↓
Robot Action
```

이다.

강의에서는 Robot Motor Signal이나 Motion Command 등을 Output하는 Model로 설명한다.

---

# 96. VLM에 Action Decoder를 붙인다

VLM:

```text
Vision
+
Language
↓
Text Response
```

VLA:

```text
Vision
+
Language
↓
Action Decoder
↓
Action
```

로 확장한다.

---

# 97. Action의 예

Output은 단순

```text
"컵을 집어라."
```

라는 Sentence가 아닐 수 있다.

실제 Robot이 실행할

```text
Position Change

Joint Movement

Motor Command
```

등으로 표현될 수 있다.

---

# 98. Physical AI

이러한 Model은 **Physical AI**와 직접 연결된다.

AI가 Physical World에서 동작하려면

```text
World Perception
↓
Reasoning / Planning
↓
Physical Action
```

을 모두 수행해야 한다.

---

# 99. Vision만으로 물체를 잡기 어려운 이유

Image에서 Object 위치를 볼 수 있다고 해서 바로 안전하게 잡을 수 있는 것은 아니다.

Robot Manipulation에는

- Weight
    
- Center of Mass
    
- Friction
    
- Material
    

같은 Physical Property도 중요하다.

---

# 100. Perception의 범위를 더 넓혀야 한다

즉 Physical AI가 이해해야 하는 것은

```text
Appearance
```

만이 아니다.

```text
Geometry

Weight

Friction

Material

Physical Interaction
```

까지 포함한다.

강의에서는 이런 Physical Understanding이 Robot AI에 중요하다고 설명한다.

---

# 101. CLIP을 Loss로 활용하는 심화 Idea

강의 중간에는 CLIP Alignment를 단순 Representation으로 사용하는 것에서 더 나아가

> **CLIP Similarity 자체를 Optimization Objective로 활용할 수 있다.**

는 심화 내용도 소개한다.

---

# 102. 일반적인 방식

Image → Text Model을 만들고 싶다면 보통

```text
Image
↓
Neural Network
↓
Text
```

가 되도록 Model Weight를 Training한다.

---

# 103. 다른 Idea — Input 자체를 최적화

이미 CLIP이 Image와 Text Alignment를 잘 알고 있다면 Model Weight를 바꾸지 않아도 된다.

예를 들어

```text
Target Image → Fixed

Text Input → Trainable
```

로 둘 수 있다.

---

# 104. CLIP Similarity를 Loss로 사용

```text
Target Image
↓
Image Encoder
↓
Image Embedding

Trainable Text
↓
Text Encoder
↓
Text Embedding

Similarity
```

를 계산한다.

그리고

```text
Similarity ↑
```

가 되도록 **Text Input 자체를 Backpropagation으로 Update**한다.

---

# 105. Model Weight가 아니라 Input을 최적화한다

보통 Training:

```text
Input Fixed
Model Parameter Update
```

여기서는:

```text
Foundation Model Fixed
Input Representation Update
```

이다.

---

# 106. 결과적으로 Image를 잘 설명하는 Text 방향으로 이동

Text Representation이 처음에는 Random하더라도

```text
CLIP Similarity ↑
```

방향으로 계속 Optimize하면 Image와 Semantic하게 맞는 Text Representation 방향으로 움직인다.

강의에서는 이를 Image Captioning 같은 응용과 연결한다.

---

# 107. 반대 방향도 가능

이번에는

```text
Text → Fixed

Image → Trainable
```

로 둘 수 있다.

그리고

```text
CLIP Similarity ↑
```

방향으로 Image를 Update하면 Text Description에 맞는 Image를 만드는 방향으로 사용할 수 있다.

---

# 108. CLIP-guided Generation

즉

```text
Text:
"A dancing Freddie Mercury"
      ↓
CLIP Guidance
      ↓
Image / 3D Representation Optimization
```

같은 구조가 가능하다.

---

# 109. 3D로도 확장 가능

3D Representation을 2D Image로 변환하는 **Differentiable Rendering** 과정이 있다고 하자.

```text
3D Representation
      ↓
Differentiable Renderer
      ↓
2D Image
      ↓
CLIP
      ↓
Text Similarity
```

를 계산한다.

---

# 110. Gradient를 3D까지 되돌린다

Renderer가 Differentiable하면 Gradient를

```text
CLIP Loss
↓
Rendered Image
↓
Renderer
↓
3D Parameter
```

까지 Backpropagate할 수 있다.

그러면 Text Description에 맞도록 3D Representation을 최적화할 수 있다.

---

# 111. Foundation Model Knowledge를 끌어낸다

핵심은 새로운 거대한 3D Dataset으로 Foundation Model을 처음부터 Training하는 것이 아니다.

이미 CLIP이나 Image Generation Foundation Model에 들어 있는 Semantic Knowledge를

```text
Loss / Guidance
```

로 사용한다.

즉 Foundation Model의 Knowledge를 다른 Modality로 **전이**한다는 Idea다.

---

# 112. SDS Loss

강의에서는 이와 연결되는 조금 더 발전된 방법으로 Image Generation / Diffusion Model의 Knowledge를 활용하는 **SDS Loss** 계열도 언급한다.

다만 강의에서는 세부 원리까지 깊이 다루지 않고

> **이미 학습된 Image Foundation Model의 Knowledge를 Loss로 활용해 3D 같은 새로운 Representation을 Optimize할 수 있다.**

는 Concept 수준으로 넘어간다.

---

# 113. CLIP의 영향력이 컸던 이유

CLIP은 단순한 Zero-shot Classifier가 아니다.

공통 Image–Text Semantic Space를 제공하므로

```text
Retrieval

Classification

Image Editing

Image Generation Guidance

3D Generation Guidance

Vision-Language Model Vision Encoder
```

등 다양한 응용의 기반으로 활용할 수 있었다고 강의에서는 설명한다.

---

# 114. VLM의 핵심 Architecture를 다시 정리

```text
                     ┌─────────────┐
Image ─────────────→ │Vision Encoder│
                     └──────┬──────┘
                            │
                     Visual Features
                            │
                     ┌──────▼──────┐
                     │  Projector   │
                     └──────┬──────┘
                            │
                   Language-compatible
                      Visual Tokens
                            │
Text Instruction ───────────┼─────────┐
                            │         │
                     ┌──────▼─────────▼┐
                     │       LLM       │
                     └────────┬────────┘
                              │
                       Natural Language
                           Response
```

---

# 115. VLM에서 각 Component 역할

### Vision Encoder

```text
Image
→ Visual Representation
```

### Projector / Connector

```text
Visual Representation
→ LLM-compatible Representation
```

### LLM

```text
Visual Context
+
Language Instruction
→ Understanding / Reasoning / Response
```

---

# 116. CLIP과 VLM을 혼동하지 않는다

### CLIP

```text
Image Encoder
+
Text Encoder
↓
Image/Text Similarity
```

주목적은 Alignment다.

### VLM

```text
Vision Encoder
+
LLM
↓
Image 이해
+
Natural Language Generation
```

이다.

---

# 117. CLIP은 VLM의 눈으로 사용할 수 있다

CLIP 전체가 VLM 자체라기보다

```text
CLIP Image Encoder
```

를 떼어내 Vision Encoder로 사용할 수 있다.

그 이유는 CLIP Image Feature가 이미 Language Semantic과 Alignment되어 있기 때문이다.

---

# 118. SigLIP 역시 같은 역할

SigLIP 역시 Image–Text Alignment를 잘 학습하므로 Vision Encoder로 사용될 수 있다.

강의에서는 여러 VLM에서 CLIP/SigLIP 기반 Vision Encoder를 활용하는 흐름을 소개한다.

---

# 119. 하지만 최근에는 단순히 기존 Encoder를 붙이는 데서 더 발전

초기:

```text
Pre-trained Vision Encoder
+
Pre-trained LLM
+
Connector
```

최근:

```text
특정 LLM에 맞게
Vision Encoder까지
함께 설계 / Training
```

하는 방향으로 발전한다고 강의에서는 설명한다.

즉 Multimodal Model 전체를 하나의 System으로 최적화한다.

---

# 120. LLaVA와 Qwen-VL의 큰 차이를 보는 방법

LLaVA의 핵심적 의미:

```text
잘 학습된 Vision Encoder
+
잘 학습된 LLM
+
작은 Projection
+
Synthetic Instruction Data
```

라는 단순한 Recipe만으로도 강력한 VLM을 만들 수 있음을 보여준 것.

Qwen-VL 계열의 발전:

```text
더 다양한 Data

더 많은 Modality

Multiple Images

Higher / Dynamic Resolution

Video

OCR / Document

Grounding

Agent

더 정교한 Training Curriculum
```

로 확장한다.

---

# 121. Architecture뿐 아니라 Data가 중요하다

VLM도 결국 어떤 Data를 보여줬는지가 매우 중요하다.

```text
Caption만 학습한 Model
```

과

```text
OCR
Document
Chart
Grounding
Multiple Image
Video
Reasoning
```

을 모두 학습한 Model은 가능한 Task 범위가 다르다.

---

# 122. Multimodal Foundation Model에서 Recipe가 중요한 이유

큰 Model을 무작정 한 번에 Training하는 것이 아니라

```text
어떤 Model을 Frozen할까?

어느 Component부터 학습할까?

어떤 Resolution부터 시작할까?

어떤 Dataset을 먼저 쓸까?

언제 LLM을 Fine-tuning할까?

언제 Vision Encoder도 Update할까?
```

를 설계해야 한다.

이 과정 자체가 Model Performance에 중요한 영향을 준다.

---

# 123. VLM의 발전 방향을 한 줄로 보면

```text
Image Encoder + LLM
        ↓
Image Question Answering
        ↓
OCR / Document / Grounding
        ↓
Multiple Images
        ↓
Video
        ↓
Audio
        ↓
3D
        ↓
Action
```

이다.

---

# 124. Multimodal Alignment와 VLM 관계

먼저

```text
Image와 Text의 의미를 연결
```

할 수 있어야 한다.

그다음

```text
그 Image Information을
Language Model의 Context로 전달
```

한다.

즉

```text
Alignment
↓
Interface
↓
Reasoning / Generation
```

의 흐름이다.

---

# 125. VLM의 강점

기존 Vision Model처럼 Task마다 별도 Output Head를 만들지 않아도 된다.

```text
Image
+
"무엇을 원하는지 Language로 설명"
      ↓
Same VLM
```

을 통해 여러 Task를 수행할 수 있다.

---

# 126. 예

하나의 VLM에 같은 Image를 주고도

```text
이 사진을 설명해 줘.

사람이 몇 명이야?

왼쪽 사람이 무엇을 하고 있어?

이 장면에서 위험한 부분을 찾아 줘.

이 사진을 한 문장으로 요약해 줘.

이 Image를 보고 다음 행동을 추천해 줘.
```

처럼 전혀 다른 Task를 수행할 수 있다.

---

# 127. 이것이 Foundation Model의 범용성과 연결된다

기존:

```text
Task마다
Head / Model 변경
```

VLM:

```text
Task 변경
↓
Natural Language Instruction 변경
```

이다.

---

# 128. 하지만 Unified Model = 모든 것을 완벽하게 잘한다는 뜻은 아니다

VLM이 많은 Task를 처리할 수 있다고 해서 각 Specialized Model보다 모든 Task에서 항상 뛰어난 것은 아니다.

특히 강의에서는 **Fine-grained Perception / Spatial Localization**이 여전히 중요한 약점으로 나타날 수 있음을 강조한다.

---

# 129. 따라서 Foundation Model끼리 조합할 수 있다

예:

```text
Segmentation Foundation Model
      ↓
Fine-grained Object Location 제공
      ↓
VLM
      ↓
Language Reasoning
```

한다.

즉 한 Foundation Model이 다른 Foundation Model의 약점을 보완한다.

---

# 130. Set-of-Mark의 의미도 이것이다

```text
Segmentation Model
= 정확한 Perception

VLM
= Language Understanding / Reasoning
```

을 조합한다.

하나의 Model이 모든 일을 혼자 하기보다 특화된 Foundation Model을 Tool처럼 사용할 수도 있다.

---

# 131. VLM과 Agent 연결

Computer Agent는

```text
Screen
↓
Perception
↓
현재 상태 이해
↓
무엇을 해야 하는지 Reasoning
↓
Button Click / Keyboard
```

한다.

따라서 VLM이 Screen을 이해할 수 있어야 한다.

---

# 132. GUI는 매우 범용적인 Interface

Legacy Software에 API가 없어도 사람은 Screen과 Mouse로 사용한다.

Agent 역시

```text
Screenshot
+
Mouse / Keyboard Action
```

을 사용할 수 있다면 기존 Software를 수정하지 않고 활용할 수 있다.

이 때문에 Vision Capability가 Agent에서도 중요하다.

---

# 133. VLM에서 VLA로

VLM은

```text
Vision
+
Language
→ Language
```

에 가깝다.

VLA는

```text
Vision
+
Language
→ Action
```

까지 확장한다.

---

# 134. Language가 공통 Interface 역할을 한다

User는 Robot에게 Motor Signal을 직접 입력하지 않는다.

```text
"저 컵을 집어서
테이블 위에 놓아 줘."
```

라고 말한다.

Model이 이를

```text
Language Intent
↓
Visual Understanding
↓
Planning
↓
Action Command
```

로 바꾼다.

---

# 135. Physical AI에서 필요한 것

단순 Vision Semantic만으로 부족하다.

```text
Object Identity

3D Geometry

Spatial Relationship

Material

Weight

Friction

Action Consequence
```

등 물리 세계에 대한 이해가 필요하다.

---

# 136. 이번 강의의 주요 모델 비교

|모델/개념|핵심 역할|
|---|---|
|CLIP|Image와 Text를 Shared Embedding Space에 Alignment|
|SigLIP|Softmax 대신 Sigmoid 기반으로 Pair를 보다 독립적으로 학습|
|ImageBind|여러 Modality를 공통 Embedding Space에 연결|
|LLaVA|Vision Encoder와 LLM을 Projection Layer로 연결한 대표 VLM|
|Qwen-VL 계열|Multilingual·OCR·Grounding·Video·Dynamic Resolution 등으로 확장|
|InternVL|다양한 Scale과 Training Recipe를 가진 Open VLM|
|Omni Model|Vision·Language에 Audio Input/Output까지 확장|
|VLA|Vision + Language를 Robot Action까지 연결|

---

# 137. CLIP vs SigLIP

|구분|CLIP|SigLIP|
|---|---|---|
|기본 목적|Image–Text Alignment|Image–Text Alignment|
|Similarity|사용|사용|
|기본 Loss 구조|Softmax Competition|Sigmoid 기반 Pairwise|
|Candidate 관계|서로 상대적으로 경쟁|Pair를 보다 독립적으로 처리|
|Negative|다른 Candidate와 경쟁하며 억제|충분히 분리되면 Gradient 영향 감소|
|활용|Zero-shot, Vision Encoder 등|VLM Vision Encoder 등|

---

# 138. CLIP vs VLM

|구분|CLIP|VLM|
|---|---|---|
|구성|Image Encoder + Text Encoder|Vision Encoder + LLM|
|핵심|Alignment|Understanding + Generation|
|Output|Embedding / Similarity|Natural Language|
|Question Answering|기본 구조 아님|가능|
|Image Caption|직접 Generator는 아님|가능|
|역할|Visual-Language Representation|Multimodal Assistant|

---

# 139. LLaVA의 2단계 학습

|단계|Vision Encoder|Projector|LLM|목적|
|---|---|---|---|---|
|Stage 1|Frozen|Train|Frozen|Vision→Language 연결|
|Stage 2|강의 Recipe에 따라|Train|Fine-tune|Multimodal Instruction Following|

핵심은

```text
먼저 서로의 언어를 연결
↓
그다음 실제 대화 능력을 학습
```

이다.

---

# 140. VLM 전체 Data 구조

기본 Training Sample은

```text
Image
+
Instruction / Question
+
Answer
```

이다.

즉 Text-only Instruction Tuning의

```text
Instruction
+
Answer
```

에 Image Context가 추가된다.

---

# 141. Multimodal RoPE를 기억하는 핵심

Text:

```text
1D Sequence Position
```

Image:

```text
2D Spatial Position
```

Video:

```text
3D
Time + Height + Width
```

이다.

즉 Modality가 복잡해질수록 Position Representation도 확장되어야 한다.

---

# 142. 자주 헷갈리는 점 ① — VLM은 Image Model인가 Language Model인가?

둘을 결합한 Multimodal Model이다.

대표 구조에서는

```text
Vision Encoder
+
LLM
```

을 사용한다.

---

# 143. 자주 헷갈리는 점 ② — CLIP 자체가 ChatGPT처럼 Image 질문에 긴 Answer를 생성하는가?

CLIP의 기본 역할은 Image/Text Embedding Alignment다.

자유로운 Natural Language Generation을 위해서는 LLM Decoder와 연결하는 구조가 필요하다.

---

# 144. 자주 헷갈리는 점 ③ — CLIP과 SigLIP은 목적이 완전히 다른가?

아니다.

둘 다 Image–Text Alignment가 목적이다.

주된 차이는 Contrastive Objective를 구현하는 Loss 구조다.

---

# 145. 자주 헷갈리는 점 ④ — SigLIP에서는 Negative를 멀리 보내지 않는가?

Negative Pair는 낮은 Similarity를 선호한다.

다만 강의의 핵심은 **이미 충분히 Negative한 Pair를 계속 무한히 밀어내는 영향이 감소하도록 Sigmoid Saturation이 작동한다**는 것이다.

---

# 146. 자주 헷갈리는 점 ⑤ — Multimodal Alignment란 두 Modality를 하나로 합쳐 버리는 것인가?

아니다.

각 Modality의 Encoder는 다를 수 있다.

핵심은

```text
같은 의미
→ 비슷한 Representation
```

이 되도록 Shared Semantic Space를 만드는 것이다.

---

# 147. 자주 헷갈리는 점 ⑥ — ImageBind는 Image와 Text만 연결하는가?

강의에서는 Image/Text뿐 아니라 Audio, Depth 등 여러 Modality를 공통 Space에 연결하려는 Model로 소개한다.

---

# 148. 자주 헷갈리는 점 ⑦ — Vision Encoder Output을 LLM에 바로 넣으면 안 되는가?

Representation Space가 다르므로 LLM이 이해하기 어렵다.

따라서 Projection / Connector를 통해 LLM-compatible Representation으로 변환한다.

---

# 149. 자주 헷갈리는 점 ⑧ — Projector는 거대한 Model인가?

LLaVA의 초기 구조에서는 매우 단순한 Linear Projection 같은 작은 Module로도 연결할 수 있다.

이 때문에 초기 Alignment Training을 효율적으로 수행할 수 있다.

---

# 150. 자주 헷갈리는 점 ⑨ — LLaVA Stage 1에서 LLM을 같이 학습하는가?

강의에서 설명한 기본 Recipe에서는 먼저 Vision Encoder와 LLM은 가져와 고정하고 중간 Projection을 학습한다.

이후 Stage에서 LLM까지 Fine-tuning한다.

---

# 151. 자주 헷갈리는 점 ⑩ — LLaVA용 Question은 전부 사람이 작성했는가?

아니다.

강의에서는 기존 COCO Description 등을 이용하여 LLM이 Question/Instruction을 생성하는 Synthetic Data 방식을 설명한다.

---

# 152. 자주 헷갈리는 점 ⑪ — Qwen-VL이 LLaVA와 원리가 완전히 다른가?

큰 구조는 Vision + Language 결합이라는 점에서 비슷하다.

중요한 발전은 더 다양한 Data, 더 많은 Training Stage, Resolution, Video, Grounding 등 **Training Recipe와 Capability 확장**에 있다.

---

# 153. 자주 헷갈리는 점 ⑫ — Transformer는 Token 위치를 자동으로 아는가?

강의에서는 Position Information이 별도로 없다면 순서 자체를 알지 못한다고 설명한다.

그래서 Positional Encoding이 필요하다.

---

# 154. 자주 헷갈리는 점 ⑬ — RoPE는 Position Vector를 Token에 단순히 더하는가?

강의에서 설명하는 RoPE는 Q/K Representation에 Position에 따른 Rotation을 적용하여 Attention 계산에 위치 관계를 반영한다.

---

# 155. 자주 헷갈리는 점 ⑭ — Multimodal RoPE도 위치축 하나만 사용하면 되는가?

Image와 Video는 Spatial/Temporal Axis가 있으므로 여러 Position Axis를 고려한다.

---

# 156. 자주 헷갈리는 점 ⑮ — VLM은 장면을 이해하니 Button 위치도 항상 잘 찾는가?

아니다.

Global Semantic Understanding과 Fine-grained Localization은 다른 능력이다.

강의에서는 정확한 Spatial Perception이 VLM의 중요한 Bottleneck 중 하나라고 설명한다.

---

# 157. 자주 헷갈리는 점 ⑯ — Set-of-Mark가 Model을 다시 Training하는 방법인가?

아니다.

Image를 Segmentation/Detection하여 Number나 Mark를 Overlay한 뒤 VLM에 전달하는 **Input 전처리 / Prompting Trick**에 가깝다.

---

# 158. 자주 헷갈리는 점 ⑰ — VLM 성능은 LLM의 Reasoning만 좋아지면 해결되는가?

아니다.

Visual Perception이 잘못되면 그 이후 Reasoning도 잘못된 Input을 바탕으로 수행된다.

---

# 159. 자주 헷갈리는 점 ⑱ — CLIP을 Loss로 쓸 때 CLIP 자체를 Training하는가?

강의의 심화 예에서는 CLIP은 고정한다.

대신

```text
Input Text

Image

3D Representation
```

등을 CLIP Similarity가 높아지는 방향으로 Optimize한다.

---

# 160. 자주 헷갈리는 점 ⑲ — Text→3D에는 반드시 대규모 3D Foundation Model을 새로 학습해야 하는가?

강의에서는 CLIP이나 Image Generation Foundation Model에 이미 들어 있는 Knowledge를 Guidance/Loss로 사용하여 3D를 Optimize하는 Design Pattern도 소개한다.

---

# 161. 자주 헷갈리는 점 ⑳ — VLA는 Text를 출력하는 VLM인가?

VLA는 최종 Output이 Robot의 Action Representation까지 확장된 Model이다.

---

# 162. 이번 강의에서 반드시 기억할 핵심

### 1. VLM은 Vision과 Language를 결합해 Image 기반의 다양한 Task를 Natural Language Interface 하나로 수행할 수 있게 하는 Model이다.

### 2. 기존 Vision Model은 Task별 Output이 제한되어 있었지만 Language Interface를 사용하면 훨씬 범용적인 Task 지정이 가능하다.

### 3. SigLIP은 CLIP의 Softmax 기반 Contrastive Objective를 Sigmoid 기반 Pairwise Objective로 바꾼 Model로 강의에서 소개된다.

### 4. CLIP Softmax에서는 Candidate들이 서로 경쟁하는 구조가 존재한다.

### 5. 강의에서는 이 경쟁 때문에 이미 충분히 멀어진 Negative까지 계속 억제할 수 있는 문제를 설명한다.

### 6. SigLIP에서는 Positive/Negative Pair를 보다 독립적으로 판단한다.

### 7. Sigmoid Saturation 때문에 충분히 잘 분리된 Pair의 Gradient 영향이 작아질 수 있다.

### 8. CLIP과 SigLIP의 공통 본질은 Multimodal Alignment다.

### 9. Multimodal Alignment는 서로 다른 Modality의 같은 의미를 Shared Embedding Space에 연결하는 것이다.

### 10. Alignment되면 서로 다른 Modality 사이의 Similarity를 직접 비교할 수 있다.

### 11. ImageBind는 Image, Text, Audio, Depth 등 여러 Modality를 공통 Space에 연결하려는 확장 사례다.

### 12. 모든 Modality의 정보가 동일하지 않아 Modality Gap이 존재할 수 있다.

### 13. 초기 VLM은 이미 Pre-trained된 Vision Encoder와 LLM을 연결하는 방식으로 만들 수 있다.

### 14. Vision Encoder는 Image를 Visual Feature로 변환한다.

### 15. LLM은 Language Understanding, Reasoning, Natural Language Generation을 담당한다.

### 16. 두 Model 사이에는 Projection / Connector가 필요하다.

### 17. Projection Layer는 Visual Feature를 LLM이 이해할 수 있는 Representation으로 변환한다.

### 18. Projection된 Visual Feature는 LLM Context의 Soft Visual Token처럼 사용할 수 있다.

### 19. LLaVA는 이러한 Vision Encoder + Projector + LLM 구조의 대표적인 VLM로 강의에서 소개된다.

### 20. LLaVA Stage 1에서는 이미 학습된 Vision Encoder와 LLM을 고정하고 중간 Projector를 먼저 학습한다.

### 21. Stage 1은 Visual Representation을 Language Model이 이해할 수 있게 연결하는 것이 목적이다.

### 22. Stage 2에서는 Multimodal Instruction Following을 위해 LLM까지 Fine-tuning한다.

### 23. VLM Instruction Data의 기본 형태는 Image + Instruction/Question + Answer다.

### 24. 기존 COCO Dataset에는 Image와 Caption 등이 있지만 VLM용 Question이 부족하다.

### 25. LLaVA에서는 기존 Description/Answer를 이용하여 LLM이 Question을 생성하는 Synthetic Data 방식을 활용했다고 강의에서 설명한다.

### 26. 즉 Text Foundation Model에서 배운 Synthetic Data 생성이 VLM Training에도 연결된다.

### 27. Qwen-VL 계열은 Vision-Language Model을 OCR, Multilingual, Grounding, Multiple Images 등으로 확장한 사례로 소개된다.

### 28. 최근 VLM에서는 Architecture뿐 아니라 Data 구성과 Training Stage를 포함하는 Training Recipe가 매우 중요하다.

### 29. Low-resolution에서 High-resolution으로 단계적으로 Training하는 Curriculum을 사용할 수 있다.

### 30. 최신 VLM은 다양한 Native/Dynamic Resolution Input을 처리하는 방향으로 발전한다.

### 31. Transformer는 Position Information을 별도로 제공하지 않으면 Token 순서를 알기 어렵기 때문에 Positional Encoding이 필요하다.

### 32. RoPE는 Query와 Key에 위치에 따른 Rotation을 적용하여 Attention에 Position을 반영한다.

### 33. Multimodal RoPE는 Text의 1D뿐 아니라 Image의 Spatial Axis와 Video의 Time Axis까지 고려한다.

### 34. Qwen2.5-VL 계열은 강의에서 Document, Chart, Formula, Grounding, Long Video 등을 처리하는 방향으로 설명된다.

### 35. VLM에서 Audio까지 확장하면 Omni Model의 방향으로 발전할 수 있다.

### 36. 강의에서는 Omni Architecture의 예로 Thinker와 Talker 역할을 나누는 구조를 소개한다.

### 37. Open VLM에는 Qwen 계열 외에도 InternVL 등이 있으며 Model의 차이는 Architecture뿐 아니라 Scale과 Training Recipe에도 있다.

### 38. VLM은 Global Scene Understanding에 강하지만 Fine-grained Spatial Localization에는 아직 약할 수 있다.

### 39. GUI Agent에서는 정확한 Button Location을 찾는 Spatial Perception이 중요한 Bottleneck이 될 수 있다.

### 40. Set-of-Mark는 Detection/Segmentation으로 Object를 미리 표시한 Image를 VLM에 제공해 Spatial Understanding을 보완하는 방법이다.

### 41. 이는 Specialized Vision Foundation Model과 VLM을 조합하는 방식으로 이해할 수 있다.

### 42. VLM은 의료나 제조 같은 Domain-specific Data로 Adapt할 수 있다.

### 43. Image에 Arrow/Circle 등 Visual Annotation을 넣어 질문 대상 영역을 명확하게 할 수도 있다.

### 44. 강의에서는 VLM이 특정 색이나 Mark를 더 잘 인식하는 경험적인 차이도 언급한다.

### 45. CLIP Similarity 자체를 Loss로 사용하여 Model이 아니라 Input Representation을 Optimize하는 응용도 가능하다.

### 46. Image를 고정하고 Text를 Optimize하면 Image를 설명하는 Text 방향으로 Representation을 찾을 수 있다.

### 47. Text를 고정하고 Image를 Optimize하면 Text와 Alignment되는 Image 방향으로 생성할 수 있다.

### 48. Differentiable Renderer를 사용하면 Gradient를 3D Representation까지 전달하여 Text-guided 3D Generation에 활용할 수 있다.

### 49. 이는 이미 학습된 Foundation Model 내부 Knowledge를 새로운 Modality에 Guidance로 활용하는 Design Pattern이다.

### 50. Vision-Language Model은 3D 이해를 거쳐 Vision-Language-Action Model로 확장될 수 있다.

### 51. VLA에서는 Output이 Text가 아니라 Robot Position, Joint Movement, Motor Command 같은 Action이 될 수 있다.

### 52. VLA는 Physical AI와 밀접하게 연결된다.

### 53. Physical AI는 Visual Appearance뿐 아니라 Weight, Center of Mass, Friction, Material 등 물리적 속성도 이해할 필요가 있다.

---

# 163. 이번 강의 전체 논리

```text
기존 Vision Model은
Task마다 Output이 정해져 있다.
        ↓
모든 상황을 처리하는
범용 Service를 만들기 어렵다.
        ↓
Natural Language를
Interface로 사용하자.
        ↓
Vision-Language Model


그전에
Image와 Language를
어떻게 연결하지?
        ↓
CLIP
        ↓
Image–Text Alignment
        ↓
그런데 Softmax Competition의
한계가 있을 수 있음
        ↓
SigLIP
        ↓
Pairwise Sigmoid Loss
        ↓
충분히 분리된 Pair는
영향 감소


핵심 개념은
Multimodal Alignment
        ↓
Image와 Text의 같은 Concept을
Shared Semantic Space에 Mapping
        ↓
이 Idea를 여러 Modality로 확장
        ↓
ImageBind


이제 Language Model에
눈을 붙이자.
        ↓
Pre-trained Vision Encoder
+
Pre-trained LLM
        ↓
하지만 Representation Space가 다름
        ↓
Projection Layer
        ↓
Visual Feature를
Language-compatible Token으로 변환
        ↓
LLM Context로 입력
        ↓
Vision-Language Model


대표 초기 Recipe
        ↓
LLaVA
        ↓

Stage 1
Vision Encoder Frozen
LLM Frozen
Projector Train
        ↓
Vision–Language 연결

Stage 2
Multimodal Instruction Data
        ↓
LLM Fine-tuning
        ↓
Image를 보고
Instruction에 답변


그런데 Instruction Data가 부족
        ↓
기존 COCO Caption / Answer
        ↓
LLM으로 Question 역생성
        ↓
Synthetic Multimodal Data


이후 VLM 발전
        ↓
Qwen-VL / InternVL
        ↓
더 많은 Task
더 높은 Resolution
Multiple Images
OCR
Grounding
Video
        ↓
Position Representation도 확장
        ↓
RoPE
        ↓
Multimodal RoPE
        ↓
Text + Spatial + Temporal Position


Vision + Language만으로 끝나지 않음
        ↓
Audio 추가
        ↓
Omni

3D Understanding 추가
        ↓
Physical World Understanding

Action Decoder 추가
        ↓
Vision-Language-Action
        ↓
Physical AI


하지만 현재 VLM도
완벽하지 않음
        ↓
Scene 전체는 잘 이해하지만
정확한 작은 위치는 약함
        ↓
GUI Agent에서 Bottleneck
        ↓
Detection / Segmentation으로
Object에 번호를 붙임
        ↓
Set-of-Mark
        ↓
Fine-grained Perception 보완


동시에 Foundation Model은
단순 Encoder로만 쓰지 않을 수 있음
        ↓
CLIP Similarity를 Loss로 사용
        ↓
Text / Image / 3D Input 자체를 Optimize
        ↓
Foundation Model의 Knowledge를
새로운 Modality로 추출
```

---

# 164. 이번 강의 핵심 한 문장

> **Vision-Language Model은 CLIP·SigLIP과 같은 Image–Text Alignment Model이 학습한 Visual Semantic Representation을 Projection Layer를 통해 Large Language Model의 입력 공간에 연결하여 Image와 Natural Language Instruction을 함께 이해하고 답변하도록 만든 Multimodal Foundation Model이며, LLaVA처럼 기존 Vision Encoder와 LLM을 연결하는 단순한 구조에서 출발해 Synthetic Instruction Data, 단계적 Fine-tuning, Dynamic Resolution, Multimodal Positional Encoding 등을 통해 Document·Video·Agent·3D까지 확장되고, 최종적으로는 Action Decoder를 연결하여 Physical World에서 행동하는 Vision-Language-Action Model로 발전하고 있다.**

가장 짧게 기억하면 다음과 같다.

```text
CLIP
= Image와 Text의 의미를 맞춘다.

SigLIP
= 그 Alignment Loss를
  Pairwise Sigmoid 방식으로 개선한다.

VLM
= Vision Encoder가 본 것을
  Projector가 번역해서
  LLM에게 전달한다.

LLaVA
= 먼저 Projector를 학습하고
  그다음 Multimodal Instruction Tuning.

Qwen-VL 계열
= Image에서
  Document·Video·Grounding까지 확장.

VLA
= 보고 말하는 데서 끝나지 않고
  실제 Action까지 출력한다.
```