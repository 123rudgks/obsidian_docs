# 3-2. 이미지 파운데이션 모델

> 📌 이 챕터는 세부 원리까지 깊게 파고들기보다, 핵심 원리와 각 모델이 어떤 특징을 가지는지 흐름 위주로 가볍게 파악하고 넘어가는 것을 목표로 한다.

## 목차 (Contents)

1. AI 파운데이션 모델 개념 및 대표 모델
2. Vision-Language Model (VLM)
3. Small VLM과 파운데이션 모델들 소개
4. 개인화, 합성 데이터 활용 사례

---

## 1-1. 파운데이션 모델(Foundation model)이란?

### AI 모델이란

- 가장 단순한 관점: 함수 또는 프로그램
- 조금 더 상세한 관점
    - 입출력을 연결하는 함수
    - 데이터로부터 그 형태(파라미터)가 결정되는 함수
    - **학습 때 보지 못했던 데이터에 대해서도 작동**이 요구되는 함수 → 이 조건이 핵심이며, 이를 **일반화**라고 부름
    - 예시: 강아지/고양이 이미지 → Neural Networks → dog/cat 분류

### 이상적인 AI 모델을 상상해보면

- 만약 AI 모델이 세상에서 발생 가능한 **모든 데이터**와 **각 데이터의 설명**을 다 기억하고 있다면?
- 그렇다면 내가 얻고 싶은 답과 유사한 답이 이미 DB에 저장되어 있을 확률이 높음 → 사실상 **검색엔진**과 유사한 개념 (예: 검색 포털, 최근접 이웃 탐색(Nearest Neighbor Search) 알고리즘)
- 그러나 이 방식은 데이터 확보·저장·탐색에 매우 큰 비용이 들어 현실적이지 않음

### 현실적인 대안: 압축

- **학습 = AI 모델에 데이터를 패턴화하여 압축**하는 것
- 이 압축 과정에서 데이터 간 비슷함과 다름을 파악하게 되고, 패턴을 익히면서 새로운 데이터에 대한 **일반화 능력**이 생김 (Representation learning)
- 즉, 뉴럴네트워크가 하는 일은 데이터를 압축하고 패턴을 찾아 일반화하는 것
- 여기서 "세상의 수많은 데이터를 최대한 기억할 수 있다면?" → 이 질문이 **파운데이션 모델** 개념으로 이어짐. 파운데이션 모델은 다양한 문제/작업에 범용적으로 적응할 수 있음

### 파운데이션 모델 vs 기존 AI 모델

- **기존 딥러닝 개발 패러다임**: 아기와 같이 언어, 시각, 청각, 촉각 등 기본적인 것부터 매 태스크마다 새로 학습해야 함 (노이즈에서부터 학습 → 매번 처음부터 가르쳐야 해서 비효율적)
- **파운데이션 모델 패러다임**: 거대 모델(커다란 뇌) + 대규모 데이터 학습(많은 지식과 경험) 기반 → 새로운 일을 처음 접해도 금방 배우고 잘할 수 있음
- 파운데이션 모델은 크게 **사전학습(Pre-training)** 단계와 원하는 태스크에 **적응(Adaptation)**하는 단계로 구성됨

### 파운데이션 모델 기반 개발 프로세스

Data Creation → Data Curation → Training → Adaptation → Deployment

### 파운데이션 모델의 특징

1. **[대규모 사전학습]**: 트랜스포머 모델 + 대규모 언어 데이터 학습
    - 태스크에 상관없이 비슷한 패턴들이 활용됨
    - 주로 비지도학습으로 훈련된 모델도 많이 등장 (대규모 데이터라 사람이 일일이 라벨링하기 어렵기 때문)
2. **[적응성]**: 높은 미세조정 성능 (높은 태스크 적응 성능) — 하나의 믿고 쓸 수 있는 모델 → 다양한 목적에 적응
3. **[범용성]**: 다양한 작업, 한정되지 않은 출력 지원
    - 예시 - 물체 판별: 기존은 출력 구조 자체가 20여개의 물체 구분으로 한정되었다면, 파운데이션 모델은 만 개 이상(또는 자연어 기반의 한정되지 않은 대상) 인식 가능

### 파운데이션 모델에 의한 AI 모델 개발의 변화

- 과거: 매번 태스크별로 모델을 새로 학습 / 현재: 잘 학습된 모델을 **얼마나 잘 활용하느냐**가 핵심
- 단, 파운데이션 모델 하나를 확보하려면 대규모 리소스(데이터, 계산, 전력)가 필요 → 일부 기관만 가능

**적응 활용 (Down-stream task adaptation) 기법들**

- 파라미터 변경 여부로 구분
    - **파라미터 고정** + 활용 → 프롬프트 엔지니어링/튜닝
        - Zero-shot: 처음 보는 문제를 추가 학습 없이 바로 적용 (모델 자체가 가진 배경지식 활용)
        - Few-shot: 예제 몇 개만 보여주면 바로 적용 가능
    - **파라미터 변경** + 활용 → 전이학습, Fine-tuning (처음부터 배우지 않아도 조금만 알려주면 금방 적응, 모델 가중치가 변경됨)

**전체 흐름** 대규모 데이터 → Pre-training(사전학습, 폭넓은 기본 지식 습득) → Zero-shot / Few-shot / Fine-tuning(특정 task에 맞게 추가 학습)

---

## 1-2. AGI를 향해서 & Vision-Language Model (VLM)

### AGI를 향해서

- **Human's Intelligence (cognition) = perception ∪ higher cognitive processes**
- 사람이 세상을 이해하는 과정: World → Perception(지각) → Cognition(인지) → Interpretation(해석)
    - 예: 정자 건물을 보고(Perception) → 뇌로 판단(Cognition) → "회색 지붕의 정자 옆에 서있는 남자"라고 해석(Interpretation)
- 이 구조를 보면 지각능력과 인지능력을 각각 따로 모델링하면 되지 않을까 하는 접근이 가능

### LLM의 등장과 한계

- 2022년 11월(ChatGPT) 이전: 인지 부분을 어떻게 구현할지 막막했음
- ChatGPT 이후: LLM이 높은 사고/추론 성능을 보여주기 시작 → **"인지 모델을 언어모델로 구현하면 되겠다"는 깨달음**
- 그러나 **사고·언어능력만으로는 현실 세계를 이해하기에 충분하지 않음** → Perception(지각) 능력이 빠져있기 때문

### LLM에 눈을 달아볼까? (시각언어모델, VLM)

- 언어모델에 "눈"을 붙여 이미지를 이해·설명하게 만드는 것 = **시각언어모델(VLM)**
- 구조: 이미지 → **이미지 인코더** → 토큰 변환 / 텍스트 질문 → 토큰 변환 → 두 토큰을 **언어모델**에 함께 입력 → 자연어 답변 생성
- 예시: ChatGPT(GPT-4)의 멀티모달 확장, Claude Computer Use처럼 화면을 보고 이해하는 시각 능력이 필요한 에이전트 사례

