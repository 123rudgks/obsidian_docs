[강의 링크](https://www.youtube.com/watch?v=lWu9f-mPpuE)

## 시험 범위에 포함되는 내용
| 시험 토픽                | 강의 내용                                                 |
| -------------------- | ----------------------------------------------------- |
| **Small VLM**        | 작은 LLM + 효율적 Vision Encoder, On-device 목적             |
| **Quantization의 특징** | Bit Precision 감소 → Memory/Compute 감소                  |
| **저비트 Quantization** | FP32/FP16 → INT8/4-bit                                |
| **배포 환경별 경량화 전략**    | Mobile/Edge/On-device, NPU, Quantization, Tokenizer 등 |
| **모델 경량화 Trade-off** | Performance ↔ Memory/Cost/Latency                     |
| **파운데이션 모델 서비스 개발**  | Model Routing, Model Serving, REST API                |
| **멀티모달 비디오 생성**      | Video + Audio 생성, Video Foundation Model              |
| **도메인 특화 AI**        | Language/Domain-specific Model 선택과 Routing            |
# 0. 강의 전체 개요

앞 차시까지는

```text
CLIP / SigLIP
      ↓
Multimodal Alignment
      ↓
Vision Encoder + LLM
      ↓
VLM
      ↓
LLaVA / Qwen-VL
```

의 흐름을 배웠다.

이번 차시는 여기서 두 방향으로 확장된다.

```text
[방향 1]
VLM을 실제 Service에서
어떻게 작게 만들고 배포할까?
      ↓
Small VLM
On-device AI
Quantization
Language-specific Tokenization
Model Routing
Model Serving


[방향 2]
VLM 외에는
어떤 Foundation Model들이 있을까?
      ↓
Segmentation
Detection / Grounding
Generation
Depth / 3D
Human Understanding
Video
World Model
Any-to-Any
```

즉 이번 강의는 하나의 Architecture를 깊게 파는 강의라기보다

> **“현재 Vision 분야에는 어떤 범용 Model들이 존재하고, 각각 어디에 활용할 수 있는가?”**

라는 **Foundation Model Tool Map**을 만드는 차시다.

---

# 1. 왜 Small VLM이 필요한가?

큰 VLM은 매우 강력하다.

하지만 문제가 있다.

```text
Parameter 수 ↑
      ↓
GPU Memory ↑
Compute ↑
Latency ↑
Deployment Cost ↑
```

강의에서는 수십 Billion Parameter 규모의 Open VLM을 일반 개인이 사용하거나 Mobile Device에 탑재하기는 어렵다고 설명한다.

---

# 2. 모든 Service가 최고 성능을 요구하는 것은 아니다

어떤 Application에서는

```text
최고 수준 Reasoning
```

보다

```text
작게 실행 가능
빠른 Response
낮은 Cost
Local Processing
```

이 더 중요할 수 있다.

예:

- Smartphone
    
- Personal PC
    
- Edge Device
    
- Embedded Device
    

따라서 등장한 것이 **Small VLM**이다.

---

# 3. Small VLM

Small VLM은 말 그대로 경량화된 Vision-Language Model이다.

큰 방향은

```text
Small LLM
      ↓
Visual Capability 추가
      ↓
Small VLM
```

이다.

주요 목적은

```text
Server-only AI
```

에서

```text
On-device AI
```

로 내려오는 것이다.

---

# 4. VLM이라는 용어는 다소 혼용된다

강의에서 중요한 용어상의 주의점이다.

`VLM`이라고 할 때

### CLIP 같은 구조

```text
Image Encoder
+
Text Encoder
```

도 Vision과 Language를 다루므로 VLM이라고 부를 수 있다.

반면 LLaVA 같은 구조는

```text
Vision Encoder
+
LLM
```

이다.

---

# 5. 그래서 다른 이름도 사용한다

LLaVA와 같은 생성 가능한 Multimodal Language Model을 보다 명확하게 부르기 위해

- MLLM: Multimodal Large Language Model
    
- Large Multimodal Model
    

등의 표현도 사용한다.

강의에서는 이 분야의 명칭이 아직 완전히 하나의 표준으로 고정된 것은 아니므로

> **용어보다 실제 Architecture를 보고 구분해야 한다.**

고 강조한다.

---

# 6. 구분할 때 가장 중요한 질문

어떤 Model이 `VLM`이라고 적혀 있다면

```text
Text Encoder만 있는가?

아니면
Autoregressive LLM Decoder가 있는가?

Text를 생성하는가?

Embedding Alignment Model인가?
```

를 확인해야 한다.

---

# 7. Small VLM의 대표적 설계

강의에서는 Hugging Face에서 공개한 **SmolVLM** 계열을 예로 든다.

구조적으로 보면

```text
Small Language Model
+
SigLIP 계열 Vision Encoder
```

를 결합한 형태다.

---

# 8. Small VLM의 가장 큰 장점 — Memory

강의에서 제시한 비교의 핵심은

```text
Large VLM
→ 십수 GB 이상의 GPU Memory

Small VLM
→ 훨씬 작은 Memory
```

를 사용한다는 점이다.

즉 절대적인 최고 성능보다는

> **“실제로 Consumer Hardware에서 실행할 수 있는가?”**

를 중요한 설계 목표로 삼는다.

---

# 9. 더 작은 VLM도 존재한다

강의에서는

```text
1B
4B
0.5B
```

정도의 경량 Model들도 소개한다.

0.5B 정도까지 내려가면 Mobile/Edge Device 활용 가능성이 더욱 커진다.

---

# 10. Quantization

Small Model을 더 작게 만들기 위해 **Quantization(양자화)** 을 사용할 수 있다.

기존 Parameter가 예를 들어

```text
FP32
FP16
```

같은 Floating-point Number를 사용한다면

이를

```text
INT8

또는

4-bit 표현
```

등으로 낮춘다.

---

# 11. Quantization의 핵심 Trade-off

```text
숫자 표현 정밀도 ↓
      ↓
Model Size ↓
Memory Usage ↓
Compute Cost ↓

하지만
      ↓
Accuracy / Output Quality가
일부 손실될 수 있음
```

이다.

강의에서는 이 세부 내용은 이후 Quantization 강의에서 다시 다룬다고 한다.

---

# 12. Small VLM은 범용성보다 특정 기능에 집중할 수 있다

매우 작은 Model에게

```text
세상의 모든 문제를 해결하라.
```

고 요구하는 것은 비현실적이다.

따라서 Small VLM은

- Captioning
    
- Question Answering
    
- Counting
    
- Pointing
    
- Detection
    

등 특정 Application 기능을 중심으로 최적화되는 경우가 많다고 설명한다.

---

# 13. 작은 Model이라도 가능한 기능

강의의 Demo에서는 작은 VLM도

```text
Image
↓
Caption

Image + Question
↓
Answer

Object Name
↓
Object 위치

Image
↓
Detection
```

등을 수행할 수 있다고 소개한다.

즉

> **Small = 아무것도 못함**

은 아니다.

---

# 14. 기능별 Head

일부 Small Model에서는

```text
Captioning Head
QA Head
Detection 관련 Interface
```

등 Task에 따라 별도의 API/Head를 사용할 수도 있다고 강의에서 설명한다.

---

# 15. On-device VLM

Small VLM이 중요한 가장 큰 이유 중 하나는 **On-device AI**다.

```text
User Data
↓
External Server 전송 X
↓
Device 내부 Model
↓
Local Inference
```

가 가능하다.

---

# 16. On-device의 장점

대표적으로

```text
Privacy

Network Dependency 감소

Latency 감소 가능

Server Cost 감소
```

등이 있다.

강의에서는 Smartphone 내부에서 Vision/Language Interaction을 수행하는 사례를 보여준다.

---

# 17. NPU

최근 Smartphone에는 CPU/GPU 외에도

**NPU(Neural Processing Unit)**

같은 AI 연산 전용 Hardware가 탑재된다.

이를 이용해

- LLM
    
- Image Processing
    
- Generative AI
    
- Inpainting
    
- Vision AI
    

등을 Device 안에서 처리하는 방향이 가능하다.

---

# 18. Small VLM의 핵심 설계 철학

정리하면

```text
최고 성능
vs
실행 가능성
```

의 Trade-off다.

```text
Large VLM

Performance ↑
하지만
Memory / Cost ↑


Small VLM

Performance 일부 ↓
하지만
Memory / Cost ↓
Deployment 가능성 ↑
```

이다.

---

# 19. 다음 문제 — 언어에 따라서도 Model 효율이 달라진다

VLM/LLM을 실제 Service에 배포할 때는 Parameter Size만 보면 안 된다.

강의에서는 **Tokenizer**도 중요하다고 강조한다.

---

# 20. 같은 Sentence라도 언어에 따라 Token 수가 다르다

LLM은 String을 그대로 읽지 않는다.

```text
Sentence
↓
Tokenizer
↓
Token Sequence
```

로 처리한다.

따라서 같은 의미의 Sentence라도

```text
English
Korean
German
Finnish
...
```

에 따라 Token 수가 달라질 수 있다.

---

# 21. 왜 이런 차이가 생기는가?

Tokenizer Vocabulary는 Training Corpus에서 자주 등장하는 Pattern을 중심으로 만들어진다.

대규모 Training Data가 영어 중심이라면

```text
English 표현
→ Vocabulary에 잘 등록

Minority Language 표현
→ 더 작은 단위로 분해
```

될 가능성이 높다.

---

# 22. Token Vocabulary의 Trade-off

Vocabulary Size는 무한정 크게 만들 수 없다.

따라서

```text
Vocabulary에
많은 언어 표현을 저장
```

하면 Vocabulary가 커지고,

저장하지 않으면 해당 언어가

```text
더 많은 Token
```

으로 분해된다.

---

# 23. 언어 자체의 문제가 아니다

강의에서는 이것을

> 한국어가 본질적으로 AI에 불리해서 생기는 문제가 아니라 **Tokenizer Design 및 Training Data Distribution의 차이**

라고 설명한다.

---

# 24. Token 수가 많아지면 무엇이 문제인가?

같은 의미를 표현하는 데 Token 수가 많으면

```text
Context Length 더 많이 소비

Inference Step 증가

Memory Usage 증가

Compute 증가

Latency 증가

API Cost 증가 가능
```

등 여러 영향이 발생한다.

---

# 25. API Cost와 연결

많은 LLM API는 Token 수를 기준으로 Cost를 계산한다.

따라서 동일한 의미를 표현했는데

```text
Language A → 10 Tokens
Language B → 20 Tokens
```

이라면 비용 차이가 생길 수 있다.

강의에서는 이 점을 한국어/영어 질문 Cost의 차이와 연결해서 설명한다.

---

# 26. 언어 특화 Model이 필요한 이유

그래서 특정 국가나 언어에 특화된 Model을 만들 때는

```text
Training Data

Tokenizer

Vocabulary

Language Representation
```

등을 해당 언어에 맞게 설계할 수 있다.

강의에서는 국내 기업들의 한국어 특화 Model 개발도 이러한 맥락에서 소개한다.

---

# 27. 범용 Model 하나가 항상 최고는 아니다

Task에 따라

```text
Global General Model
```

보다

```text
Language-specific Model

Domain-specific Model

Small Task-specific Model
```

이 더 효율적일 수 있다.

---

# 28. 그래서 Model Routing

Agent System에서는 하나의 가장 큰 Model만 계속 사용할 필요가 없다.

먼저 Orchestrator가 Task를 분석한다.

```text
User Task
↓
Planning
↓
Subtasks
↓
각 Subtask에 적절한 Model 선택
```

하는 방식이 가능하다.

---

# 29. Model Router

예:

```text
한국어 Task
→ Korean-specialized Model

간단한 Task
→ Small Model

강한 Reasoning 필요
→ Large Model

Vision Task
→ VLM

특정 Domain
→ Domain-specialized Model
```

처럼 Routing한다.

---

# 30. Model Routing의 목적

```text
Cost ↓
+
Latency ↓
+
Quality ↑
```

을 동시에 노린다.

강의에서는 OpenRouter 계열 Service 등을 이러한 Model Routing의 사례로 언급한다.

---

# 31. 다양한 Model이 존재하는 것 자체가 가치가 있다

“최고 성능 Model 하나만 있으면 되지 않는가?”라는 생각과 달리

```text
Task마다 요구사항이 다름
```

때문에 여러 Model이 존재할 이유가 있다.

---

# 32. Model Serving

Model을 직접 Download해서 Python Process 안에서 실행할 수도 있다.

하지만 Software Architecture 관점에서는 Model Server를 따로 두고

```text
Frontend / Application
      ↓
REST API
      ↓
Model Serving Server
      ↓
VLM / LLM
```

형태로 구성하는 것이 유리할 수 있다.

---

# 33. Serving을 분리하는 이유

- Application과 Model Lifecycle 분리
    
- Deployment 관리
    
- Scaling
    
- Resource 관리
    
- API Integration
    

등이 편해진다.

---

# 34. LMDeploy

강의에서는 대표적인 Open-source Model Deployment/Serving Tool의 예로 **LMDeploy**를 소개한다.

지원 Model을 설치한 뒤

```text
Model
↓
LMDeploy
↓
API Server
```

형태로 실행할 수 있다.

---

# 35. Application에서는 REST API만 호출

Model Server가 실행되고 있다면 다른 Software에서는

```text
HTTP Request
↓
Inference Server
↓
Response
```

형태로 사용할 수 있다.

즉 Model Implementation 세부사항을 Application에서 분리할 수 있다.

---

# 36. 이제 Small VLM에서 다른 Foundation Model로

앞 차시에서 VLM의 Visual Perception을 보완하기 위해

```text
Segmentation
Detection
```

Model을 VLM 앞단에 사용할 수 있다는 내용을 배웠다.

이번 강의에서는 이러한 **Vision Foundation Model 자체**를 더 넓게 소개한다.

---

# 37. Computer Vision Foundation Model

Computer Vision의 방대한 Data를 Pre-training해서 다양한 Vision Task에 활용할 수 있는 Model이다.

종류는 매우 다양하다.

```text
Segmentation

Detection

Tracking

3D

Human Understanding

Image Generation

Video Generation
```

등이다.

---

# 38. Segment Anything Model — SAM

대표적인 Segmentation Foundation Model이 Meta의 **SAM(Segment Anything Model)** 이다.

강의에서는 SAM이 매우 많은 고품질 Segmentation Mask Data를 기반으로 만들어진 대표적인 Vision Foundation Model이라고 설명한다.

---

# 39. Segmentation Data는 만들기 매우 비싸다

Classification Label은

```text
Dog
```

라고 하나만 붙이면 된다.

하지만 Segmentation은 Object Boundary를 Pixel Level로 정확하게 그려야 한다.

```text
Image
↓
Pixel-level Mask Annotation
```

이므로 Human Labeling Cost가 매우 높다.

---

# 40. SAM의 의미

그렇게 큰 비용을 투자해 거대한 Segmentation Data와 Model을 만들었고,

그 결과 사용자는 직접 새 Segmentation Model을 Training하지 않고도

```text
Point

Box

Prompt
```

등을 주어 다양한 Object를 Segment할 수 있다.

---

# 41. 이미지뿐 아니라 Video에서도 활용

강의에서는 Segmentation Foundation Model이 Video Object Tracking과 연결되는 사례도 소개한다.

```text
Object 지정
↓
Frame 변화
↓
계속 Object 추적
```

이 가능하다.

---

# 42. Detection Foundation Model

Segmentation뿐 아니라 **Object Detection / Grounding**에도 Foundation Model이 존재한다.

대표 사례로 강의에서는 **Grounding DINO** 계열을 소개한다.

---

# 43. 기존 Object Detector

기존 Detector가

```text
Person
Car
Dog
...
```

같이 Training 때 정해진 Class만 Detection했다면,

Grounding 계열은

```text
"running girl"

"panda"

특정 자연어 표현
```

을 Text로 입력하여 해당 Concept을 Image 안에서 찾을 수 있다.

---

# 44. Open-vocabulary Detection

즉

```text
Text Query
+
Image
↓
해당 Concept의 위치
```

를 찾는다.

CLIP의 Open Vocabulary가 Detection으로 확장된 것으로 생각할 수 있다.

---

# 45. SAM + Grounding Detector

두 Foundation Model을 연결할 수도 있다.

```text
Text
↓
Grounding Detector
↓
Bounding Box
↓
SAM
↓
정밀 Object Mask
```

형태다.

---

# 46. 여러 Foundation Model을 조합하는 능력

이번 강의의 중요한 실무적 메시지다.

> **좋은 Model 하나를 아는 것보다 어떤 Foundation Model들이 존재하는지 알고 필요할 때 조합하는 것이 개발자의 중요한 역량이 될 수 있다.**

---

# 47. Tracking Foundation Model 사례

강의에서는 Video에서 특정 Person/Object를

- 가려졌다가
    
- 다시 나타나도
    
- 재식별하여
    

계속 추적하는 연구 사례도 소개한다.

이러한 Foundation Model들은

- Surveillance
    
- Sports
    
- Robotics
    

등 긍정적인 Application에도 활용될 수 있지만 군사적/위험한 활용 가능성도 존재한다고 강의에서 언급한다.

---

# 48. 이제 생성 Foundation Model

지금까지는

```text
Image / Video
↓
Analysis
```

하는 Model이었다.

다음은

```text
Description
↓
New Image / Video
```

를 만드는 **Generative Foundation Model**이다.

---

# 49. 생성모델을 왜 “생성모델”이라고 하는가?

강의에서는 이를 **확률분포**에서 출발해 설명한다.

세상에 실제 Data를 만드는 미지의 Distribution을

`P_data(x)`

라고 생각한다.

---

# 50. Dataset은 Distribution 자체가 아니다

우리가 가진 Dataset은

```text
P_data(x)
↓
Sampling
↓
x₁, x₂, ..., xₙ
```

처럼 실제 Distribution에서 얻어진 유한한 Sample 집합이다.

---

# 51. 우리가 알고 싶은 것은 원래 Distribution

만약 실제

`P_data(x)`

를 정확히 안다면 여기에서 계속 Sampling해서 새로운 Realistic Data를 생성할 수 있다.

즉

```text
Distribution을 안다
      ↓
Sampling 가능
      ↓
새로운 Data 생성 가능
```

이다.

---

# 52. 하지만 P_data는 알 수 없다

우리는 실제 세계의 모든 생성 과정을 정확하게 알 수 없다.

따라서 목표를 바꾼다.

```text
P_data(x)
≈
P_model(x)
```

이 되도록 Model Distribution을 학습한다.

---

# 53. Generative Modeling의 핵심 목표

```text
Dataset Samples
↓
Learning
↓
P_model
↓
P_model ≈ P_data
```

로 만드는 것이다.

그리고

```text
x ~ P_model
```

로 Sampling하여 새로운 Data를 만든다.

---

# 54. 생성 품질은 Distribution Approximation Quality에 달려 있다

```text
P_model
```

이

```text
P_data
```

에 가까울수록 생성되는 Sample도 실제 Data와 더 비슷해진다.

반대로 Modeling Gap이 크면

- 비현실적인 Image
    
- 형태 오류
    
- Artifact
    

등이 발생할 수 있다.

---

# 55. Model Capacity도 한계가 있다

어떤 Neural Network Architecture를 사용하느냐에 따라 표현할 수 있는 Probability Distribution의 범위가 달라진다.

실제 Distribution이 Model Family가 표현할 수 있는 범위 밖에 있다면 완전히 일치시키기 어렵다.

강의에서는 이를 생성 Model과 Real Data 사이의 Gap 관점으로 설명한다.

---

# 56. 대표적인 성공적인 생성방법 — Diffusion Model

최근 Image Generation에서 대표적인 방법 중 하나가 **Diffusion Model**이다.

강의에서는

- DDPM
    
- DDIM
    
- 여러 Variant
    

등을 모두 큰 범주의 Diffusion Model로 소개한다.

---

# 57. Diffusion의 출발점 — Gaussian Noise

복잡한 실제 Image Distribution을 직접 Modeling하기는 어렵다.

대신 Sampling하기 쉬운

```text
Gaussian Distribution
```

을 이용한다.

Gaussian Noise는 Computer에서 쉽게 만들 수 있다.

---

# 58. Generation 방향

생성 과정에서는

```text
Gaussian Noise
      ↓
조금 Denoising
      ↓
조금 더 Denoising
      ↓
...
      ↓
Image
```

처럼 Noise를 단계적으로 제거한다.

---

# 59. Training 방향

우리는 실제 Image를 가지고 있으므로 반대 방향 Dataset을 쉽게 만들 수 있다.

```text
Clean Image
↓
Noise 조금 추가
↓
더 추가
↓
...
↓
Gaussian Noise에 가까워짐
```

Noise를 만드는 것은 쉽다.

따라서

> **Noise가 섞인 Data에서 원래 깨끗한 상태로 돌아가는 방법**

을 Neural Network에게 학습시킨다.

---

# 60. Forward와 Reverse Process

### Forward

```text
Image
→ Noise를 점점 추가
```

### Reverse

```text
Noise
→ Noise를 점점 제거
→ Image
```

실제 Model이 학습해야 하는 핵심은 Reverse Denoising Process다.

---

# 61. Diffusion의 문제 — 느리다

Noise에서 Image까지 한 Step에 바로 변환하지 않는다.

```text
x_T
→ x_(T-1)
→ ...
→ x_1
→ x_0
```

여러 Iteration이 필요하다.

따라서 Generation이 느릴 수 있다.

---

# 62. Pixel Space에서 직접 Diffusion하면 비싸다

고해상도 Image 자체를 계속 Denoising하면

```text
Spatial Resolution ↑
      ↓
Memory ↑
Compute ↑
```

문제가 커진다.

---

# 63. 해결 — Latent Diffusion Model(LDM)

Stable Diffusion에서 중요한 Idea로 강의에서는 **Latent Diffusion**을 설명한다.

먼저 Autoencoder를 학습한다.

```text
Image
↓
Encoder
↓
Latent Representation
↓
Decoder
↓
Image Reconstruction
```

---

# 64. Latent Space

Encoder가 Image를 더 압축된 Representation으로 변환한다.

이를 **Latent Representation**이라고 한다.

강의의 직관에서는

```text
원래 고해상도 Image
↓
더 작은 Spatial Representation
```

으로 압축한다고 본다.

---

# 65. Diffusion을 Pixel이 아니라 Latent에서 수행

기존:

```text
Pixel Image
↓
Noise
↓
Denoising
```

LDM:

```text
Image
↓
Encoder
↓
Latent
↓
Noise
↓
Denoising
↓
Clean Latent
↓
Decoder
↓
Image
```

이다.

---

# 66. 장점

압축된 Space에서 계산하므로

```text
Compute ↓
Memory ↓
Generation Efficiency ↑
```

를 얻을 수 있다.

---

# 67. Denoising Network

강의에서는 전통적인 Latent Diffusion Architecture의 Denoising Network로 **U-Net**을 설명한다.

```text
Noisy Latent
↓
U-Net
↓
조금 더 깨끗한 Latent
```

을 반복한다.

---

# 68. 그런데 그냥 Denoising하면 원하는 Image가 나오지 않는다

Gaussian Noise 자체는 Random하다.

따라서 그냥 Denoising하면 무엇이 생성될지 통제하기 어렵다.

우리가 원하는 것은

```text
"a cute dog"
```

라고 입력했을 때 Cute Dog가 나오는 것이다.

---

# 69. Conditional Generation

그래서 Denoising 과정에 **Condition**을 준다.

예:

```text
Text Prompt
↓
Text Encoder
↓
Text Feature
      ↓
Denoising Network에 Condition
```

을 제공한다.

이를 **Conditional Generation**이라고 한다.

---

# 70. Cross-Attention

강의에서는 Text Condition을 Denoising Network에 연결하는 핵심 Mechanism으로 **Cross-Attention**을 설명한다.

```text
Noisy Image/Latent Feature
        ↔
Text Condition
```

의 관계를 Attention으로 본다.

---

# 71. Self-Attention과 비교

### Self-Attention

```text
현재 Modality 내부 관계
```

를 본다.

### Cross-Attention

```text
현재 Image Feature
↔
외부 Text Condition
```

의 관계를 본다.

따라서 Text가 Denoising 방향을 유도한다.

---

# 72. 직관

```text
현재 Noise
      ↓
"어느 방향으로 깨끗하게 만들까?"
      ↓
Text:
"a cute dog"
      ↓
Dog Image 방향으로
Denoising
```

한다.

---

# 73. Guidance

Text Condition이 Denoising 방향을 얼마나 강하게 제어할 것인지를 조정할 수 있다.

강의에서는 이를 **Guidance**와 연결한다.

---

# 74. Guidance Scale

```text
Guidance Scale ↑
→ Prompt Condition 영향 ↑

Guidance Scale ↓
→ Condition 영향 ↓
```

처럼 이해한다.

강의에서는 세부 수식보다는

> **Text Prompt가 Generation 방향을 얼마나 강하게 밀어주는가**

라는 직관을 잡도록 한다.

---

# 75. Diffusion Generation 전체 구조

```text
Text Prompt
↓
Text Encoder
↓
Text Condition
       ↘

Gaussian Noise
↓
Noisy Latent
↓
Denoising Network
↔ Cross-Attention
↓
Cleaner Latent
↓
반복
↓
Final Latent
↓
Decoder
↓
Generated Image
```

---

# 76. 대표 이미지 생성 Model

강의에서는 대표적인 Image Generation Model로 여러 사례를 소개한다.

### Midjourney

예술적이고 높은 품질의 Image Generation으로 유명한 Closed Model 사례.

### Stable Diffusion

Open Weight/Open Ecosystem의 대표적인 Diffusion Model.

### FLUX

강의에서는 Stable Diffusion 이후 고품질 Image Generation Model의 사례로 소개한다.

---

# 77. Diffusion Architecture도 발전한다

초기 Stable Diffusion 계열은 U-Net 중심이었다.

최근에는 **Diffusion Transformer(DiT)** 계열처럼 Transformer Architecture를 Denoising Backbone으로 사용하는 방향도 발전했다고 설명한다.

---

# 78. Image Generation에서 Text Rendering도 중요하다

실제 광고/Poster/Application에서는 Image만 예쁜 것으로 끝나지 않는다.

```text
Logo
Copy
Sentence
Label
```

같은 Text를 Image 안에 정확하게 그려야 할 수 있다.

따라서 Model 간 Quality 비교에서 **Text Rendering Ability**도 중요한 요소로 언급된다.

---

# 79. 생성 Foundation Model은 단순 이미지 생성기만이 아니다

Image Generation Model은 이미

> **Image가 어떤 방식으로 구성되는지에 대한 광범위한 Knowledge**

를 학습했다고 볼 수 있다.

따라서 그 Knowledge를 다른 Task에 Fine-tuning하여 재사용할 수 있다.

이것이 생성 Model도 Foundation Model로 볼 수 있는 중요한 이유다.

---

# 80. 대표 사례 — ControlNet

기본 Text-to-Image Diffusion Model에 추가 Condition을 넣어 더 정밀하게 Generation을 제어한다.

예:

```text
Text
+
Sketch

Text
+
Edge

Text
+
Segmentation Map

Text
+
Human Pose
```

등이다.

---

# 81. 왜 ControlNet이 필요한가?

Text Prompt만으로는

```text
정확한 Pose

Object 위치

Line Structure

Layout
```

를 정밀하게 지정하기 어렵다.

그래서 Visual Condition을 추가한다.

---

# 82. Control Signal

예:

```text
Human Pose
↓
이 Pose를 유지하면서
새로운 사람 Image 생성
```

또는

```text
Edge Map
↓
같은 구조를 유지하면서
새로운 Style Image 생성
```

이 가능하다.

---

# 83. ControlNet의 핵심은 Fine-tuning/Adaptation

강의에서는 이를

> **이미 Image Generation 능력을 가진 Foundation Model을 추가 Condition에 반응하도록 Fine-tuning하여 용도를 확장한 사례**

로 설명한다.

---

# 84. Foundation Model Knowledge를 다른 Task로 전환

이제 매우 중요한 Idea가 나온다.

Image Generator는 Image Structure를 어느 정도 알고 있다.

그렇다면

```text
Viewpoint

Depth

3D

Pose
```

와 같은 다른 Vision 정보도 Fine-tuning을 통해 꺼낼 수 있지 않을까?

---

# 85. Novel View Generation

예를 들어 하나의 Image가 있을 때

```text
Camera Angle +5°
+10°
+15°
...
```

같이 다른 시점의 Image를 생성하도록 Model을 Adapt할 수 있다.

---

# 86. 여러 시점을 생성할 수 있다는 의미

한 Object를 360도로 돌린 View를 일관되게 생성할 수 있다면

> **Model이 Object의 3D Structure를 어느 정도 이해한다고 볼 수 있다.**

는 것이 강의의 직관이다.

실제로 여러 View를 이용하면 3D Reconstruction과 연결할 수 있다.

---

# 87. Depth Estimation으로도 재사용

Image Generation Model이 Image Structure를 잘 이해하고 있다면

```text
RGB Image
↓
Depth Map
```

Task로 Fine-tuning할 수도 있다.

---

# 88. Marigold

강의에서는 Diffusion Model을 Depth Estimation에 Adapt한 대표적인 연구 사례로 **Marigold**를 소개한다.

핵심 Idea:

```text
Pre-trained Diffusion Model
↓
Depth Task에 Fine-tuning
↓
High-quality Depth Prediction
```

이다.

---

# 89. Synthetic Data를 이용한 Fine-tuning

강의에서는 Marigold 사례에서도 실제 Real Depth Data만 사용하는 것이 아니라 Synthetic Data를 활용한 Fine-tuning을 언급한다.

즉

```text
Foundation Model
+
Synthetic Task Data
↓
New Task
```

라는 Pattern이다.

---

# 90. Image + Depth 동시 생성

강의에서는 Text Prompt로부터

```text
RGB Image
+
Depth Map
```

을 동시에 생성하는 2.5D Model 연구 사례도 소개한다.

Image Appearance뿐 아니라 Geometry까지 함께 표현하여 Physical Consistency를 높이려는 방향이다.

---

# 91. 왜 3D Understanding이 중요한가?

2D Image만 이해해서는 Robot이나 Autonomous System이 실제 공간에서 안전하게 행동하기 어렵다.

필요한 것은

```text
Object 위치
거리
Depth
Geometry
```

등이다.

---

# 92. 대표 활용 분야

- 3D Reconstruction
    
- Robot Vision
    
- Autonomous Driving
    
- Mapping
    
- AR/VR
    

등에서 3D Understanding이 중요하다.

---

# 93. 또 다른 Foundation Model — Human-centric Model

실제 AI Service에서는 인간과 관련된 Task가 매우 많다.

예:

- 운동 자세 분석
    
- 신체 Pose
    
- Human Segmentation
    
- 3D Body
    
- Surface Geometry
    

따라서 사람에 특화된 Foundation Model도 중요하다.

---

# 94. Sapiens

강의에서는 Meta에서 공개한 Human-centric Foundation Model 계열인 **Sapiens**를 소개한다.

지원할 수 있는 Task 예:

```text
2D Pose Estimation

Human Segmentation

3D Estimation

Surface Normal Estimation
```

등이다.

---

# 95. Foundation Model을 Repurpose한다

이번 차시에 반복해서 등장하는 중요한 Pattern이다.

```text
Foundation Model
      ↓
이미 대규모 Knowledge 보유
      ↓
Task-specific Fine-tuning
      ↓
전혀 다른 Application
```

이 가능하다.

---

# 96. 이제 Image에서 Video Foundation Model로

Image를 한 장 생성하는 것보다 Video Generation은 더 어렵다.

Video는

```text
Spatial Consistency
+
Temporal Consistency
+
Motion
+
Physics
```

를 동시에 고려해야 한다.

---

# 97. Sora 사례

강의에서는 높은 Realism을 가진 Video Generation Model의 사례로 **Sora**를 소개하며,

사람들이 이러한 결과를 보고

> **“이 정도 Video를 만들 수 있다면 Physical World의 Rule을 어느 정도 학습한 것 아닌가?”**

라는 질문을 하기 시작했다고 설명한다.

---

# 98. World Model

강의에서는

> **세상의 동작 원리와 상태 변화를 Simulation할 수 있는 Model**

을 **World Model**이라는 방향으로 설명한다.

```text
Current World State
↓
Action / Time
↓
Future World State
```

를 예측하거나 생성할 수 있다면 Physical Environment를 Modeling하고 있다고 볼 수 있다.

---

# 99. Video Generation과 World Model의 연결

높은 품질의 Video를 만들려면 단순 Pixel Texture뿐 아니라

- Object Motion
    
- Camera Motion
    
- Interaction
    
- 물리적 변화
    

등을 어느 정도 Modeling해야 한다.

그래서 Video Generator를 World Model의 기반으로 활용하려는 연구 방향이 생긴다.

---

# 100. Video Generation은 Compute Cost가 매우 높다

Video는 Image보다 Data Dimension이 훨씬 크다.

```text
Width
×
Height
×
Frames
```

를 처리해야 한다.

따라서 Generation 및 Service Cost가 매우 높을 수 있다고 강의에서는 설명한다.

---

# 101. Audio까지 함께 생성하는 Video Model

강의에서는 Video와 자연스럽게 Synchronization된 Audio까지 함께 생성하는 최신 Multimodal Video Model 사례도 소개한다.

즉

```text
Video
+
Speech
+
Sound Effect
+
Environment Sound
```

를 함께 만들 수 있는 방향이다.

---

# 102. 이 발전의 위험

Video와 Audio가 매우 현실적이 되면

```text
Deepfake

Fraud

False News

Impersonation
```

등의 위험도 증가한다.

강의에서는 생성된 Video를 실제 영상으로 오인할 가능성에 주의해야 한다고 언급한다.

---

# 103. Video Editing Model

Generation뿐 아니라 기존 Video를 수정할 수도 있다.

예:

```text
Original Video
↓
AI Editing
↓
Background 변경

Style 변경

Cinematic Scene 변환
```

등이다.

---

# 104. Generative AI의 콘텐츠 제작 활용

강의에서는

- Advertising
    
- Film
    
- Video
    
- Education
    
- Avatar
    
- Presentation
    

등 Content Production Cost를 낮추는 방향도 소개한다.

---

# 105. Speaking Avatar

한 장의 사진과

```text
Text Script
+
Voice Sample
+
Portrait Image
```

를 이용하여 말하는 사람의 Video를 생성할 수 있다.

강의에서는 HeyGen 같은 사례를 소개한다.

---

# 106. Learning Material Generation

다른 AI Tool들과 조합하면

```text
Study Material
↓
LLM
↓
Podcast Script
↓
Speech Generator
↓
Avatar Generator
↓
Educational Video
```

처럼 자신만의 Learning Content를 만들 수도 있다고 설명한다.

---

# 107. Open vs Closed Generation Models

Vision 분야에도

```text
Closed Model
```

과

```text
Open Weight / Open-source Model
```

이 존재한다.

Closed Model은 Service로만 사용할 수 있는 경우가 많고,

Open Model은 Hardware만 있다면 직접 Download하여 Inference하거나 Fine-tuning할 수 있다.

---

# 108. Wan 계열 사례

강의에서는 중국에서 공개된 고품질 Open Video Generation Model 계열의 사례도 소개하며,

- Viewpoint 변화
    
- Lighting 변화
    
- Tone 변화
    
- Video Editing
    

등의 기능과 높은 VRAM 요구량을 설명한다.

이 부분은 특정 최신 Model 소개이므로

> **강의 당시의 기술 사례**

로 이해하면 된다.

---

# 109. Video를 다시 3D로

Video Foundation Model이 있다면 그 결과를

```text
Video
↓
Dynamic 3D Reconstruction
↓
3D Scene
```

으로 변환하는 Foundation Model도 결합할 수 있다.

---

# 110. Dynamic 3D

시간에 따라 움직이는 Object/Scene을 3D로 Reconstruction한다.

활용 예:

- 3D Map
    
- Real Estate
    
- AR/VR
    
- Digital Twin
    
- Robotics
    

등이다.

---

# 111. 결국 Multimodal Model의 방향은 계속 확장된다

처음에는

```text
Text
```

였다.

그다음

```text
Text + Image
```

가 되었다.

그리고

```text
Text
Image
Video
Audio
```

로 확장된다.

---

# 112. Omni Model

강의에서는 앞 차시에서

```text
읽고
쓰고
보고
듣고
말하는
```

Multimodal Model을 Omni Model이라는 표현으로 소개했다.

---

# 113. 그러나 Omni도 아직 모든 Modality를 의미하지 않을 수 있다

예를 들어

```text
Image Generation

Video Generation
```

까지 모두 하나의 Model이 직접 수행하지 않을 수도 있다.

---

# 114. Any-to-Any

더 나아간 방향은

```text
Text
Image
Audio
Video
      ↓
Unified Foundation Model
      ↓
Text
Image
Audio
Video
```

처럼 **어떤 Modality에서 어떤 Modality로든 변환**하는 것이다.

강의에서는 이를 **Any-to-Any** 방향의 Unified Foundation Model로 소개한다.

---

# 115. Foundation Model 시대의 중요한 개발자 역량

강의 마지막의 중요한 메시지다.

Foundation Model은 매우 빠른 속도로 새로 나온다.

따라서

```text
현재 Model 이름을 모두 외운다.
```

는 것보다

```text
어떤 종류의 Model이 있는가?

무슨 Input을 받는가?

무슨 Output을 내는가?

어떤 Task에 강한가?

Open인가 Closed인가?

내 System에서 어떻게 연결할 수 있는가?
```

를 관리하는 것이 더 중요하다.

---

# 116. 자신만의 Foundation Model Tool List

강의에서는 새로운 Model이 계속 등장하기 때문에

> **본인만의 Foundation Model 목록과 활용 지식을 지속적으로 관리하는 것 자체가 Skill이자 경쟁력이 될 수 있다.**

고 강조한다.

---

# 117. Small VLM 핵심 구조

```text
Large VLM
      ↓
너무 비쌈
      ↓
Smaller Language Model
+
Lightweight Vision Encoder
      ↓
Quantization
      ↓
Memory ↓
Compute ↓
      ↓
On-device / Edge Deployment
```

---

# 118. On-device Model에서 중요한 것

단순 Parameter 수만 보는 것이 아니다.

```text
Model Size

Quantization

Tokenizer Efficiency

Memory

Latency

Hardware Accelerator

Task Scope
```

를 함께 봐야 한다.

---

# 119. 언어별 Tokenizer 문제 전체 구조

```text
Training Corpus가
영어 중심
      ↓
English Pattern이
Vocabulary에 많이 등록
      ↓
English Tokenization 효율 ↑

반대로
Minority Language
      ↓
작은 Subword/Character로 분해
      ↓
Token 수 ↑
      ↓
Cost / Latency / Memory ↑
```

---

# 120. Model Routing의 전체 구조

```text
User Request
      ↓
Orchestrator
      ↓
Task 분석
      ↓
────────────────────
Simple → Small Model
Korean → Korean Model
Vision → VLM
Reasoning → Large Model
Domain → Specialized Model
────────────────────
      ↓
Final Response
```

---

# 121. Foundation Model 활용의 핵심 Pattern ① 직접 사용

```text
Foundation Model
↓
Zero-shot / Prompt
↓
Task 수행
```

예:

```text
SAM
CLIP
Grounding Model
```

등.

---

# 122. 활용 Pattern ② 앞단 Tool로 사용

```text
Image
↓
Segmentation Foundation Model
↓
Annotated Image
↓
VLM
↓
Better Reasoning
```

처럼 다른 Model의 성능을 보조한다.

---

# 123. 활용 Pattern ③ 여러 Model 연결

```text
Text
↓
Grounding Detector
↓
Box
↓
SAM
↓
Mask
```

처럼 Pipeline을 만든다.

---

# 124. 활용 Pattern ④ Fine-tuning으로 Repurpose

```text
Image Generation Foundation Model
↓
Task-specific Data
↓
Fine-tuning
↓
Depth / View / Control / 기타 Task
```

처럼 기존 Knowledge를 다른 Output으로 전환한다.

---

# 125. 활용 Pattern ⑤ Synthetic Data

```text
Foundation Model
↓
Synthetic Data 생성
↓
다른 Model 학습
```

에도 사용할 수 있다.

이번 강의의 Depth Adaptation에서도 이 Pattern이 등장한다.

---

# 126. 분석 Foundation Model vs 생성 Foundation Model

|구분|분석형|생성형|
|---|---|---|
|입력|Image/Video|Text, Noise, Condition 등|
|출력|Label, Mask, Box, Feature 등|Image/Video|
|예|SAM, Grounding DINO|Stable Diffusion, FLUX 등|
|핵심|Existing Data 이해|Data Distribution Modeling|
|재사용|Detection/Segmentation Pipeline|Editing/Depth/3D/Control 등|

---

# 127. Diffusion Model 핵심 흐름

```text
실제 Image
      ↓
Noise를 점점 추가
      ↓
Gaussian Noise

이 Reverse 방향을 학습
      ↓

Gaussian Noise
      ↓
Denoise
      ↓
Denoise
      ↓
...
      ↓
Image
```

---

# 128. Latent Diffusion 핵심 흐름

```text
Image
↓
Encoder
↓
Compressed Latent
↓
Diffusion / Denoising
↓
Clean Latent
↓
Decoder
↓
Image
```

Pixel Space보다 작은 Representation에서 Diffusion하여 효율성을 높인다.

---

# 129. Conditional Diffusion 핵심 흐름

```text
Text
↓
Text Encoder
↓
Condition
      ↘

Noise / Latent
↓
Denoising Network
↔ Cross-Attention
↓
Prompt 방향으로 Denoising
↓
Image
```

---

# 130. ControlNet 핵심 흐름

```text
Text
+
Pose / Edge / Sketch / Segmentation
        ↓
Conditional Diffusion
        ↓
조건을 더 정확하게 만족하는 Image
```

---

# 131. Foundation Model의 핵심 가치

이 강의에서 반복되는 결론은 하나다.

Foundation Model은

```text
하나의 Task 정답기
```

가 아니다.

대규모 Data에서 얻은 Knowledge를 가지고 있기 때문에

```text
새로운 Input

새로운 Output

새로운 Task

새로운 Domain
```

으로 비교적 쉽게 Adapt할 수 있다.

---

# 132. 자주 헷갈리는 점 ① — Small VLM은 그냥 큰 VLM을 압축한 것인가?

반드시 그렇지는 않다.

처음부터

```text
Small Language Model
+
Small/효율적 Vision Encoder
```

조합으로 설계할 수도 있고 Quantization 등 여러 기법을 함께 사용한다.

---

# 133. 자주 헷갈리는 점 ② — 작은 Model이면 모든 기능이 그대로 유지되는가?

아니다.

Memory와 Compute를 줄이는 대신 성능과 범용성에 Trade-off가 있을 수 있다.

특정 Application에 기능을 제한하기도 한다.

---

# 134. 자주 헷갈리는 점 ③ — Quantization은 Parameter 개수를 줄이는가?

강의에서 소개한 기본적인 Quantization의 핵심은

> **Parameter 하나를 표현하는 숫자의 Bit Precision을 낮추는 것**

이다.

즉 Parameter 개수 자체를 줄이는 것과는 구분한다.

---

# 135. 자주 헷갈리는 점 ④ — On-device AI는 Server를 사용하는가?

핵심 방향은 Model을 Device 내부에서 실행해 User Data를 외부 Server로 보내지 않고 처리하는 것이다.

---

# 136. 자주 헷갈리는 점 ⑤ — 한국어가 본질적으로 Token을 많이 쓰는 언어인가?

강의의 핵심 설명은 언어 자체보다 **Tokenizer Vocabulary와 Training Corpus 구성**의 영향이 크다는 것이다.

---

# 137. 자주 헷갈리는 점 ⑥ — 가장 큰 Model 하나만 쓰면 되지 않는가?

Task마다

- Cost
    
- Latency
    
- Language
    
- Modality
    
- Required Capability
    

가 다르다.

그래서 Model Routing이 유용할 수 있다.

---

# 138. 자주 헷갈리는 점 ⑦ — SAM은 Object Detector인가?

SAM의 중심 기능은 **Segmentation**이다.

Detection/Grounding Model과 결합하면 Text로 Object를 찾은 뒤 Mask까지 만들 수 있다.

---

# 139. 자주 헷갈리는 점 ⑧ — Grounding DINO는 Fixed Class Detector인가?

강의에서 강조하는 특징은 Text Description을 이용해 Open-vocabulary Object Grounding/Detection을 할 수 있다는 것이다.

---

# 140. 자주 헷갈리는 점 ⑨ — 생성모델은 Training Image를 그대로 저장해서 꺼내는가?

강의에서는 생성모델을

```text
P_data
```

를 직접 저장하는 것이 아니라

```text
P_model
```

로 Data Distribution을 근사하는 관점에서 설명한다.

---

# 141. 자주 헷갈리는 점 ⑩ — Dataset = Probability Distribution인가?

아니다.

Dataset은 실제 미지의 Distribution에서 얻어진 유한한 Sample 집합이다.

---

# 142. 자주 헷갈리는 점 ⑪ — Diffusion은 Noise를 한 번에 제거하는가?

기본적인 Diffusion 설명에서는 여러 Step에 걸쳐 조금씩 Denoising한다.

이 때문에 느릴 수 있다.

---

# 143. 자주 헷갈리는 점 ⑫ — Stable Diffusion은 Pixel에서 직접 Diffusion하는가?

강의에서 설명하는 Latent Diffusion은 Image를 Encoder로 Latent Space에 압축한 뒤 그 공간에서 Diffusion을 수행한다.

---

# 144. 자주 헷갈리는 점 ⑬ — Text Prompt는 Denoising 후에 붙이는가?

아니다.

Denoising 과정 중 Text Condition을 Cross-Attention 등을 통해 계속 반영하여 생성 방향을 제어한다.

---

# 145. 자주 헷갈리는 점 ⑭ — Guidance Scale은 Image Resolution인가?

아니다.

Text Condition이 Generation에 미치는 영향의 강도와 관련된 Hyperparameter다.

---

# 146. 자주 헷갈리는 점 ⑮ — ControlNet은 완전히 새로운 Image Generator를 처음부터 Training하는가?

강의에서는 기존에 잘 학습된 Image Generation Foundation Model을 활용하고 추가 Control Condition에 반응하도록 Adapt하는 방향으로 설명한다.

---

# 147. 자주 헷갈리는 점 ⑯ — Image Generation Model은 Image Generation 외에는 쓸 수 없는가?

아니다.

이미 Image Structure에 대한 Knowledge를 학습했기 때문에

- Depth
    
- Novel View
    
- 3D
    
- Editing
    

등 다른 Task로 Fine-tuning할 수 있다.

---

# 148. 자주 헷갈리는 점 ⑰ — Depth Estimation은 반드시 실제 Depth Sensor Data만 필요하다?

강의에서는 Synthetic Data를 이용해 Diffusion Foundation Model을 Depth Task에 Fine-tuning한 사례를 소개한다.

---

# 149. 자주 헷갈리는 점 ⑱ — Video Generation Model = World Model인가?

동일한 개념이라고 단정하면 안 된다.

강의에서는 사실적인 Video Generation 결과가

> **Physical World를 Modeling할 수 있는 World Model로 발전할 가능성**

을 보여준다는 관점으로 연결한다.

---

# 150. 자주 헷갈리는 점 ⑲ — Foundation Model 목록을 전부 외워야 하는가?

강의의 결론은 오히려 반대다.

Model은 너무 빠르게 바뀐다.

중요한 것은

```text
Model Category
Capability
Input
Output
License/Open 여부
사용 방법
```

을 관리하는 능력이다.

---

# 151. 앞 차시와 연결

## 1차시

```text
Foundation Model
↓
CLIP
↓
Contrastive Learning
↓
Image–Text Alignment
```

---

## 2차시

```text
SigLIP
↓
Vision Encoder + LLM
↓
LLaVA
↓
Qwen-VL
↓
Multimodal VLM
```

---

## 3차시

이제 이 기술을 실제로 배포하고 다른 Foundation Model과 조합한다.

```text
Large VLM
↓
Small VLM
↓
On-device

+
Segmentation
Detection
Generation
Depth
3D
Human
Video
```

으로 Vision Foundation Model 생태계 전체가 확장된다.

---

# 152. 이번 강의 전체 논리

```text
VLM은 좋다.
      ↓
그런데 너무 크고 비싸다.
      ↓
Smartphone이나
Personal Device에서
사용하고 싶다.
      ↓
Small VLM
      ↓
작은 LLM
+
Lightweight Vision Encoder
+
Quantization
      ↓
On-device 가능성 증가


그런데
실제 효율은
Model Size만으로 결정되지 않는다.
      ↓
Tokenizer
      ↓
언어별 Token 수 차이
      ↓
Cost / Latency / Memory 차이
      ↓
Language-specialized Model 필요


그리고
모든 Task에
최고 Model이 필요한 것도 아니다.
      ↓
Model Routing
      ↓
Task에 맞는 Model 선택
      ↓
Cost ↓
Quality ↑


그런 Model들을
Service에서 사용하려면?
      ↓
Model Serving
      ↓
LMDeploy 등
      ↓
REST API


이제 VLM 외에도
Foundation Model이 많다.
      ↓
Segmentation
→ SAM

Detection / Grounding
→ Grounding DINO

Tracking
→ Video Foundation Models
      ↓
여러 Model을
Pipeline으로 조합 가능


그리고
생성형 Foundation Model
      ↓
Generative Modeling
      ↓
Unknown P_data
      ↓
Dataset Sample만 관찰
      ↓
P_model을 학습
      ↓
P_model ≈ P_data
      ↓
새로운 Sample 생성


대표적인 성공 방식
      ↓
Diffusion
      ↓
Image → Noise
Forward Process
      ↓
Noise → Image
Reverse Denoising


하지만 Pixel Space는 비싸다.
      ↓
Latent Diffusion
      ↓
Image를 Latent로 압축
      ↓
Latent에서 Denoising
      ↓
Decoder로 Image 복원


원하는 Image를 만들고 싶다.
      ↓
Text Condition
      ↓
Cross-Attention
      ↓
Conditional Generation
      ↓
Guidance


더 정확하게 제어하고 싶다.
      ↓
Pose / Edge / Sketch /
Segmentation Condition
      ↓
ControlNet


Generation Model이
Image Structure를 이미 잘 안다.
      ↓
그 Knowledge를
다른 Task로 전환 가능
      ↓
Novel View
Depth
3D
      ↓
Marigold 등


사람 중심 Task도 많다.
      ↓
Human Foundation Model
      ↓
Sapiens


Image뿐 아니라 Video까지
      ↓
Video Generation
      ↓
Physical World Rule까지
배우는 것 아닐까?
      ↓
World Model


Audio까지 함께
      ↓
Multimodal Generation


최종적으로
      ↓
Text / Image / Audio / Video
어떤 Modality든 Input
      ↓
어떤 Modality든 Output
      ↓
Any-to-Any
Unified Foundation Model
```

---

# 153. 이번 강의에서 반드시 기억할 핵심

### 1. 대형 VLM은 성능은 좋지만 GPU Memory와 Deployment Cost가 높다.

### 2. On-device와 Edge 환경을 위해 Small VLM이 개발된다.

### 3. Small VLM은 작은 LLM과 효율적인 Vision Encoder 등을 결합하는 방향으로 설계할 수 있다.

### 4. Quantization은 Parameter를 표현하는 Bit Precision을 낮춰 Model Size와 Memory Cost를 줄이는 방법이다.

### 5. 경량화에는 Accuracy/Quality Trade-off가 있을 수 있다.

### 6. 매우 작은 VLM은 모든 범용 Task보다 특정 Application 기능에 집중할 수 있다.

### 7. Small VLM도 Captioning, QA, Counting, Detection 등의 기능을 수행할 수 있다.

### 8. On-device AI는 Data를 외부 Server로 보내지 않고 Device 내부에서 처리할 수 있다는 장점이 있다.

### 9. Smartphone에는 NPU 같은 AI 전용 연산 Hardware가 활용될 수 있다.

### 10. VLM이라는 용어는 CLIP형 Encoder Model과 LLM 기반 Multimodal Model 양쪽에 혼용될 수 있다.

### 11. 따라서 명칭보다 Architecture와 Text Generation 가능 여부를 확인해야 한다.

### 12. 언어별 Tokenization Efficiency는 큰 차이가 날 수 있다.

### 13. Training Corpus와 Token Vocabulary가 특정 언어 중심이면 다른 언어가 더 많은 Token으로 분해될 수 있다.

### 14. Token 수 증가는 Cost, Latency, Memory, Compute와 연결된다.

### 15. 그래서 한국어 등 특정 언어에 맞춘 Tokenizer와 Language Model의 필요성이 존재한다.

### 16. 모든 Subtask에 가장 큰 Model을 사용할 필요는 없다.

### 17. Agent/Orchestrator가 Task에 적합한 Model을 선택하는 Model Routing이 가능하다.

### 18. Routing을 통해 Cost를 줄이면서 Quality를 높일 수 있다.

### 19. Model Serving Tool을 사용해 VLM/LLM을 API Server 형태로 운영할 수 있다.

### 20. Vision Foundation Model은 Segmentation, Detection, Tracking, Generation, 3D 등 매우 다양한 종류가 존재한다.

### 21. SAM은 대표적인 Segmentation Foundation Model이다.

### 22. Segmentation Annotation은 Pixel-level Label이 필요해 구축 비용이 매우 크다.

### 23. Grounding 계열 Detector는 Natural Language로 새로운 Object Concept을 찾는 Open-vocabulary Detection에 활용할 수 있다.

### 24. Grounding Detector와 SAM을 결합하면 Text→Box→Mask Pipeline을 만들 수 있다.

### 25. 여러 Foundation Model을 적절히 조합하는 능력 자체가 실무적으로 중요하다.

### 26. Generative Modeling은 실제 미지의 Data Distribution `P_data`를 Model Distribution `P_model`로 근사하는 문제로 볼 수 있다.

### 27. Dataset은 `P_data` 자체가 아니라 그 Distribution에서 Sampling된 Sample 집합이다.

### 28. `P_model`이 `P_data`에 가까울수록 생성 Sample의 Quality도 좋아질 수 있다.

### 29. Diffusion Model은 대표적인 Image Generative Model이다.

### 30. Forward Process에서는 Image에 Noise를 점점 추가한다.

### 31. Reverse Process에서는 Noise를 점점 제거하여 Image를 생성한다.

### 32. 기본적인 Diffusion은 여러 Denoising Step이 필요하므로 느릴 수 있다.

### 33. Latent Diffusion은 Pixel Image를 Latent Representation으로 압축한 뒤 그 공간에서 Diffusion을 수행한다.

### 34. Stable Diffusion은 Latent Diffusion 계열의 대표적인 사례로 소개된다.

### 35. 전통적인 LDM에서는 U-Net을 Denoising Network로 사용할 수 있다.

### 36. 최근에는 Diffusion Transformer 계열도 사용된다.

### 37. Text-to-Image Generation에서는 Text를 Condition으로 제공한다.

### 38. Cross-Attention은 Image/Latent Feature와 Text Condition을 연결해 Denoising 방향을 제어한다.

### 39. Guidance Scale은 Condition의 영향력을 조절하는 개념으로 소개된다.

### 40. ControlNet은 Text 외에 Pose, Edge, Sketch, Segmentation Map 같은 Control Condition을 추가할 수 있게 한다.

### 41. Image Generation Foundation Model은 Image Structure에 대한 Knowledge를 다른 Task에 재활용할 수 있다.

### 42. Camera View를 Condition으로 Fine-tuning하면 Novel View Generation과 3D Understanding에 연결할 수 있다.

### 43. Diffusion Foundation Model을 Depth Estimation으로 Fine-tuning한 사례로 Marigold가 소개된다.

### 44. Synthetic Data도 이러한 Task Adaptation에 사용할 수 있다.

### 45. Depth와 3D Understanding은 Robotics와 Autonomous Driving 등 Physical AI에 중요하다.

### 46. 사람 중심 Task를 위한 Foundation Model로 Sapiens 같은 계열이 소개된다.

### 47. Human Foundation Model은 Pose, Segmentation, 3D, Surface Normal 등의 Task에 활용할 수 있다.

### 48. Video Generation Model은 Spatial뿐 아니라 Temporal Pattern도 Modeling해야 한다.

### 49. 고품질 Video Generation의 발전은 World Model 가능성과 연결되어 연구된다.

### 50. World Model은 Physical World의 상태와 변화 Rule을 Simulation하는 방향의 Model이다.

### 51. 최신 Multimodal Generation에서는 Video와 Audio를 함께 생성하는 방향도 존재한다.

### 52. 고품질 AI Video는 Deepfake와 Fraud 등의 Risk도 증가시킨다.

### 53. Video Editing Model은 기존 Video의 Style, Background, Scene 등을 수정할 수 있다.

### 54. Video를 Dynamic 3D로 변환하는 Foundation Model들도 존재한다.

### 55. Foundation Model은 Text→Image 수준에서 Text/Image/Audio/Video를 모두 주고받는 Any-to-Any 방향으로 발전하고 있다.

### 56. 특정 Model 이름을 모두 외우는 것보다 어떤 Foundation Model 종류와 Capability가 존재하는지를 지속적으로 관리하는 것이 중요하다는 것이 강의의 최종 메시지다.

---

# 154. 이번 강의의 핵심 한 문장

> **Small VLM은 대형 Vision-Language Model의 높은 계산·Memory 비용을 줄여 Smartphone이나 Edge Device에서도 Vision과 Language 기능을 사용할 수 있도록 하는 경량 Foundation Model이며, 실제 Vision AI 개발에서는 VLM 하나만 사용하는 것이 아니라 SAM 같은 Segmentation Model, Grounding Detector, Diffusion 기반 Image Generator, Depth·3D·Human·Video Foundation Model 등을 Task에 따라 Routing·Fine-tuning·Pipeline 형태로 조합함으로써 기존에 대규모 Data와 Model을 통해 학습된 Knowledge를 새로운 Application에 재사용하는 것이 중요하다.**

가장 짧게 기억하면 다음과 같다.

```text
Small VLM
= 성능 일부를 양보하고
  Device에서 실행 가능하게 만든다.

Quantization
= 숫자 정밀도를 낮춰
  Memory를 줄인다.

Tokenizer
= 언어에 따라
  Cost와 속도 차이를 만든다.

Model Routing
= 모든 문제에
  가장 큰 Model을 쓰지 않는다.

Vision Foundation Models
= SAM / Grounding / Generation /
  Depth / 3D / Human / Video 등
  이미 배운 능력을 가져다 쓴다.

Diffusion
= Noise에서 출발해
  단계적으로 Image를 복원한다.

Latent Diffusion
= Image가 아니라
  압축된 Latent에서 Denoising한다.

Foundation Model 활용의 본질
= 이미 배운 Knowledge를
  새로운 Task에 재사용한다.
```