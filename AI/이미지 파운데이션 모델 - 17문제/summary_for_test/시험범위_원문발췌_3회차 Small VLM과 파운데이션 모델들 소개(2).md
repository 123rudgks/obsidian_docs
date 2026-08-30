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