### 눈으로 어떤 것을 쓸까? — CLIP (2021) by OpenAI

- **CLIP = Contrastive Language-Image Pre-training**. 언어와 이미지를 모두 이해하는 모델이라는 점에서 VLM의 "눈"으로 자주 채택됨
- 언어-이미지 유사도 학습: 개 이미지(Anchor)와 텍스트 "Dog"는 가깝게, 관련 없는 텍스트는 멀게 학습
- 파운데이션 모델로서의 특징: 학습 안 한 새 도메인에도 강함(제로샷 전이), 자연어로 본 적 없는 카테고리도 정의 가능(언어 인터페이스)
- 성능: 익숙한 도메인(ImageNet)에서는 기존 모델(ResNet101)과 비슷하지만, 낯선 도메인(Sketch, Adversarial 등)에서는 CLIP이 압도적으로 강함 → 학습 때 못 본 스타일에도 일반화가 잘 됨

---

## 1-3. CLIP 구조 & 학습 방법

### CLIP 구조 - 텍스트 인코더 (Transformer 기반 Text Encoder)

- **Remind - Transformer**: 원래 구조 = **인코더(Encoder) + 디코더(Decoder)** (예: "Je suis étudiant" → "I am a student" 번역 구조)
- **CLIP에서는 Encoder only 구조 사용**: 문장을 생성(번역)하는 게 목적이 아니라, 이해해서 벡터(임베딩)로 압축하는 게 목적이므로 디코더 불필요

### CLIP 구조 - 이미지 인코더 (ViT: Vision Transformer, 2020)

**Remind - Vision Transformer**

- 입력 구성: 텍스트 인코더는 **Sub-word 단위** 임베딩, 이미지 인코더는 **패치 단위** 임베딩
- ViT: 비전 분야에 트랜스포머를 (최소 수정으로) 적용한 모델

**ViT 처리 과정**

1. 이미지를 작은 패치(16×16×3)로 나눔
2. 각 패치를 1D로 Flatten
3. **Learnable position embedding** 사용 — 각 패치의 위치 민감 정보를 추가, 학습 과정에서 함께 학습됨
4. Transformer encoder: 패치 처리
5. **MLP Head**를 통해 분류 작업 수행 — Head 수정으로 다른 작업 transfer learning 가능 (CLIP은 이 부분을 CLIP만의 학습법으로 학습)

### CLIP (2021) 학습 - 대조 학습 (Contrastive learning)

**학습 기준**

- 목표 이미지(앵커)를 대응하는 텍스트(양성)와 가깝게, 일치하지 않는 여러 텍스트(음성)와는 멀게
- 학습 목표: 의미에 따른 거리 감각을 학습하도록 유도 (예: 강아지 이미지 → "강아지"와 가깝게, "코끼리"/"자전거"/"컴퓨터"와 멀게)

**Numpy-like pseudocode 흐름**

1. `image_encoder`(ResNet 또는 ViT)와 `text_encoder`(CBOW 또는 Text Transformer)로 각 모달리티의 feature 추출: `I_f = image_encoder(I)`, `T_f = text_encoder(T)`
2. 각 feature를 projection하여 **같은 차원의 joint multimodal embedding**으로 변환 후 L2 정규화 → `I_e`, `T_e`
3. 배치 내 모든 이미지-텍스트 쌍에 대해 **스케일된 코사인 유사도**(logits) 계산: `logits = I_e · T_e^T * exp(t)` (t는 학습되는 temperature 파라미터)
4. **대칭 손실함수(symmetric loss)**
    - labels: 배치 내 대각선(자기 자신과 짝인 것)이 정답
    - `loss_i` = 이미지 방향 cross-entropy (axis=0), `loss_t` = 텍스트 방향 cross-entropy (axis=1)
    - `loss = (loss_i + loss_t) / 2`

**Softmax Recap**

- softmax: 임의의 실수 벡터를 **0~1 사이 값이면서 합이 1**인 벡터로 변환 → 확률처럼 취급 가능

**손실함수 수식 구조** (s_ij: i번째 이미지와 j번째 텍스트 임베딩 간의 코사인 유사도)

- **Image→Text Loss**: 각 이미지 i를 앵커로, 모든 텍스트 j와의 유사도(s_ij)를 softmax에 넣어 확률분포를 만들고, 정답 텍스트(i번째)에 대한 negative log-likelihood 계산
- **Text→Image Loss**: 반대로 각 텍스트를 앵커로 놓고 동일하게 계산
- 최종 CLIP loss = 두 loss의 평균

### CLIP 간단 응용 - 제로샷 이미지 인식기

1. 텍스트로 원하는 물체 카테고리 리스트 준비 (예: plane, car, dog, bird...)
2. 각 카테고리를 "A photo of a {object}." 형태 문장으로 만들어 **텍스트 인코더**에 통과 → 텍스트 임베딩들을 미리 준비 (Vector DB처럼)
3. 쿼리 이미지를 **이미지 인코더**로 임베딩한 뒤, 미리 준비된 모든 텍스트 임베딩과 비교(내적)해서 가장 높은 점수의 카테고리를 반환

---

## 1-4. SigLIP & 멀티모달 정합 응용

### SigLIP (2023) - softmax 대신 sigmoid 기반 손실함수

**기존 CLIP(대조학습)의 한계**

- 이미 충분히 거리가 먼 음성(negative) 데이터 쌍들에 대해서도 계속 거리를 벌리기 위해 학습이 진행됨 (비효율)

**SigLIP의 해법**

- CLIP과 달리, 일치하지 않는 음성 데이터에 **제한된 영향만 받도록** 손실함수 디자인을 고침
- Label z = -1(음성 페어) → s가 작아지면 좋음 / Label z = +1(양성 페어) → s가 커지면 좋음

**Sigmoid 함수의 역할**

- 각 이미지-텍스트 쌍마다 "맞다/아니다"를 독립적으로 판단하는 구조
- sigmoid 곡선은 입력값이 어느 정도 이상 커지거나 작아지면 출력이 0 또는 1 근처로 포화(saturate)됨 → **어느 정도 커지면(또는 작아지면) 더 이상 고려하지 않음** → 이미 충분히 먼 negative pair를 계속 밀어내려는 CLIP의 비효율을 해결
- Algorithm: CLIP과 유사하게 임베딩을 정규화·유사도(logits) 계산하되, loss 계산 시 `log_sigmoid(labels * logits)` 사용 (softmax 대신 sigmoid)
- 성능: 노이즈가 많은 실제 데이터 환경에서 SigLIP이 CLIP보다 더 안정적이고 강건함

### 멀티모달 정합 응용 (Multi-modal Alignment)

- 개념: 서로 다른 모달리티(이미지, 텍스트 등) 간의 **공통된 임베딩 벡터 공간**을 구성 → 그 공간 안에서 모달리티 간 유사도 비교 가능
- 대표 모델: **CLIP**(이미지-텍스트), **ImageBind**(Meta, 소리·텍스트·이미지·열화상·깊이맵 등 더 다양한 모달리티를 하나의 공간에 결합)
- ImageBind 응용: Cross-Modal Retrieval(오디오로 이미지/텍스트 검색), Embedding-Space Arithmetic(임베딩 연산), Audio-to-Image/Segmentation(소리만으로 이미지 생성·분할)
- CLIP/SigLIP은 그 자체로 대화형 모델이 아니라 VLM의 "눈" 역할이며, 여기에 별도 언어모델(OPT, Vicuna, PaLM 등)을 결합해 실제 VLM이 만들어짐 (예: BLIP-2, LLaVA, mPLUG-Owl은 CLIP 기반 / PaLI-X, SmolVLM은 SigLIP 기반)

---

## 1-5. 멀티모달 언어모델의 실제 구현 - LLaVA & Qwen-VL 시리즈

### AGI 구조에 CLIP과 LLM을 채워보면

- World → Perception → Cognition → Interpretation 구조에서, **Perception = CLIP**, **Cognition = LLM**
- 즉 "언어모델로 사고할 수 있는 뇌는 있는데, 눈은 뭘 붙일까?" → 답은 CLIP
- 대표적인 멀티모달 언어모델: ChatGPT, Claude, LLaVA, Qwen-VL, InternVL, LLaMA-Vision 등

### CLIP(눈)과 LLM(뇌)을 어떻게 물리적으로 연결하나?

- **Linear Projection(선형 레이어)**로 연결
- 구조: 이미지 → **Image Encoder**(고정) → Feature map → **Linear Projection**(학습됨) → Soft prompts(텍스트 공간으로 투영) → **Text Decoder**(고정) → 텍스트 출력
- 즉 CLIP이 만든 Image Space의 벡터를, 학습 가능한 얇은 레이어 하나로 언어모델의 Text Space로 "번역"해서 넘겨주는 구조

### LLaVA (Large Language and Vision Assistant, 2023)

- 이 Linear Projection 아이디어를 **처음 시도한 모델**
- 구조: Vision Encoder(고정, Zv) → Projection W(학습됨) → Hv(이미지 토큰) + Hq(언어 명령 토큰) → Language Model(고정) → Language Response

**학습 2단계**

- **Step 1: 사전학습(Pre-training)** — Vision Encoder와 Language Model은 고정, **Projection layer만 학습** → 거대한 두 모델은 그대로 두고 얇은 연결 레이어만 학습해 자원·시간 절약
- **Step 2: Fine-tuning** — Projection layer + Language Model까지 함께 학습(Vision Encoder는 여전히 고정) → 정밀도 최적화 등으로 메모리 효율화하며 특정 작업에 맞춰 미세조정

**LLaVA 학습 데이터 (합성 데이터)**: ChatGPT를 활용해 시각 설명 데이터(visual instruction data) 생성 — COCO의 이미지+라벨(캡션·박스)을 바탕으로 ChatGPT가 역으로 질의를 생성 (대화/상세설명/복잡추론 타입)

### Qwen-VL 시리즈 (Alibaba) - 대표적인 오픈소스 VLM

- **Qwen-VL**: Qwen-LM에 시각 처리 능력 부여. 여러 이미지 입력·번역·텍스트 읽기·위치 찾기 등 다양한 능력 보유
    - 학습 파이프라인(3단계): Stage1 저해상도로 Projection만 빠르게 학습 → Stage2 고해상도로 QwenLM까지 학습 → Stage3 다시 ViT 고정 후 QwenLM 미세조정 — 저해상도로 기초를 잡고 고해상도로 정교화하는 방식
- 이후 버전들은 기능이 계속 확장됨: **Qwen2-VL**(임의 해상도 처리, M-RoPE 도입) → **Qwen2.5-VL**(문서 파싱·객체 그라운딩 강화) → **Qwen2.5-Omni**(읽고·쓰고·보고·듣고·말하는 Any-to-Any) → **Qwen3-VL**(코딩까지 가능한 최신 버전)

**Positional Embedding 정리 (M-RoPE 이해를 위한 배경)**

- Position encoding의 역할: 없으면 토큰들의 순서 정보를 다룰 수 없음
- 기존 PE: Positioned Token = Token + PE (처음 입력 시 한 번만 더함)
- RoPE: 중간 activation feature인 Query(q_i)와 Key(k_j)에 대해 **회전(rotation)**을 적용 (q'_i = R(i)q_i, k'_j = R(j)k_j)
- **M-RoPE (Multimodal RoPE)**: RoPE를 확장해 **1D 텍스트, 2D 시각(이미지), 3D 비디오**(시간 축 포함)의 위치 정보를 모두 부여할 수 있도록 만든 것 → 멀티모달을 다룰 수 있게 함

---

## 1-6. VLM 성능 향상 트릭 & 도메인 특화 파운데이션 모델

### VLM의 성능을 높이는 트릭 - Set of Mark (SoM)

- VLM에서는 인지능력(언어모델)보다 **여전히 지각능력(Perception)이 병목**
- 개념: 물체 탐지·세그멘테이션 모델로 입력 이미지에 물체별 **숫자 마크를 미리 붙여준 뒤** VLM에 입력하는 방법
- 효과: 부족한 시각 능력을 보완해 비약적인 성능 향상 (예: 번호 없이는 "왼쪽에 컵" 같은 부정확한 답변 → 번호가 있으면 "12번 램프"처럼 정확)
- 응용: Computer 작동 Agent의 비주얼 프롬프팅에 유용

### 도메인 특화 파운데이션 모델

- **의료**: BiomedCLIP·MedCLIP(의료 이미지-텍스트 정합, Contrastive learning), LLaVA-Med(LLaVA를 의료 데이터로 파인튜닝, X-ray 진단 대화 가능)
- **제조업**: AnomalyGPT — ImageBind 이미지 인코더 + Vicuna로 결함(anomaly) 탐지 챗봇
- **3D 언어 모델**: 3D Feature(point cloud) + Question → Perceiver → LLM → 위치 토큰 포함 통합 어휘로 답변. 3D 질의응답, 공간 네비게이션, 로봇팔 동작 생성에 응용
- **로봇 행동 모델 (VLA, Vision-Language-Action)**: 텍스트 명령+로봇 시점 영상을 입력받아 로봇 행동(위치 변화, 관절 움직임)을 출력. VLM+Action expert 구조로 Physical AI와 밀접 (예: PaLM-E)

---

## 2-1. 경량화된 소형 VLM (sVLM)

### sVLM - 경량화 시도들

- 서버 비용 문제로 개인 기기에서 돌아가는 가벼운 VLM에 대한 수요에서 출발
- 3단계 흐름: **VLM** 개발 → **sLLM**(언어모델 경량화) → **sVLM**(효율적인 소형 VLM 개발)
- RAM 대비 성능(Win Rate) 트레이드오프에서 왼쪽 위(cheaper/faster + better)에 가까울수록 좋은 모델

### 대표 sVLM 모델들 (핵심만)

- **SmolVLM**: Vision encoder(SigLIP)+Modality projection/Pooling(토큰 수 축소)+경량 LLM(SmolLM2 1.7B) 구조. 이미지 개수가 늘어나도 다른 모델 대비 메모리 사용량이 완만하게 증가
- **Moondream 0.5B**: 모바일·엣지 실시간 실행 목표. **양자화**(파라미터 정밀도를 낮춰 크기·메모리 절감, 대신 정확도 소폭 감소)로 375~479MB까지 경량화
- **Gemini Nano**: 온디바이스용 경량 Gemini(Nano-1/Nano-2), 픽셀 9 시리즈 탑재
- **갤럭시 온디바이스 AI**: 모바일 NPU로 이미지 생성·인페인팅·문법 교정 등 온디바이스 생성형 AI 수행
- 기타: Qwen 2.5-VL 3B, Phi-3.5 vision 4.2B, DeepSeek-VL2 1B, Gemma 3 1B 등

### 용어 정리 - VLM의 두 가지 의미

- "VLM"은 두 가지 의미로 혼용됨: CLIP처럼 이미지-텍스트 정합만 하는 **인코더 역할의 모델** vs LLaVA처럼 인코더+LLM을 결합한 **전체 대화형 모델**
- 후자를 구분해서 **MLLM(Multimodal LLM)** 또는 **LMM(Large Multimodal Model)**이라고도 부름

---

## 2-2. 언어별 토큰화 복잡성 & 한국어 sVLM

### 언어별 구조적·형태적 차이에 따른 토큰화 복잡성

- 언어에 따라 동일한 문장도 토큰화 후 길이 차이가 큼. 대부분의 토크나이저가 **영어 중심**으로 설계되어, 비영어권 언어는 컨텍스트 활용 효율이 낮고 토큰 낭비가 발생하는 **"구조적" 불이익**이 있음 (언어 자체의 한계가 아니라 토큰화 방법의 효율성 차이)
- 예시 비교: "I love machine learning"(영어) 4토큰/23자 vs "나는 머신러닝을 사랑한다"(한국어) 7토큰/13자 vs 중국어 4토큰/7자 vs 일본어 9토큰/12자
- 핀란드어·독일어처럼 형태소가 복잡한 언어는 서브워드 토큰 수가 크게 증가
- 한국어 특화 토크나이저로 확장 시 효율 개선: "이번 방학 때 뭐해?" — Base 19토큰 → Extended(한국어 특화) 8토큰

### 한국어 sVLM 모델

- **HyperCLOVAX-SEED-Vision-Instruct-3B**(NAVER), **Kanana-1.5-v-3b-instruct**(Kakao) — 한국어 이미지 이해·멀티모달 지시 이해에서 범용 모델보다 우수한 성능
- 한국어 VLM을 별도 개발하는 이유는 위 토큰화 비효율 문제와 직결됨

### 실습 - LMDeploy

- LLM/VLM의 효율적 압축·배포·서빙을 지원하는 오픈소스 툴킷 (지속적 배치·양자화 등으로 처리량↑, 지연↓)
- LLaVA, Qwen-VL 시리즈, InternVL 시리즈 등 다양한 오픈소스 VLM 지원
- 실습 코드 예시(InternVL 배포)
    
    ```python
    from lmdeploy import pipelinefrom lmdeploy.vl import load_imagepipe = pipeline('OpenGVLab/InternVL2-8B')image = load_image('https://raw.githubusercontent.com/open-mmlab/mmdeploy/main/tests/data/tiger.jpeg')response = pipe((f'describe this image', image))print(response)
    ```
    
    - 온라인 배포는 `lmdeploy serve api_server OpenGVLab/InternVL2-8B` 한 줄로 API 서버 형태 서빙 가능

---

## 2-3. 이미지/영상 세그멘테이션 & 탐지 파운데이션 모델

- **Segment Anything (SAM, 2023 / SAM2, 2024, Meta)**: 클릭·박스·텍스트 등 다양한 입력으로 원하는 영역 마스크 추출. 약 1,100만 이미지(10억 마스크)로 학습. SAM2는 비디오까지 확장
- **Grounding DINO (2023, IDEA Research)**: 텍스트 입력으로 이미지 내 물체 탐지, 높은 일반화 성능
- **Grounded SAM (2024)**: Grounding DINO(탐지)+SAM(분할) 결합으로 텍스트 기반 탐지+분할 동시 수행
- **SAMURAI (2024)**: SAM2 기반 비주얼 물체 트래킹 SOTA, 비디오 편집·CCTV 분석·스포츠 중계 등에 응용

---

## 2-4. 영상/이미지 생성 파운데이션 모델

- 이미지 생성 모델은 대규모 이미지로 학습되어 텍스트 설명을 토대로 새 이미지를 생성하며, 데이터 증강·화질 개선·스타일 변경 등에 응용됨

### 생성 모델링이란?

- **데이터셋**: 미지의 데이터 분포 p_data(x)로부터의 샘플들
- **생성모델**: 데이터셋으로부터 데이터 분포 p_data(x)를 알아내는 과정 → 알 수 있다면 새로운 데이터 x_new를 샘플링(생성)할 수 있음
- **핵심 어려움**: p_data(x)가 어떤 형태인지 우리는 모름(샘플링된 실제 데이터만 알고 있음) → **생성모델링의 목표**는 실제 분포를 유사 모델 p_model(x)로 **근사**하는 것 (p_data(x) ≈ p_model(x))

### 생성 모델링 문제 정리

- 모르는 것: 미지의 데이터 분포 p_data(x) / 알고 있는 것: 실제 데이터셋 {x} / 학습: 주어진 {x}로부터 p_model(x)를 학습

**생성 모델 설계**

- 설계가 정교할수록 실제 데이터 분포에 가까워짐 → 생성 품질 향상
- p_data(주황 점)와 가능한 모델 집합 M(파란 영역) 사이의 거리를 **모델 갭**이라 부르며, 갭이 클수록 생성 샘플 품질이 저하됨 (예: 강아지 생성 의도였지만 모델 갭으로 "테니스공인지 강아지인지" 애매한 샘플)
- 핵심 질문: p_model(x)의 모델 구조는? 학습 방법은? → 이후 GAN, Diffusion Model 등 구체적 설계로 이어짐

---

## 2-5. Diffusion Model & Latent Diffusion Model (LDM)

### Denoising Diffusion Probabilistic Model (DDPM, a.k.a. Diffusion model)

- 최근 성공사례를 보여준 대표적인 영상 생성 모델
- **메커니즘**: 노이즈로부터 점진적으로 노이즈를 제거해가며 이미지를 구체화하는 생성 방식
- **학습법**: 이미지에 합성 노이즈를 입히고, 그 노이즈가 무엇이었는지 예측하는 문제를 반복 학습하도록 유도
- **단점**: 느림 (여러 스텝을 거쳐야 생성이 완료됨)

**Forward / Backward 과정**

- **Forward (노이즈를 더해가는 과정)**: 원본 이미지(예: 고양이 사진)에 점차 노이즈를 추가해 최종적으로 가우시안 노이즈(정규분포, 가장 간단한 분포)로 만듦
- **Backward (노이즈를 제거해가는 과정)**: 반대로 가우시안 노이즈에서 출발해 점차 노이즈를 제거하며 원본과 유사한 이미지를 복원 → **실제 이미지 생성 시에는 이 backward 과정을 사용**

### Latent Diffusion Model (LDM)

- **Stable Diffusion(2022)**에서 소개된 기법
- 고차원 **픽셀 단위** 대신, 저차원 **latent 임베딩**(압축된 특징 정보)으로 변환한 후 diffusion 연산을 수행하는 방식
- 연산 효율을 높임과 동시에 이미지 생성 품질도 향상시킴
- 구조 흐름: 이미지 → **Encoder** → latent 임베딩(Latent Space) 생성 → 이 공간 안에서 forward(노이즈 추가)/backward(노이즈 제거) diffusion 수행 → **Decoder** → 다시 pixel space 이미지로 복원

**LDM 상세 구조**

- **Pixel Space**(원본 x) ↔ Encoder(E)/Decoder(D)를 통해 Latent Space와 연결
- **Latent Space**: Diffusion Process가 일어나는 공간. **Denoising U-Net(ε_θ)**이 각 timestep마다 제거할 노이즈를 예측
    - U-Net 내부에서 Q, K, V 기반 **cross-attention**으로 조건(conditioning) 정보를 반영
    - skip connection, switch, concat 등을 통해 정보 결합
- **Conditioning**: Semantic Map, Text, Representations, Images 등 다양한 조건을 cross-attention으로 diffusion 과정에 주입 가능 → 텍스트 프롬프트 기반 이미지 생성이 가능해지는 핵심 부분

**LDM의 2단계 생성 과정**

1. **1단계 - 텍스트 특징 생성 (Text representation generation)**
    - 텍스트 입력(prompt)을 함축하는 임베딩 값을 얻는 과정
    - **Text Representation Generator**가 담당
2. **2단계 - 이미지 특징 개선 (Image representation refinement)**
    - 텍스트 특징을 바탕으로, 랜덤 노이즈(seed, timestep 0)에서 시작한 이미지 표현을 점차 개선하여 고품질 이미지를 생성하는 과정
    - **Image Representation Refiner**가 담당하며, 각 timestep마다 U-Net이 "제거해야 할 노이즈"를 예측 → 노이즈를 약화(weaken)시켜 다음 timestep의 표현(representation)을 생성
        - 예: timestep 49의 representation → UNet이 노이즈 예측 → timestep 50의 refined representation
    - **Guidance scale**: 이미지 표현이 텍스트 프롬프트를 얼마나 강하게 따를지 조절하는 값. 값이 높을수록 텍스트 프롬프트에 강하게 부합하지만, 지나치면 결과가 과장(exaggerate)될 수 있음
    - 여러 timestep을 반복한 뒤 **Upscale**을 거쳐 최종 고해상도 이미지 생성

**요약**: 1단계(텍스트 이해) → 2단계(노이즈에서 이미지로 점진적 정제) → 업스케일 순서로 진행되며, DDPM의 forward/backward 개념을 latent space 위에서 효율적으로 구현한 것이 LDM의 핵심 아이디어

_(출처: Diffusion Explainer - Stable Diffusion Explained with Visualization (poloclub.github.io) / Rombach et al., "High-Resolution Image Synthesis with Latent Diffusion Models", CVPR 2022)_

---

## 2-6. 대표적인 이미지 생성 모델 (Closed / Open Source)

- **Closed - Midjourney v7**: 세부 묘사·프롬프트 해석 정확도·손/신체 일관성이 높은 상용 이미지 생성 모델 (2025.4)
- **Open Source - Stable Diffusion 3/3.5 (Stability AI)**: Diffusion Transformer 아키텍처로 품질·다중 객체 처리 개선, 텍스트 표현 강화, 800M~8B 다양한 크기 제공
- **Open Source - FLUX (Black Forest Labs)**: Rectified Flow Transformer 기반, 텍스트 이미지화 능력 탁월. Pro(유료)/Dev(연구용)/Schnell(경량·상업 허용) 세 변형

---

## 2-7. 이미지 생성모델 응용 - 파인튜닝으로 용도 변경

> 기반 diffusion 구조는 그대로 두고 파인튜닝을 통해 원래의 "텍스트→이미지" 용도를 넘어 다양한 태스크로 확장되는 사례들

- **ControlNet (2023)**: 스케치·Depth map·Human pose 등 다양한 조건 신호를 입력받아 그 조건에 맞는 이미지 생성. 커뮤니티에서 매우 활발히 응용
- **Zero123XL (2023)**: 2D 이미지 한 장으로 원하는 카메라 각도의 모습이나 3D 전체 형태를 생성 (VR/AR 콘텐츠에 응용)
- **Marigold (2024)**: 이미지 생성 Diffusion 모델을 합성데이터로 파인튜닝해 정교한 단안 깊이 추정 수행
- **JointDiT (Microsoft, POSTECH, 2025)**: 이미지와 3D 깊이 맵을 동시에 생성/추정해 물리적으로 더 그럴듯한 장면 생성

---

## 2-8. 3D 파운데이션 모델

- **Depth Anything v2 (HKU, TikTok, 2024)**: SAM 이후 깊이맵 예측용 vision foundation 모델. 150만 라벨 데이터+6,200만 비라벨 데이터로 성능 극대화. 자율주행·로봇 비전·3D 복원에 응용
- **Sapiens (Meta, 2024)**: 3000만 이미지로 학습된 사람 중심 파운데이션 모델. 2D Pose Estimation(자세), Body-part Segmentation(신체부위), Depth Estimation(거리), Surface Normal Prediction(표면 법선) 등 사람 중심 태스크 수행

---

## 2-9. 비디오 생성 모델 (Closed / Open Source & 응용)

- **Sora (OpenAI, 2024.12)**: ChatGPT Pro에 공개, 텍스트/이미지→비디오 생성(최대 1080p·20초). 물리 법칙에 대한 이해를 보여줘 **월드 모델**(현실 세계 규칙을 이해·시뮬레이션해 미래를 예측/계획하는 모델)로서의 가능성을 시사
- **Veo 3 (Google Gemini)**: 영상과 자연스럽게 싱크된 소리까지 함께 생성
- **Modify Video (Luma Labs)**: 기존 영상의 배경·스타일을 변형하는 비디오 편집 모델
- **Wan 2.2 (오픈소스, 2025.7)**: text/image/sound-to-video 등 다양한 모드, 최대 720P·24FPS
- 응용 사례: Coca-Cola의 AI 광고/영화 제작, **HeyGen's Avatar IV**(텍스트 스크립트+목소리 샘플+사진 1장 → 스피킹 비디오, NotebookLM과 조합한 기술 스택 사례)

---

## 2-10. Dynamic 3D 파운데이션 모델

- **MegaSaM (Google DeepMind, 2024.12)**: 단안 카메라 동영상에서 정확한 카메라 포즈·깊이 추정
- **CUT3R (UC Berkeley, Google DeepMind, 2025.1)**: 가상 시점의 미관측 영역까지 추론, 순서 없는 사진 모음도 자연스럽게 처리

---

## 2-11. Audio-Vision Language Models

- 대규모 언어모델에 영상·소리 입력을 확장해 멀티모달 언어모델로 발전 중
- **OneLLM (2024)**: ImageBind 기반 비디오 입력 / **VideoLLaMA2 (2024)**: 프레임 단위 비디오 입력
- 예시(Bubo-GPT): 이미지 속 소리 나는 지점(예: 헤어드라이어)을 오디오와 매칭해 정확히 지목·설명

---

## 2-12. Toward Unified Foundation Models - NExT-GPT (2023)

- **Any-to-Any Multimodal Large Language Model**
- 구조: Text/Image/Audio/Video 입력 → 각 모달리티별 **Encoder** → **Input Projection** → 중앙의 **LLM**(의미 이해·정렬) → **Output Projection** → 각 모달리티별 **Diffusion 모델** → Text/Image/Audio/Video 출력
- 5단계 흐름: Multimodal Input Encoding → LLM-centric Alignment → LLM-based Semantic Understanding → Instruction-following Alignment → Multimodal Output Generation
- 하나의 LLM을 중심으로 모든 모달리티를 입출력할 수 있게 만든 "통합 파운데이션 모델"의 방향성을 보여줌

---

## 유용한 파운데이션 모델들 정리 (참고 리스트)

> 최신 프론티어 모델들은 계속해서 갱신되고 있어, 본인만의 리스트를 만들어 놓는 것이 경쟁력이 될 수 있음

- Image: DINOv3
- Image & Text: CLIP, BLIP (Grounded: GLIPv2)
- Language-to-policy: RT1, RT2
- Speech recognition: wav2vec, Whisper
- Audio & text: CLAP
- Multi-modal embedding: ImageBind, Mega-Transformer
- Multi-modal LLM: InternVL, Qwen3-VL
- Object 3D: Zero123XL
- Text & satellite: RemoteCLIP

---

## 4-1. 파운데이션 모델 + Fine-tuning

### 파운데이션 모델과 Fine-tuning이 필요한 이유

- 파운데이션 모델: 방대한 데이터로 학습된 초대형 딥러닝 모델. 다양한 작업이나 범용적인 문제에 바로 적용 가능
- 최근에는 텍스트뿐만 아니라 이미지, 오디오, 비디오 등의 다양한 입력 데이터를 처리할 수 있는 멀티모달로 확장
- 하지만 최신 정보나 특정한 작업/도메인에 최적화되어 있지 않아, 즉시 활용이 어려운 경우가 반드시 있음 → Fine-tuning이 필요해지는 이유

### AI 리터러시++ (강사 생각) - 차별화된 AI 종합 활용 능력

- AI의 작동 원리를 이해하고, AI가 생성한 정보를 비판적으로 분석하며, AI를 도구로서 효과적으로 활용할 수 있는 역량 + **AI를 내 입맛대로 변경해서 사용할 수 있는 능력**
- AI 리터러시를 구성하는 6가지 축: AI와 데이터 이해, 프롬프트 엔지니어링, AI 활용 문제해결, **AI 개인화 능력**, AI 비판적 사고, AI 윤리 영향 고려
- AI 개인화 능력 = AI 모델을 재학습하고 여러 모델을 연동할 수 있는 능력 → 이 챕터(Fine-tuning)가 다루는 핵심 역량

### 미세조정(Fine-tuning)이란

- 추가 학습을 통해 이미 학습된 모델을 조금만 튜닝하는 것
- Fine-tuning을 통해 특정 작업에 특화된 모델을 개발할 수 있음
- **파운데이션 모델 + Fine-tuning = 실용적인 개인화 파운데이션 모델**
    - 적은 데이터로 학습 가능
    - 학습 리소스 절약 가능
    - 특정 작업에 대한 우수한 성능
- 흐름: Pretraining(Large data → Base Model) → Fine-tuning(Small data → Fine-tuned Model)

---

## 4-2. Fine-tuning이란? (프롬프팅과 비교)

- (MLLM 기준) 사전학습된 모델에 프롬프팅을 통한 작업을 했을 때보다 Fine-tuning이 더 좋은 퀄리티의 결과물을 생성
- 프롬프트에 넣는 예제보다 훨씬 더 많은 예제를 통해 학습 가능
- 프롬프트의 길이가 줄어들면서 토큰 개수 절약
- 응답하는 데에 걸리는 시간(latency)을 단축
- (비교 예시: 태스크 설명 + 예시("강아지⇒dog", "고양이⇒cat", "토끼⇒rabbit") + 프롬프트("소⇒?")를 매번 프롬프트에 넣어 사전학습된 언어 파운데이션 모델로 "소⇒cow"를 유도하는 few-shot 프롬프팅 방식 — Fine-tuning 없이 매번 프롬프트에 예시를 넣어야 하는 방식과 대비됨)

---

## 4-3. Fine-tuning의 핵심 하이퍼파라미터 - Learning Rate

### Remind - Gradient Descent (GD)

- Gradient(경사): 손실함수(Loss)를 미분해 구한 기울기
- 비유: 강아지에게 "앉아!"라고 반복해서 명령하고 교정해주면 점차 올바른 행동(앉기)으로 수렴해가는 과정 = 모델이 손실을 줄이는 방향으로 파라미터를 조금씩 조정해가는 과정과 유사

### Learning Rate란

- 손실함수가 큰 값일 때 한 번에 크게 조정하면 불안정하므로, 뉴럴넷 모델에 기울기를 **작은 비율로 반영**함 → 그 비율이 Learning rate
- 적절한 값은 모델과 데이터마다 달라 실험을 통해 구함 (예: 0.005배(5e-3), 0.0003배(3e-4))
- Loss-epoch 그래프상 네 가지 패턴: very high learning rate(발산), low learning rate(느린 수렴), high learning rate(불안정하게 수렴), **good learning rate**(빠르고 안정적으로 수렴)

### 너무 낮은 Learning Rate (예: 1e-8)

- Local minimum에 빠져서 Global minimum에 도달할 가능성이 낮아짐 (조금씩만 이동하다 보니 얕은 골짜기에서 멈춰버림)

### 너무 높은 Learning Rate (예: 1e-1)

- 마구 점프를 뛰다 보니 Global minimum으로 딱 맞춰 가기 어려워짐 (계속 오버슈팅하며 튀어다님)
- 미세조정(Fine-tuning)에서는 이미 좋은 시작점(사전학습된 지점)에서부터 시작하기 때문에, **작은 learning rate부터 보수적으로 시작**해야 함
- 비유: "앉아!"를 가르칠 때 너무 큰 교정(큰 learning rate)을 주면 개가 전혀 엉뚱한 반응을 하게 되는 것과 유사 — 원래 잘 학습되어 있던 지식을 오히려 망가뜨릴 수 있음

---

## 4-4. Parameter-Efficient Fine-Tuning (PEFT)

### 배경 - AI 모델의 크기 증가

- 하드웨어 발전, 대규모 데이터 축적, AI 모델 발전에 따라 학습 비용과 모델 용량이 기하급수적으로 증가 (AlexNet 60M → ViT-MoE-15E 14.7B)
- 오픈소스로 공개된 고성능 파운데이션 모델을 출발점으로 Fine-tuning하는 접근이 일반화되었으나, 여전히 높은 비용 → **효율적인 미세조정 방법**이 필요
    - 프롬프트 튜닝(prompt tuning)
    - Adaptor 모듈 추가 학습 (예: LoRA, Low-Rank Adaptation of Large Language Models)

### 프롬프트 디자인 (prompt design)

- 언어모델에서 주로 활용. 모델이 원하는 레벨의 결과를 출력할 수 있도록 입력 텍스트를 변형하는 방법
- 장점: 추가 학습 없이 사전학습된 모델의 예측 성능을 끌어올릴 수 있음
- 단점: 프롬프트를 사람이 직접 설계해야 한다는 부담이 있으며 성능 향상이 제한적
- Text-to-Image 예시: "강아지 사진을 생성해줘" 보다 "캐논 EOS1D로 찍은 듯한 강아지 사진을 생성해줘"처럼 더 구체적인 프롬프트를 줄 때 더 원하는 품질의 결과를 얻음 (모델 자체는 고정, 입력만 바뀜)

### 프롬프트 튜닝 (prompt tuning; 2021)

- 학습 가능한 프롬프트, 즉 **가상 토큰(virtual token)**을 입력에 추가
- 역전파를 통해 오직 가상 토큰에 대한 임베딩만 학습하고, 나머지 모델은 고정(Frozen)
- 장점
    - 사람이 직접 디자인하지 않아도 스스로 프롬프트를 학습할 수 있음
    - 사전학습된 모델을 고정할 수 있어 **지식 손실이 없음** (일반 파인튜닝은 지식 손실 발생 가능)
    - 적은 비용으로 새로운 데이터셋에 대한 모델을 학습할 수 있음
- 학습된 프롬프트는 해석 불가 (그저 숫자 벡터 열일 뿐, 사람이 읽을 수 있는 텍스트가 아님)

**세 가지 방식 비교 (성능 vs 효율)**

- **모델 튜닝(미세조정)**: 파운데이션 모델 자체를 추가 학습 (성능 높음, 비용 큼)
- **프롬프트 튜닝**: 모델은 고정, 학습 가능한 가상 프롬프트만 추가 학습 (성능과 효율의 균형)
- **프롬프트 디자인**: 모델도 고정, 프롬프트도 사람이 직접 작성 (효율 가장 높지만 성능 향상 제한적)

### Adaptor 모듈 추가 학습

- 학습 가능한 작은 모듈(Adapter)을 각 레이어마다 삽입해 **일부 파라미터만 학습**하는 기법
- 기존 모델 블럭(Layer 1~4)은 그대로 두고, 레이어 사이사이에 학습 가능한 파라미터(Adapter)를 추가하여 그 부분만 학습

---

## 4-5. 개인화 모델 예시 - DreamBooth

- 프롬프트 튜닝 응용 사례로, 영상 생성 모델을 개인화하는 방법
- **방법론**: 학습 가능한 토큰(Unique identifier, 예: "[V]")과 모델을 함께 Fine-tuning
    - Fine-Tuning 단계: 입력 {특정 대상의 이미지 3~5장 + 클래스명(예: "dog")} + 사전학습된 Text-to-Image 모델 → DreamBooth → 출력 {고유 식별자 "[V]"} + 개인화된 Text-to-Image 모델
    - Inference 단계: "A [V] dog in the beach"처럼 프롬프트에 고유 식별자를 넣으면, 학습된 특정 대상이 다양한 상황·배경에 자연스럽게 합성된 이미지 생성
- 응용 결과 예시: 화풍 재해석(반 고흐 등 스타일로), 색상 변형(차량 색깔 바꾸기), 하이브리드 생성(다른 동물과 교배한 듯한 이미지)

---

## 4-6. 합성데이터 활용법 1 - Knowledge Distillation (Teacher-Student 학습)

- 질문: 사전학습된 고성능 모델의 지식을 작은 모델에 압축해서 빠르고 효율적으로 만들 수 없을까?
- 지금까지 배운 미세조정과 같은 전이학습(Transfer learning)은 사전학습된 모델과 새로 학습할 모델(타겟 모델)의 **구조가 동일한 경우**를 가정했음 → Knowledge Distillation은 **서로 다른 크기·구조의 모델 사이**에도 지식을 전달할 수 있는 방법

### 지식증류(Knowledge Distillation)란

- 높은 성능의 무거운 모델(**Teacher**, 선생님)을 모방하도록 가벼운 모델(**Student**, 학생)을 학습시키는 방법
- 또는, 크기가 작은 모델(student)만으로 충분히 학습하기 어려운 데이터 특징을 학습하기 위해, 비교적 무겁고 성능이 높은 모델(teacher)의 도움을 받는 기법으로도 볼 수 있음
- 학습 방식: 선생님 모델이 예측한 **soft-label** 값과 학생 모델의 예측 값이 가까워지도록 학습 유도
    - Soft-label: [0, 1] 사이의 모델 예측 확률을 가짜 라벨(정답)처럼 사용
- 구조: 동일한 Training Data가 Teacher Model(사전학습됨)과 Student Model(학습 대상)에 함께 입력 → 두 모델의 Predictions 간 차이(Knowledge Distillation loss)와, Student Predictions와 Ground Truth 간 차이를 함께 줄이도록 학습

### 핵심 포인트

- 작은 모델만으로 학습하는 것보다 큰 모델과 함께 학습하면, 큰 모델에 녹아있는 지식(단순 정답을 넘어선 클래스 간 유사도 등 풍부한 확률분포 정보)까지 같이 학습하게 됨
- 즉 Student는 Ground Truth만 보고 학습할 때보다 Teacher의 soft-label을 통해 더 풍부한 정보를 얻을 수 있음
- → 모델 **경량화**에 활용될 수 있는 대표적인 기법 (앞서 다룬 sVLM 등 경량 모델 개발에도 실제로 쓰이는 접근)

---

## 4-7. 합성데이터 활용법 2 - InstructPix2Pix (2023)

파운데이션 모델들을 **"툴"로 활용**해 새로운 학습 데이터를 자동 생성하는 대표 사례

### 개념

- 명령(지시사항, instruction)에 따라 이미지 편집을 수행하는 모델
- 기존 방법: 입력 이미지와 출력 이미지에 대한 상세 설명이 필요했음
- 본 방법: 입/출력 이미지 상세 설명 없이, **명령만으로** 편집 수행 (예: "Swap sunflowers with roses", "Turn it into a still from a western", "Make his jacket out of leather")

### 학습 데이터 생성 파이프라인 (4단계)

**1단계 - 텍스트 편집 데이터셋 생성**

- 기존 범용 데이터셋은 {이미지, 이미지 설명(캡션)} 형태 → 이를 지시사항(instruction) 기반 이미지 편집 문제로 전환하려면 {편집 지시사항, 편집 전 이미지, 편집 후 이미지} 형식의 학습 데이터셋이 필요
- 먼저 사람이 직접 {Input caption, Edit instruction, Edited caption} 700개를 작성(Human-written)
- 이 상대적으로 작은 사람 작성 데이터셋으로 **GPT-3를 fine-tuning**
- Fine-tuning된 GPT-3는 새로운 입력 캡션이 주어지면 창의적이면서도 그럴듯한 지시사항과 편집 후 캡션을 자동 생성 → 45만 개 이상(GPT-3 generated)으로 데이터셋 확장

**2단계 - 이미지 쌍 생성**

- 1단계에서 생성된 텍스트(캡션) 쌍을 기반으로, 별도의 기존 이미지 생성 모델(**Stable Diffusion + Prompt2Prompt**)을 이용해 이미지 쌍 생성
- Prompt2Prompt를 함께 사용하면 편집 전/후 이미지의 구도·배경 등이 크게 흔들리지 않고 일관되게 유지됨 (With Prompt-to-Prompt가 Without 대비 훨씬 자연스러운 편집 결과를 보여줌)

**3단계 - 학습용 예제 대량 생성**

- 1, 2단계 파이프라인을 반복해 {이미지 편집 지시사항, 편집 전 이미지, 편집 후 이미지} 형태의 학습용 예제를 **45만 개** 생성

**4단계 - 최종 모델 학습(fine-tuning) 및 추론**

- 생성된 이미지-명령 쌍 데이터셋을 기반으로 최종 **Instruction-following Diffusion Model**(InstructPix2Pix)을 fine-tuning
- 추론 시에는 모델이 사람이 작성한 지시사항 하나만으로 실제 이미지를 바로 편집 (예: 진주 귀걸이를 한 소녀 그림 → "turn her into a snake lady")

### 핵심 포인트

- "이미지에 대한 설명을 직접 다는 것"은 비용이 크고 어려운 작업 → 소량(700개)의 사람 작성 예시로 GPT-3를 fine-tuning해 그 패턴대로 지시사항·캡션을 **학습 기반으로 자동 확장 생성**
- 이렇게 생성된 텍스트 쌍은 그 자체로 이미지가 아니라, 별도의 이미지 생성 모델(Stable Diffusion + Prompt2Prompt)에 다시 입력되어야 실제 이미지 쌍이 만들어짐
- 즉 **"텍스트 데이터 자동 생성" → "그 텍스트로 이미지 데이터 생성" → "최종 모델 학습"**의 2단계 합성 데이터 파이프라인

---

## 4-8. 합성데이터 활용법 2' - LLaVA에서의 합성데이터 활용

### LLaVA 학습 데이터 생성 방법

- GPT를 활용해 시각 설명 데이터(visual instruction data) 생성
- 기존에 존재하는 이미지, 캡션, 탐지 데이터셋(정답 데이터)을 활용
- GPT를 이용하여 문제-정답 데이터 쌍을 생성
- 구체적으로: 이미지에 대한 Context type 1: Captions(설명 텍스트)와 Context type 2: Boxes(person, backpack, suitcase 등 객체별 bounding box 좌표)를 GPT에 입력 → GPT가 세 종류의 응답을 생성
    - Response type 1 - conversation: 이미지 내용에 대한 Q&A
    - Response type 2 - detailed description: 상세 설명
    - Response type 3 - complex reasoning: 심화 추론이 필요한 질문-답변
- 즉 GPT는 이미지를 직접 보지 않고, 캡션+박스 좌표라는 텍스트 컨텍스트만으로 학습용 질문-답변 데이터를 자동 생성 — InstructPix2Pix와 마찬가지로 "GPT를 툴로 써서 데이터를 합성"하는 사례

### 실제 활용 예시

- 모나리자 그림에 "이 그림을 그린 사람이 누구인지 알아?"라고 물으면 다빈치 작품임을 정확히 설명하고, 합성 이미지(강아지가 모나리자 드레스를 입은)에 같은 질문을 하면 정확한 작가는 알 수 없지만 다빈치를 참고한 것 같다고 창의적으로 답변 — 이런 대화 능력이 위 방식의 합성 instruction 데이터로 학습된 결과

---

## 4-9. 합성데이터 활용법 3 - 시뮬레이션 기반 합성 데이터

### 개념

- 간단한 시뮬레이션 기반 합성 데이터: 실제 데이터를 모방하거나 새로 생성한 인공 데이터
- 예시: 가상의 이미지, 텍스트, 소리 등을 알고리즘을 통해 생성
- 실제 데이터를 수집하거나 사용하기 어려운 경우에 대체 가능
- 데이터 부족 문제를 해결하고 모델 성능을 개선하는 데 사용

### 예시

- 모션 증폭(motion magnification)용 합성 데이터: 실제 촬영 영상에 알고리즘을 적용해 미세 움직임을 증폭한 합성 데이터 생성
- 객체 탐지 합성 데이터: 서로 다른 이미지의 객체를 다른 배경에 copy-paste하는 방식으로 새로운 조합의 학습 데이터 생성

### 합성 데이터가 특히 유용한 경우 - 데이터 취득이 어려운 문제

- 실제 촬영하기 어려운 움직임이나 환경을 시뮬레이션하여 다양성 높은 데이터를 생성 가능
- 예시) 모션 증폭: 심박수 변화, 건물 진동 등의 미세한 움직임을 합성 데이터를 통해 학습 가능 (예: 잠자는 아기의 미세한 호흡·움직임을 담은 이미지 쌍)

---

## 4-10. 합성데이터 활용 시 유의사항

### 라벨 품질의 불일치 (Label Inconsistency)

- 합성 데이터를 만들 때 사용하는 VLM 또는 LLM 기반 라벨러가 항상 일관적인 기준으로 라벨을 생성하지 않음
- 같은 객체라도 프롬프트 방식·모델 버전·컨텍스트에 따라 라벨 표현이 달라질 수 있음
    - 예: "기타(guitar)" → "electric guitar", "guitar player", "acoustic instrument" 등 서로 다른 라벨
- 개선 방안: 라벨 정규화, 클래스 사전, 교차 모델 검증, 휴먼 검수

### 할루시네이션 (Hallucination)

- 합성 데이터 생성 모델이 실제 존재하지 않는 객체, 특징, 관계를 마음대로 만들어내는 현상
- 실제 세계와 다른 패턴을 학습해 오류와 편향을 강화함
    - 예: 손가락 6개 이미지, 물리적으로 불가능한 그림자가 지속적으로 합성됨
- 개선 방안: 정교한 프롬프트, 후처리 Refiner, 사실성 검증 모델, 실데이터 혼합