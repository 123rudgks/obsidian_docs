[강의 링크](https://www.youtube.com/watch?v=IJw-W7vHIpk)

## 시험 범위에 포함되는 내용

# 자연어 처리 기본 — 3차시: Transformer

# 0. 강의 전체 개요

앞 차시까지의 흐름은 다음과 같았다.

```text
Word Embedding
      ↓
RNN / LSTM
      ↓
Seq2Seq
      ↓
Encoder–Decoder Attention
```

RNN은 단어를 순서대로 처리하면서 이전 문맥을 Hidden State에 누적한다.

하지만 두 가지 큰 문제가 있다.

```text
RNN
├─ 먼 단어 사이 정보가 희석됨
│    → Long-term Dependency
│
└─ 순차 계산이 필수
     → Parallelization 어려움
     → Training 느림
```

Transformer는 **Self-Attention**을 중심으로 이 구조 자체를 바꾼다.

전체 흐름은 다음과 같다.

```text
RNN의 한계
      ↓
Self-Attention
      ↓
Query / Key / Value
      ↓
모든 Token을 동시에 연결
      ↓
문제 1. 순서 정보 없음
      → Positional Encoding

문제 2. 비선형성 부족
      → Feed Forward Network

문제 3. Decoder에서 미래 Token 참조
      → Masked Self-Attention
      ↓
Transformer Block
      ↓
Multi-Head Attention
      ↓
Scaled Dot-Product Attention
      ↓
Residual Connection
      ↓
Layer Normalization
      ↓
Encoder Self-Attention
+
Decoder Masked Self-Attention
+
Encoder–Decoder Attention
      ↓
Linear + Softmax
      ↓
다음 Token Probability
```

---

# 1. 왜 RNN을 대체하려 했는가?

RNN에서는

`h_t`

를 계산하기 위해

`h_(t-1)`

이 먼저 필요하다.

즉

```text
h₁
 ↓
h₂
 ↓
h₃
 ↓
h₄
```

순서대로 계산해야 한다.

이 구조가 RNN의 장점이면서 동시에 한계다.

---

# 2. RNN 문제 ① Long-term Dependency

문장이 길어지면 멀리 떨어진 단어 사이의 관계를 파악해야 할 수 있다.

예를 들어 문장 초반의

```text
chef
```

와 아주 뒤에서 다시 등장하는 표현이 같은 사람을 의미하는지 판단해야 할 수 있다.

RNN에서는 정보가

```text
h₁ → h₂ → h₃ → ... → hₜ
```

를 거치면서 전달된다.

따라서 멀어질수록 정보가 희석될 수 있다.

LSTM이 이를 개선하지만 강의에서는 **물리적으로 긴 경로를 거치는 문제 자체가 완전히 없어지는 것은 아니다**라고 설명한다.

---

# 3. RNN 문제 ② 순차 계산

RNN에서는

```text
h_(t-1)
```

을 계산하기 전에는

```text
h_t
```

를 계산할 수 없다.

따라서 한 문장의 모든 Token을 동시에 처리하기 어렵다.

---

# 4. 왜 Training Speed가 중요한가?

단순히

> “학습에 몇 시간 더 걸리는 것이 싫다.”

의 문제가 아니다.

Training이 느리면 사용할 수 있는 Data의 양 자체가 제한된다.

```text
Training 느림
      ↓
더 큰 Dataset 사용 어려움
      ↓
Model Scaling의 병목
```

이 된다.

강의에서는 이후 방대한 Web Data까지 학습하고 싶은 상황을 생각하면 이러한 병목이 더욱 중요해진다고 설명한다.

---

# 5. Transformer의 핵심 — Self-Attention

Transformer는 **Self-Attention**을 중심으로 구성된다.

여기서 `Self`는

> **같은 Sequence 안의 Token들끼리 수행하는 Attention**

이라는 의미다.

---

# 6. 앞 차시 Attention과 Self-Attention의 차이

Seq2Seq에서 배운 Attention은

```text
Decoder
   ↓ Query
Encoder Hidden States
   ↓
필요한 Encoder 정보 검색
```

이었다.

즉 **Encoder와 Decoder 사이의 Attention**이었다.

Self-Attention은

```text
Encoder 내부 Token ↔ Encoder 내부 Token
```

또는

```text
Decoder 내부 Token ↔ Decoder 내부 Token
```

사이에서 일어난다.

---

# 7. Self-Attention은 RNN Attention을 단순히 교체한 것이 아니다

중요하다.

Transformer의 Self-Attention은 기존 Seq2Seq의 Encoder–Decoder Attention만 대체한 것이 아니다.

오히려

> **RNN으로 Sequence 정보를 누적하던 Architecture 자체를 Self-Attention으로 대체한다.**

는 것이 핵심이다.

---

# 8. 순차 전달 대신 직접 참조한다

RNN에서는 어떤 Token의 Representation을 만들 때 이전 Hidden State를 거쳐 정보를 전달받았다.

Transformer에서는 현재 Token이 Sequence의 다른 Token들을 **직접 참조**한다.

예:

```text
"어제 카페에 갔었어. 거기 사람이 많더라."
```

`거기`라는 Token의 표현을 만들 때

```text
어제
카페
갔었어
거기
사람
많더라
```

등 전체 Token 중 관련 있는 정보를 직접 참고할 수 있다.

---

# 9. Self-Attention의 핵심 아이디어

각 Token에 대해 묻는다.

> **“이 Token의 새로운 표현을 만들 때 문장 안의 다른 Token들을 각각 얼마나 참고해야 하는가?”**

그리고 그 중요도를 Weight로 만들어 다른 Token 정보를 Weighted Sum한다.

---

# 10. 왜 병렬처리가 가능한가?

`어제`의 새로운 Representation을 계산하는 것과

`카페`의 새로운 Representation을 계산하는 것이 서로 순차적으로 의존하지 않는다.

따라서

```text
어제   ─┐
카페   ─┤
갔었어 ─┤ → 동시에 Self-Attention
거기   ─┤
사람   ─┤
많더라 ─┘
```

처럼 Matrix Operation으로 동시에 처리할 수 있다.

---

# 11. Self-Attention의 준비물 — Token Embedding

각 Token은 먼저 Embedding Vector를 가진다.

강의에서는 Original Transformer를 설명하면서 예시 Dimension으로

```text
Embedding Dimension = 512
```

를 사용한다.

즉 One-hot Vocabulary Vector를 그대로 사용하지 않고 Embedding Layer를 통해 저차원 Dense Vector로 변환한다.

---

# 12. 하나의 Embedding에서 세 가지 역할을 만든다

각 Token Embedding으로부터 세 Vector를 만든다.

```text
Query
Key
Value
```

이를 줄여서

```text
Q
K
V
```

라고 한다.

---

# 13. Query

Query는

> **현재 Token이 어떤 정보를 찾고 있는가**

를 나타내는 역할을 한다.

쉽게 말하면

```text
"내 표현을 새롭게 만들려는데
어떤 정보가 나와 관련 있는가?"
```

를 묻는 Vector다.

---

# 14. Key

Key는

> **각 Token이 Query와 얼마나 관련 있는지를 비교하기 위한 표현**

이다.

Query가 질문이라면 Key는 검색 대상의 색인과 비슷한 역할을 한다.

---

# 15. Value

Value는

> **Attention Weight가 정해진 뒤 실제로 가져올 정보**

다.

즉

```text
Query × Key
→ 얼마나 참고할지 결정

그 Weight × Value
→ 실제 정보 취합
```

으로 역할을 구분한다.

---

# 16. Q/K/V는 어떻게 만드는가?

Token Embedding을 `x`라고 하면 각각 별도의 학습 가능한 Matrix를 사용한다.

```text
Q = xW_Q
K = xW_K
V = xW_V
```

강의의 512 Dimension 예에서는 각각의 Matrix가 해당 Dimension을 변환하도록 설계된다.

중요한 것은

> **W_Q, W_K, W_V가 Trainable Parameter**

라는 것이다.

---

# 17. 같은 원래 Token이라도 Q/K/V는 다르다

같은 Embedding에서 출발하지만

```text
W_Q
W_K
W_V
```

가 서로 다르므로

```text
Query Representation
Key Representation
Value Representation
```

도 서로 다르게 학습된다.

즉 하나의 Token을 용도에 따라 세 가지 관점으로 표현한다.

---

# 18. 왜 Key와 Value를 구분하는가?

앞 차시의 Seq2Seq Attention에서는 Encoder Hidden State 하나가

- Attention Score 계산
    
- 실제 가져올 정보
    

두 역할을 모두 수행했다.

Transformer에서는 이를 명시적으로

```text
Key
→ 관련도 계산용

Value
→ 정보 전달용
```

으로 분리한다.

---

# 19. Self-Attention Step 1 — Query와 모든 Key 비교

Token `i`의 새로운 Representation을 만들고 싶다고 하자.

그러면

`Q_i`

와 Sequence에 존재하는 모든

```text
K₁
K₂
K₃
...
Kₙ
```

을 비교한다.

---

# 20. 기본적인 Attention Score

가장 기본적인 방법은 Dot Product다.

`score(i,j) = Q_i · K_j`

이다.

즉

```text
현재 Token Query
×
j번째 Token Key
      ↓
Scalar Score
```

가 나온다.

---

# 21. 자기 자신도 Attention한다

Self-Attention에서는 다른 Token뿐 아니라 **자기 자신도 포함**한다.

예를 들어 `카페` Token은

```text
카페 ↔ 어제
카페 ↔ 카페
카페 ↔ 갔었어
카페 ↔ 거기
...
```

처럼 모든 Token을 비교할 수 있다.

---

# 22. Softmax로 Weight 만들기

Score들을 그대로 사용하지 않고 Softmax를 적용한다.

```text
score₁
score₂
score₃
...
     ↓
Softmax
     ↓
α₁
α₂
α₃
...
```

이 값들은 각 Token을 얼마나 참고할지 나타내는 **Attention Weight**가 된다.

---

# 23. Attention Weight의 의미

예를 들어 `카페`라는 Token의 표현을 만들 때

```text
카페       0.40
갔었어     0.30
어제       0.15
거기       0.10
사람       0.05
```

같은 Weight가 학습될 수 있다.

이는 설명을 위한 예이며 실제 Attention Pattern은 Data와 Task에 의해 결정된다.

---

# 24. Self-Attention Step 2 — Value Weighted Sum

Attention Weight를 구한 다음 Value Vector를 Weighted Sum한다.

개념적으로

`o_i = Σ_j α_ij V_j`

이다.

즉

```text
α₁V₁
+
α₂V₂
+
...
+
αₙVₙ
      ↓
새로운 Token Representation
```

을 만든다.

---

# 25. Query / Key / Value를 한 문장으로

```text
Query
→ 무엇을 찾고 있는가?

Key
→ 내가 찾는 것과 얼마나 관련 있는가?

Value
→ 실제로 가져올 정보는 무엇인가?
```

---

# 26. 모든 Token이 동시에 이 작업을 한다

Token 하나만 Query가 되는 것이 아니다.

Sequence의 모든 Token이 각각 Query가 된다.

```text
Token 1 Query
→ 모든 Key 검색

Token 2 Query
→ 모든 Key 검색

Token 3 Query
→ 모든 Key 검색

...
```

각 결과로 새로운 Token Representation을 얻는다.

---

# 27. Self-Attention Matrix 관점

실제로 구현에서는 Token별로 하나씩 계산하기보다

```text
Q Matrix
K Matrix
V Matrix
```

로 묶어 한 번에 계산한다.

이것이 Transformer가 GPU 같은 병렬 Matrix 연산 장치에서 효율적으로 동작할 수 있는 이유 중 하나다.

---

# 28. 기본 Self-Attention에는 문제점이 있다

강의에서는 순수한 Self-Attention만 사용하면 세 가지 큰 문제가 있다고 설명한다.

```text
1. Sequence Order 정보가 없음

2. Non-linearity가 부족함

3. Decoder에서 Future Token을 볼 수 있음
```

Transformer에서는 각각 별도의 장치를 추가해 해결한다.

---

# 29. 문제 ① Self-Attention에는 순서 개념이 없다

Query와 Key의 Dot Product 자체에는

```text
첫 번째 Token
두 번째 Token
세 번째 Token
```

이라는 정보가 없다.

즉 같은 Token 집합이 있다면 순서를 바꾸어도 Self-Attention 연산 자체는 위치를 자동으로 알지 못한다.

---

# 30. NLP에서 순서가 중요한 이유

예:

```text
I love you
```

와

```text
You love I
```

는 같은 Token들을 포함하지만 의미와 문법이 다르다.

따라서 Token의 Semantic Representation만 있어서는 충분하지 않다.

---

# 31. 해결 — Positional Encoding

각 Token에

```text
Semantic Information
+
Position Information
```

을 함께 제공한다.

즉

`Input Representation = Word Embedding + Positional Encoding`

으로 만든다.

---

# 32. 좋은 Position Representation의 조건

강의에서는 두 가지 직관을 제시한다.

### 1. 서로 다른 위치는 서로 다른 표현을 가져야 한다

```text
Position 1 ≠ Position 2 ≠ Position 3
```

이어야 한다.

### 2. 가까운 위치는 어느 정도 비슷한 관계를 표현할 수 있어야 한다

```text
Position 1 ↔ Position 2
```

가

```text
Position 1 ↔ Position 100
```

보다 위치적으로 가까운 관계를 표현할 수 있으면 좋다.

---

# 33. One-hot Position의 한계

Position마다 One-hot을 사용하는 것도 서로 다른 위치를 구분한다는 첫 조건은 만족한다.

하지만

```text
Position 1
Position 2
```

와

```text
Position 1
Position 100
```

사이의 거리 구조가 동일하게 취급될 수 있다.

이는 Word One-hot의 한계와 비슷하다.

---

# 34. Sinusoidal Positional Encoding

Original Transformer에서는 강의 설명상 Sin/Cos 같은 주기함수를 이용해 Position Vector를 인위적으로 구성하는 방법을 소개한다.

핵심은 공식을 암기하는 것이 아니라

> **각 Position은 고유하면서도 상대적인 위치 관계가 Vector에 반영되도록 만든다.**

는 점이다.

---

# 35. Learned Positional Embedding

Position Vector를 사람이 함수로 정하는 대신 학습할 수도 있다.

```text
Position ID
      ↓
Trainable Embedding
      ↓
Position Vector
```

즉 Loss가 감소하도록 Position Representation 자체를 학습한다.

강의에서는 고정형 Positional Encoding과 Learned Positional Embedding 모두 가능한 접근으로 설명한다.

---

# 36. Word Embedding과 Position Vector를 더한다

Token의 Semantic Embedding:

`x_i`

Position Representation:

`p_i`

라면

`x̃_i = x_i + p_i`

와 같이 더해서 Transformer Block의 Input으로 사용한다.

---

# 37. Vector를 더한다고 정보가 사라지는가?

강의에서는 Scalar Addition과 Vector Addition을 구분한다.

Vector는 공간상의 방향과 크기를 가지는 Representation이다.

두 Vector를 더하면 두 방향을 반영한 새로운 Vector가 만들어진다.

따라서 단순히

> “두 숫자를 더해서 원래 정보가 완전히 사라진다.”

는 식으로 이해하면 안 된다.

---

# 38. 문제 ② Self-Attention만 반복하면 비선형성이 부족하다

Attention Output은 Value Vector들의 Weighted Sum이다.

즉 기본적으로

```text
V₁
V₂
V₃
...
```

의 Linear Combination이다.

강의에서는 Self-Attention만 계속 반복해서는 Deep Neural Network에서 기대하는 충분한 Non-linearity가 추가되지 않는다고 설명한다.

---

# 39. 해결 — Feed Forward Network

각 Self-Attention 뒤에 **Feed Forward Neural Network(FFN)** 를 배치한다.

```text
Self-Attention
      ↓
Feed Forward Network
      ↓
Non-linearity 추가
```

---

# 40. Position-wise Feed Forward

동일한 Feed Forward Network를 Sequence의 각 Token에 독립적으로 적용한다.

```text
Token 1 → 같은 FFN
Token 2 → 같은 FFN
Token 3 → 같은 FFN
...
```

즉 한 Transformer Layer 내부에서는 Token마다 별도 Network를 만드는 것이 아니라 같은 FFN Parameter를 공유한다.

---

# 41. Feed Forward Network의 구조

강의에서는 예를 들어

```text
512
 ↓
1024
 ↓
Non-linear Activation
 ↓
512
```

처럼 Dimension을 확장했다 다시 원래 Dimension으로 줄이는 구조를 설명한다.

핵심은

```text
Linear
→ Non-linear Activation
→ Linear
```

을 통해 Non-linearity를 추가하는 것이다.

---

# 42. Transformer Block이 깊어지는 방식

하나의 Block이

```text
Self-Attention
+
Feed Forward
```

로 구성된다면 이 Block을 여러 개 쌓는다.

```text
Block 1
 ↓
Block 2
 ↓
Block 3
 ↓
...
```

각 Block은 별도의 Trainable Parameter를 가진다.

즉 Layer 사이에서 `W_Q`, `W_K`, `W_V`, FFN Parameter를 그대로 공유하는 구조가 아니다.

---

# 43. 문제 ③ Decoder의 Future Token 참조

Language Generation에서는 현재 Token을 예측할 때 미래 Token을 알 수 없다.

예를 들어

```text
<SOS> The cat ...
```

에서 `cat`을 생성하기 전에 뒤에 나올 Token을 Attention하면 정답을 미리 보는 셈이다.

---

# 44. 해결 — Masked Self-Attention

Decoder에서는 현재 Position보다 미래에 있는 Token의 Attention Score를 강제로 막는다.

```text
현재까지의 Token
→ 참조 가능

미래 Token
→ 참조 불가능
```

이를 **Masked Self-Attention**이라고 한다.

---

# 45. Mask는 어떻게 구현하는가?

미래 Position의 Attention Score에 매우 작은 값, 강의에서는 개념적으로

`-∞`

를 넣는다.

그 다음 Softmax를 수행한다.

`e^(-∞) ≈ 0`

이므로 해당 Position의 Attention Probability가 거의 0이 된다.

---

# 46. Encoder와 Decoder Self-Attention 차이

## Encoder

전체 Input Sentence가 이미 주어져 있다.

따라서

```text
앞 Token
현재 Token
뒤 Token
```

모두 Attention 가능하다.

## Decoder

Text를 하나씩 생성한다.

따라서 현재까지 생성된 Token만 볼 수 있다.

```text
Past + Current
→ Attention 가능

Future
→ Mask
```

---

# 47. Transformer에서도 Training은 일반 Neural Network와 같다

최종 Prediction과 정답 Token을 비교해 Loss를 계산한다.

```text
Forward
 ↓
Prediction
 ↓
Loss
 ↓
Backpropagation
 ↓
Gradient Descent
```

구조는 기존 Neural Network와 동일하다.

---

# 48. Teacher Forcing도 사용한다

앞 Seq2Seq에서 배운 Teacher Forcing 개념도 Transformer Decoder Training에 연결된다.

Training에서는 이전 정답 Token들이 주어진 상태에서 다음 Token을 예측하도록 할 수 있다.

따라서 각 Position의 다음 Token Prediction을 병렬적으로 학습하기 용이해진다.

---

# 49. 기본 Transformer Module의 큰 구조

강의 흐름을 단순화하면

```text
Token Embedding
      +
Positional Encoding
      ↓
Self-Attention
      ↓
Feed Forward Network
      ↓
다음 Transformer Block
```

이다.

Decoder에서는 Self-Attention 대신 **Masked Self-Attention**을 사용한다.

---

# 50. Transformer의 추가 개선 ① Multi-Head Attention

Attention을 한 번만 수행하지 않고 여러 개의 **Head**로 나누어 수행한다.

이를 **Multi-Head Attention**이라고 한다.

---

# 51. 왜 Attention Head를 여러 개 두는가?

하나의 Token이 다른 Token과 맺는 관계에는 한 종류만 있는 것이 아니다.

예를 들어 어떤 Head는

- 주어와 동사 관계
    

에 집중할 수 있고,

다른 Head는

- 명사와 관련 표현
    

또 다른 Head는

- 주변 Local Context
    

같은 정보를 학습할 수도 있다.

강의의 핵심은

> **Attention 관계에는 하나의 정답이 없으므로 여러 관점에서 관계를 학습할 수 있게 한다.**

는 것이다.

---

# 52. Head마다 별도의 Q/K/V Parameter

각 Head는 서로 다른

```text
W_Q
W_K
W_V
```

를 가진다.

따라서 Head마다 서로 다른 Attention Pattern을 학습할 수 있다.

---

# 53. 전체 Dimension을 Head별로 나눈다

강의에서는 Original Transformer 예로

```text
Model Dimension = 512
Head = 8
```

이면 Head 하나당

```text
512 / 8 = 64
```

Dimension의 Q/K/V를 사용하는 방식을 설명한다.

---

# 54. 왜 Dimension을 나누는가?

Head가 8개라고 해서 각 Head마다 512 Dimension을 그대로 사용하면 Parameter와 계산량이 크게 증가한다.

대신

```text
8 Heads × 64 Dimensions
```

로 나누면 전체 규모를 관리하면서 여러 Attention을 병렬로 사용할 수 있다.

---

# 55. 여러 Head의 결과는 다시 합친다

각 Head에서

```text
Attention Head 1
Attention Head 2
...
Attention Head H
```

의 Output을 구한 뒤 합쳐서 다시 하나의 Representation으로 만든다.

즉

```text
한 관계만 보는 것
      ↓
여러 관계를 병렬 탐색
      ↓
결과 통합
```

이다.

---

# 56. Transformer의 추가 개선 ② Scaled Dot-Product Attention

기본 Attention Score는

`QKᵀ`

와 같은 Dot Product다.

하지만 Vector Dimension이 커질수록 Dot Product의 크기도 커질 수 있다.

---

# 57. Dot Product가 너무 커지면 생기는 문제

큰 Score를 Softmax에 넣으면 Output Distribution이 지나치게 극단적으로 변할 수 있다.

```text
0.9999
0.0001
0.0000
...
```

처럼 일부 위치에 너무 몰릴 수 있다.

강의에서는 Attention이 항상 특정 한두 Token만 고르는 개념이 아니라 **모든 Token에 연속적인 중요도를 배분하는 것**임을 강조한다.

---

# 58. Scale Factor

이를 완화하기 위해 Dot Product를 Query/Key Dimension의 Square Root로 나눈다.

`Attention(Q,K,V) = softmax(QKᵀ / √d_k)V`

이다.

---

# 59. 예: Head Dimension이 64라면

`d_k = 64`

이므로

`√64 = 8`

이다.

따라서 Dot Product Score를 `8`로 나누어 Scale을 조정한다.

강의에서는 이를 통해 Softmax가 지나치게 극단적인 Distribution이 되는 것을 완화한다고 설명한다.

---

# 60. Scaled Dot-Product Attention 핵심 구조

```text
Q × Kᵀ
 ↓
√d_k로 나눔
 ↓
Softmax
 ↓
Attention Weight
 ↓
× V
 ↓
Attention Output
```

이것이 Transformer Attention의 핵심 Formula다.

---

# 61. Transformer의 추가 개선 ③ Residual Connection

각 Layer의 Output만 다음 Layer로 넘기지 않고 원래 Input을 함께 더한다.

개념적으로

`Output = F(x) + x`

이다.

이를 **Residual Connection / Skip Connection**이라고 한다.

---

# 62. Residual Connection의 Forward 장점

Layer가 만든 Representation만 사용하는 것이 아니라 기존 Representation을 직접 다음 단계에 전달한다.

따라서 기존 정보를 보존하는 통로가 생긴다.

---

# 63. Residual Connection의 Backpropagation 장점

Gradient 역시 복잡한 Layer만 거치는 것이 아니라 Skip Path를 통해 보다 직접적으로 전달될 수 있다.

따라서 강의에서는

> **Vanishing Gradient 문제 완화**

와 연결한다.

---

# 64. Position 정보 유지와 Residual

처음 Input에는

```text
Word Embedding
+
Position Encoding
```

이 들어 있다.

여러 Self-Attention Layer를 거치면서 Position 정보가 희석될 수 있지만 Residual Connection을 통해 초기 Representation이 계속 전달되면서 위치 정보가 유지되는 효과도 있다고 강의에서 설명한다.

---

# 65. Transformer의 추가 개선 ④ Layer Normalization

Layer를 계속 거치면서 Representation 값의 Distribution이 지나치게 커지거나 작아지면 Training이 불안정해질 수 있다.

**Layer Normalization**은 각 Layer의 Representation Scale을 안정적으로 유지하기 위한 방법으로 소개된다.

---

# 66. Layer Normalization의 목적

강의의 핵심 설명은

```text
Layer 통과
 ↓
Representation 분포 변동
 ↓
Training 불안정 가능
 ↓
Normalization
 ↓
더 안정적인 범위 유지
```

다.

---

# 67. Encoder Block

강의 내용을 큰 구조로 묶으면 Encoder Block은 다음과 같다.

```text
Input Representation
      ↓
Multi-Head Self-Attention
      ↓
Residual + Normalization
      ↓
Feed Forward Network
      ↓
Residual + Normalization
      ↓
다음 Encoder Block
```

이 구조를 여러 번 반복한다.

---

# 68. Decoder Block

Decoder에는 하나가 더 들어간다.

```text
Decoder Input
      ↓
Masked Multi-Head Self-Attention
      ↓
Residual + Norm
      ↓
Encoder–Decoder Attention
      ↓
Residual + Norm
      ↓
Feed Forward
      ↓
Residual + Norm
```

---

# 69. Decoder의 두 번째 Attention

Decoder에서

```text
Masked Self-Attention
```

다음에 또 하나의 Attention이 존재한다.

이것은 Self-Attention이 아니라 앞 차시의 Seq2Seq Attention과 비슷한 **Encoder–Decoder Attention**이다.

---

# 70. Encoder–Decoder Attention의 역할

Decoder는 현재 Target Language Context를 가지고 있다.

하지만 번역하려면 Source Sentence의 어느 부분이 현재 생성에 필요한지도 찾아야 한다.

```text
Decoder
→ "지금 어떤 Source 정보가 필요하지?"

Encoder
→ 관련된 Source Representation 제공
```

한다.

---

# 71. Cross-Attention에서 Q/K/V의 출처

강의에서는 다음처럼 설명한다.

### Query

Decoder Representation에서 온다.

### Key

Encoder Representation에서 온다.

### Value

Encoder Representation에서 온다.

즉

```text
Q = Decoder

K,V = Encoder
```

다.

---

# 72. Self-Attention과 Cross-Attention 비교

|Self-Attention|Encoder–Decoder Attention|
|---|---|
|같은 Sequence 내부|Encoder와 Decoder 사이|
|Q/K/V가 같은 쪽 Representation에서 생성|Q는 Decoder, K/V는 Encoder|
|Token 간 Context 형성|Source 정보 검색|
|Encoder 또는 Decoder 내부|Decoder Block 내부|

---

# 73. 번역에서 Cross-Attention의 직관

영어 → 프랑스어 번역이라면

```text
Decoder의 프랑스어 Context
      ↓ Query

Encoder의 영어 Representations
      ↓ Key / Value

관련 영어 정보 검색
      ↓
프랑스어 다음 Token 생성
```

이다.

---

# 74. Source와 Target 정보를 어떻게 합치는가?

Cross-Attention Output은 Encoder 쪽 Value들의 Weighted Sum이므로 Source 정보를 담는다.

Decoder에는 이미 Target Language Representation이 있다.

Residual Connection 등을 통해 두 Representation이 함께 사용되면서 Source와 Target Context가 결합된다.

강의에서는 이를 Seq2Seq에서 `S`와 Attention Output `A`를 함께 사용했던 구조와 연결한다.

---

# 75. Transformer 최종 출력

Decoder의 마지막 Representation은 아직 Vocabulary Probability가 아니다.

먼저 Linear Transformation을 수행한다.

```text
Hidden Dimension
      ↓ Linear
Vocabulary Dimension
```

으로 바꾼다.

그다음 Softmax를 적용한다.

```text
Vocabulary Logits
      ↓
Softmax
      ↓
Next-token Probability
```

---

# 76. 생성 방식은 앞 차시와 연결된다

Probability Distribution을 얻은 뒤

- Greedy Decoding
    
- Beam Search
    

등을 사용할 수 있다.

즉 **Text Generation 방법 자체는 Seq2Seq에서 배운 개념과 연결**된다.

---

# 77. Transformer Training은 병렬이지만 생성은 순차적일 수 있다

매우 중요한 구분이다.

## Training

Teacher Forcing과 Mask를 이용하여 여러 Position의 계산을 병렬화할 수 있다.

## Autoregressive Inference

다음 Token은 직전까지 생성된 Token에 의존하므로

```text
Token 1
 ↓
Token 2
 ↓
Token 3
```

처럼 순차적으로 생성된다.

즉

> **Transformer가 RNN보다 병렬적이라는 말이 모든 상황에서 Token Generation까지 완전히 동시에 된다는 뜻은 아니다.**

---

# 78. Transformer와 RNN의 가장 큰 구조 차이

## RNN

```text
Token 1
 ↓
Token 2
 ↓
Token 3
 ↓
Token 4
```

정보를 순차적으로 전달한다.

## Transformer

```text
Token 1 ↔ Token 2
   ↕       ↕
Token 3 ↔ Token 4
```

Self-Attention을 통해 Token 사이에 직접적인 연결을 만들 수 있다.

강의는 이러한 All-to-All Connection 가능성을 Transformer의 중요한 특성으로 설명한다.

---

# 79. 긴 의존관계 처리에서의 장점

RNN에서 첫 Token의 정보가 마지막 Token까지 도달하려면 긴 Recurrent Path를 거쳐야 한다.

Self-Attention에서는 두 Token이 멀리 떨어져 있어도 한 Layer 안에서 직접 Attention할 수 있다.

```text
RNN

Token 1 → 2 → 3 → ... → 100


Self-Attention

Token 1 ─────────────→ Token 100
```

따라서 Long-range Relationship을 직접 모델링할 수 있다.

---

# 80. Transformer가 대규모 학습에 적합한 이유 ① 병렬성

RNN은 Sequence Direction의 Dependency 때문에 병렬화가 어렵다.

Transformer는 한 Layer의 Token Representation을 Matrix Operation으로 동시에 계산할 수 있다.

따라서 대규모 Data Training에서 훨씬 효율적인 구조가 될 수 있다.

강의 후반에서는 이전 RNN 계열과 비교했을 때 Transformer가 **성능뿐 아니라 계산 효율성에서도 장점**을 보인다고 정리한다.

---

# 81. 대규모 학습에 적합한 이유 ② 반복 가능한 Module

RNN을 깊게 만들 때는

- Layer 수
    
- Hidden Dimension
    
- 각 구조
    

등 많은 Architecture Design이 필요하다.

Transformer는 강의 설명상

> **입력과 출력 Dimension을 유지하는 유사한 Block을 반복적으로 쌓기 쉽다.**

는 점도 Scaling에 유리하다.

---

# 82. Transformer Block의 Parameter는 Layer마다 다르다

구조 모양은 반복되지만 Parameter까지 동일한 것은 아니다.

예:

```text
Block 1
W_Q¹, W_K¹, W_V¹

Block 2
W_Q², W_K², W_V²
```

처럼 각 Layer에서 서로 다른 관계를 학습할 수 있다.

---

# 83. Layer마다 다른 Attention Pattern을 학습할 수 있다

강의에서는 깊은 Transformer를 이해하는 직관으로, Layer마다 서로 다른 연결 Pattern이 나타날 수 있다고 설명한다.

초기 Layer에서는 가까운 Token이나 이전 Token을 많이 볼 수 있고,

다른 Layer에서는 Phrase 수준 관계,

더 깊은 Layer에서는 넓은 Context를 통합하는 형태가 나타날 수 있다는 식의 conceptual explanation을 제시한다.

---

# 84. RNN 같은 연결도 학습될 수 있다

Attention이 주로 바로 이전 Token에 높은 Weight를 둔다면 Information Flow가 RNN과 유사하게 보일 수 있다.

```text
x₁ ← x₂ ← x₃ ← x₄
```

형태다.

---

# 85. CNN 같은 Local Pattern도 가능하다

각 Token이 주변 Token에 높은 Attention을 준다면

```text
Local Neighborhood
```

의 정보를 취합하는 CNN과 유사한 Pattern처럼 볼 수도 있다.

---

# 86. Global Aggregation도 가능하다

특정 Token이 Sequence 전체에 넓게 Attention한다면 여러 Token 정보를 한꺼번에 통합할 수도 있다.

강의에서는 이를 MLP적인 전체 정보 통합과 비슷한 직관으로 설명한다.

---

# 87. 강의에서 제시하는 Transformer의 높은 수준 해석

강의 후반에서는 Transformer를 고정적으로

```text
RNN
CNN
MLP
```

중 하나라고 보기보다는

> **Attention Weight를 어떻게 학습하느냐에 따라 Data에 적합한 연결 구조를 만들어내는 매우 유연한 Architecture**

라고 설명한다.

주의할 점은 이것을 Transformer의 표준 정의로 외우기보다는 **강의자가 Transformer의 유연성을 설명하기 위해 제시한 관점**으로 이해하는 것이다.

---

# 88. “Neural Architecture Search처럼 볼 수 있다”는 강의의 관점

강의에서는 이러한 유연성을 강조하면서 Transformer를 일종의 **Neural Architecture Search와 비슷하게 생각해볼 수 있다**는 설명도 한다.

즉 Attention Pattern에 따라 실질적인 Information Flow가 Data에 맞게 달라진다는 직관이다.

이 역시 Transformer의 정식 정의라기보다 **강의의 conceptual framing**으로 기억하는 것이 적절하다.

---

# 89. 왜 현대 Language Model의 기반이 되었는가?

강의의 큰 결론은 다음과 같다.

```text
병렬 Training 가능
      +
Long-range Interaction
      +
동일한 Block의 반복적 Scaling
      +
유연한 Attention Pattern
      ↓
대규모 Data와 대규모 Model에 적합
      ↓
현대 Language Model의 핵심 Architecture
```

---

# 90. Self-Attention 전체 계산

Token Representation Matrix를 `X`라 하면 구조적으로

```text
Q = XW_Q
K = XW_K
V = XW_V
```

다.

Attention Score:

`QKᵀ`

Scaling:

`QKᵀ / √d_k`

Softmax:

`A = softmax(QKᵀ / √d_k)`

Output:

`O = AV`

이다.

---

# 91. 이 수식을 구조적으로 읽는 법

수식을 암기하기 전에 이렇게 읽는다.

```text
QKᵀ
→ 누가 누구를 얼마나 볼까?

Softmax
→ 중요도를 Weight로 변환

× V
→ 그 Weight만큼 실제 정보 가져오기
```

즉

> **관련성을 계산하고 → 중요도를 만들고 → 그 중요도만큼 정보를 합친다.**

---

# 92. Query/Key와 Value를 혼동하면 안 된다

Attention Weight 자체를 구하는 데 핵심적으로 쓰이는 것은

```text
Query
Key
```

다.

최종 Context Vector를 만드는 데 사용되는 것은

```text
Value
```

다.

```text
Q × K
→ 어디를 볼지

Weight × V
→ 무엇을 가져올지
```

이다.

---

# 93. Attention은 하나의 Token을 골라내는 Hard Selection이 아니다

강의에서는 이 점도 강조한다.

Attention은 일반적으로

```text
Token A = 중요
나머지 = 필요 없음
```

이라는 이진 선택이 아니다.

각 Token에

```text
0.03
0.11
0.36
0.18
...
```

처럼 연속적인 Weight를 부여한다.

따라서 여러 Token 정보를 동시에 이용할 수 있다.

---

# 94. Multi-Head는 이 Weight Distribution을 여러 개 만드는 것

Head마다 다른 Q/K/V Projection을 사용하므로

```text
Head 1
→ Attention Distribution A

Head 2
→ Attention Distribution B

Head 3
→ Attention Distribution C
```

처럼 서로 다른 관계를 동시에 볼 수 있다.

---

# 95. Attention vs Self-Attention

|항목|Seq2Seq Attention|Self-Attention|
|---|---|---|
|Query|Decoder State|현재 Sequence Token|
|검색 대상|Encoder Hidden States|같은 Sequence의 Token들|
|목적|Decoder에 필요한 Source 정보|Token의 Context-aware Representation|
|RNN 필요|기존 구조에서는 사용|Transformer에서는 RNN 대체|
|계산|Decoder Step별|Token별 병렬 가능|

---

# 96. Self-Attention vs Masked Self-Attention

|Self-Attention|Masked Self-Attention|
|---|---|
|전체 Sequence 참조 가능|미래 Position 참조 불가|
|Encoder에서 사용|Autoregressive Decoder에서 사용|
|양쪽 Context 사용 가능|Past/Current Context만 사용|
|Mask 없음|Future Score에 Mask|

---

# 97. Positional Encoding이 필요한 이유

Self-Attention은 Token 관계는 계산하지만 그 Token이

```text
1번째인지
2번째인지
100번째인지
```

자체는 알지 못한다.

따라서 별도의 Position Information을 제공한다.

---

# 98. Feed Forward가 필요한 이유

Attention Output은 다른 Token Value들의 Weighted Sum이다.

따라서 Self-Attention 사이에 Non-linear Neural Network를 넣어 더 복잡한 Representation을 학습하도록 한다.

---

# 99. Residual이 필요한 이유

```text
복잡한 Transformation
+
기존 Representation 보존
```

을 동시에 가능하게 하고 Gradient Flow를 개선하기 위한 구조다.

---

# 100. Layer Normalization이 필요한 이유

깊은 Layer를 반복할 때 Representation Scale을 안정적으로 유지하여 Training을 보다 안정적으로 만드는 역할이다.

---

# 101. Multi-Head가 필요한 이유

하나의 Attention Pattern에 모든 관계를 맡기는 대신 여러 Projection Space에서 다양한 관계를 동시에 학습하기 위한 것이다.

---

# 102. Scaling이 필요한 이유

Query/Key Dimension이 커질수록 Dot Product Magnitude가 커지고 Softmax가 지나치게 Sharp해질 수 있으므로

`√d_k`

로 나누어 안정화한다.

---

# 103. Encoder Self-Attention이 하는 일

각 Source Token이 Source Sentence 전체 정보를 참조해서 **Context-aware Source Representation**을 만든다.

---

# 104. Decoder Self-Attention이 하는 일

현재까지 생성된 Target Token들을 이용해 **Target-side Context Representation**을 만든다.

다만 미래 Token은 Mask한다.

---

# 105. Cross-Attention이 하는 일

Decoder가 현재 생성에 필요한 Encoder 정보를 직접 검색한다.

즉 앞 강의에서 배운 Attention의 역할이 Transformer에서도 그대로 남아 있다.

---

# 106. Transformer는 Encoder–Decoder 구조를 버린 것이 아니다

Original Transformer는 Seq2Seq처럼

```text
Encoder
+
Decoder
```

구조를 가진다.

달라진 것은 내부 Sequence Modeling을 RNN 대신 Attention 중심 Block으로 구현했다는 것이다.

---

# 107. RNN Seq2Seq와 Transformer 비교

## RNN Seq2Seq

```text
Embedding
 ↓
RNN Encoder
 ↓
Hidden State Sequence
 ↓ Attention
RNN Decoder
 ↓
Output
```

## Transformer

```text
Embedding + Position
 ↓
Self-Attention
 ↓
Feed Forward
 ↓
Encoder Blocks
 ↓
Cross-Attention
 ↓
Masked Self-Attention + FFN
 ↓
Decoder Blocks
 ↓
Output
```

---

# 108. RNN에서 Attention은 보조 장치였다

RNN Seq2Seq에서는 기본 Sequence Representation이 RNN Hidden State였다.

Attention은

> **마지막 Hidden State 하나에 모든 정보를 압축하는 Bottleneck을 보완**

하는 역할이었다.

---

# 109. Transformer에서는 Attention이 중심이다

Transformer에서는 Sequence 정보를 만드는 것 자체가 Self-Attention이다.

```text
RNN
→ 기본 Sequence Engine

Attention
→ 보조 정보 검색
```

에서

```text
Self-Attention
→ 기본 Sequence Engine
```

으로 바뀐다.

이 변화가 가장 중요하다.

---

# 110. 자주 헷갈리는 점 ① — Self-Attention은 Encoder–Decoder Attention인가?

아니다.

Self-Attention은 **같은 Sequence 내부**의 Attention이다.

Encoder–Decoder Attention은 Source와 Target 사이의 Attention이다.

---

# 111. 자주 헷갈리는 점 ② — Query/Key/Value는 세 개의 다른 단어인가?

아니다.

같은 Token Representation으로부터 서로 다른 Trainable Projection을 통해 만든 **세 가지 역할의 Vector**다.

---

# 112. 자주 헷갈리는 점 ③ — Query와 Key가 정보를 가져오는가?

Query와 Key는 주로 **Attention Weight를 결정**한다.

실제로 Weighted Sum되는 정보는 Value다.

---

# 113. 자주 헷갈리는 점 ④ — Self-Attention은 순서를 자동으로 아는가?

아니다.

기본 Self-Attention에는 Sequence Order가 없기 때문에 Positional Encoding/Embedding이 필요하다.

---

# 114. 자주 헷갈리는 점 ⑤ — Position Vector가 Word Embedding을 대체하는가?

아니다.

둘을 함께 사용한다.

```text
Semantic Representation
+
Position Representation
```

이다.

---

# 115. 자주 헷갈리는 점 ⑥ — Self-Attention만 많이 쌓으면 충분한가?

강의에서는 Self-Attention Output의 Linear Combination 성격 때문에 Feed Forward Network를 넣어 Non-linearity를 추가하는 구조를 설명한다.

---

# 116. 자주 헷갈리는 점 ⑦ — Decoder도 전체 문장을 Attention하면 되지 않는가?

Training/Generation 목표상 현재 Token을 예측할 때 미래 정답을 보면 안 된다.

따라서 Masked Self-Attention이 필요하다.

---

# 117. 자주 헷갈리는 점 ⑧ — Multi-Head Attention은 같은 Attention을 여러 번 복제한 것인가?

단순 복제가 아니다.

Head마다 서로 다른 Q/K/V Projection Parameter를 사용하기 때문에 서로 다른 Attention Pattern을 학습할 수 있다.

---

# 118. 자주 헷갈리는 점 ⑨ — Head가 8개면 Parameter가 무조건 8배인가?

강의에서는 Model Dimension을 Head별 작은 Dimension으로 나누어 각 Q/K/V를 계산하는 구조를 설명한다.

예:

```text
512
→ 8 Heads
→ Head당 64
```

이다.

---

# 119. 자주 헷갈리는 점 ⑩ — Scaling은 왜 √d_k인가?

강의에서 반드시 잡아야 할 핵심은

> **Dimension이 커지면서 Dot Product가 너무 커져 Softmax가 극단적으로 되는 것을 완화한다.**

는 목적이다.

---

# 120. 자주 헷갈리는 점 ⑪ — Residual은 Attention인가?

아니다.

Attention Layer 등을 우회하여 기존 Representation을 직접 더해주는 별도의 Connection이다.

---

# 121. 자주 헷갈리는 점 ⑫ — Transformer는 생성도 모든 Token을 동시에 하는가?

Autoregressive Inference에서는 아니다.

다음 Token을 생성해야 그 Token을 기반으로 그다음 Token을 생성할 수 있다.

Transformer의 강한 병렬성은 특히 **Training 시 Sequence Representation 계산**에서 중요하다.

---

# 122. 자주 헷갈리는 점 ⑬ — Attention은 항상 하나의 가장 관련된 단어를 찾는가?

아니다.

여러 Token 모두에 연속적인 Attention Weight를 부여할 수 있다.

하나의 단어 일대일 Alignment만을 의미하지 않는다.

---

# 123. 자주 헷갈리는 점 ⑭ — Transformer는 단순히 RNN을 더 빠르게 만든 것인가?

아니다.

순차 Hidden State 전달 자체를 없애고 Token 사이의 직접 Attention Connection을 만드는 다른 Sequence Modeling 방식이다.

---

# 124. 앞 차시 Attention과 Transformer의 연결

앞 차시:

```text
Decoder State S_t
      ↓ Query
Encoder Hidden States
      ↓
Attention
      ↓
필요한 Source 정보 A_t
```

이번 차시:

```text
각 Token
 ↓
Query / Key / Value
 ↓
같은 Sequence 전체 검색
 ↓
Context-aware Token Representation
```

즉 Attention이라는 **“필요한 정보를 직접 검색한다”**는 아이디어를 Sequence 전체 Representation 계산으로 확장했다.

---

# 125. 이전 NLP 강의 전체 연결

## Word Embedding

```text
Word
→ Semantic Vector
```

## RNN

```text
Word Vector
+
이전 Context
→ Context-aware Hidden State
```

## LSTM

```text
Gate를 통해
Long-term Memory 관리
```

## Seq2Seq Attention

```text
Decoder가 Encoder 정보 중
필요한 부분 직접 검색
```

## Transformer

```text
각 Token이
다른 Token 정보를 직접 검색
→ Sequence Representation 자체를
Attention으로 구성
```

---

# 126. Transformer Block을 가장 짧게 기억하면

```text
Attention
→ Token 간 정보 교환

Feed Forward
→ 각 Token 내부 표현 변환

Residual
→ 기존 정보와 Gradient 경로 보존

LayerNorm
→ Training 안정화
```

이다.

---

# 127. Transformer에서 반드시 기억할 핵심

### 1. Transformer는 Self-Attention을 핵심 연산으로 사용한다.

### 2. Self-Attention은 같은 Sequence 내부 Token 사이의 Attention이다.

### 3. RNN처럼 이전 Hidden State를 기다릴 필요가 없어 Token 계산을 병렬화할 수 있다.

### 4. 각 Token Embedding에서 Q, K, V를 만든다.

### 5. Q/K/V는 Trainable Matrix `W_Q`, `W_K`, `W_V`를 통해 만들어진다.

### 6. Query는 찾는 쪽, Key는 관련도 비교용, Value는 실제 가져올 정보다.

### 7. Query와 Key의 Dot Product로 Attention Score를 계산한다.

### 8. Softmax를 통해 Attention Weight를 만든다.

### 9. Attention Weight로 Value를 Weighted Sum하여 새로운 Token Representation을 만든다.

### 10. 기본 Self-Attention에는 위치 정보가 없으므로 Positional Encoding이 필요하다.

### 11. Position 표현은 고정 Sin/Cos 방식이나 Learned Embedding 방식 등이 가능하다.

### 12. Word Embedding과 Positional Representation을 더해 Input으로 사용한다.

### 13. Self-Attention만으로는 Non-linearity가 부족하므로 Feed Forward Network를 넣는다.

### 14. Decoder에서는 미래 Token을 참조하지 못하도록 Masked Self-Attention을 사용한다.

### 15. Multi-Head Attention은 여러 Attention Pattern을 동시에 학습한다.

### 16. Head마다 서로 다른 Q/K/V Projection을 학습한다.

### 17. Scaled Dot-Product Attention은 Score를 `√d_k`로 나눈다.

### 18. Scaling은 큰 Dot Product 때문에 Softmax가 지나치게 극단적으로 되는 것을 완화한다.

### 19. Residual Connection은 기존 Representation을 직접 전달하고 Gradient Flow를 개선한다.

### 20. Layer Normalization은 Layer Representation의 Scale을 안정화하여 Training을 돕는다.

### 21. Encoder에서는 전체 Sequence Self-Attention을 수행한다.

### 22. Decoder에서는 Masked Self-Attention을 수행한다.

### 23. Decoder에는 Encoder 정보를 검색하는 Encoder–Decoder Attention도 존재한다.

### 24. Cross-Attention에서 Query는 Decoder에서, Key/Value는 Encoder에서 온다.

### 25. 마지막에는 Linear + Softmax로 Vocabulary Probability를 만든다.

### 26. Transformer의 병렬성은 대규모 Data Training에 특히 유리하다.

### 27. Attention을 통해 멀리 떨어진 Token도 직접 연결할 수 있다.

### 28. 동일한 형태의 Block을 반복적으로 Scaling하기 쉬운 구조다.

---

# 128. 이번 강의 전체 논리

```text
RNN으로 Sequence를 처리했다.
        ↓
문제가 있다.
        ↓
첫째,
멀리 떨어진 정보가
순차 전달 중 희석될 수 있다.
        ↓
둘째,
h_(t-1)이 있어야 h_t를 계산하므로
병렬화가 어렵다.
        ↓
RNN 없이
Token들이 서로 직접 정보를
주고받으면 어떨까?
        ↓
Self-Attention
        ↓
각 Token에서
Q / K / V 생성
        ↓
Query × Key
        ↓
Token 간 관련도 계산
        ↓
Softmax
        ↓
Attention Weight
        ↓
Value Weighted Sum
        ↓
Context-aware Token Representation
        ↓
모든 Token이 독립적으로
Query가 될 수 있으므로
병렬 계산 가능
        ↓
하지만 기본 Self-Attention에는
세 가지 문제가 있다.
        ↓
순서를 모른다.
→ Positional Encoding
        ↓
Non-linearity 부족
→ Feed Forward Network
        ↓
Decoder가 미래를 볼 수 있다.
→ Masked Self-Attention
        ↓
여기에 더 좋은 Training과
Representation을 위해
        ↓
Multi-Head Attention
Scaled Dot-Product
Residual Connection
Layer Normalization
        ↓
Transformer Block 완성
        ↓
Encoder에서는
전체 Source Self-Attention
        ↓
Decoder에서는
Masked Target Self-Attention
        ↓
그리고 현재 생성에 필요한
Encoder 정보를 검색하기 위해
Cross-Attention
        ↓
Linear + Softmax
        ↓
Next-token Probability
        ↓
이 Block을 여러 층 쌓아
대규모 Language Model의
기반 Architecture로 사용
```

---

# 129. 이번 강의의 핵심 한 문장

> **Transformer는 RNN처럼 정보를 시간축으로 순차 전달하지 않고, 각 Token에서 Query·Key·Value를 생성하여 Self-Attention으로 Sequence의 다른 Token을 직접 참조함으로써 Context-aware Representation을 병렬적으로 학습하며, 위치 정보·비선형성·미래 정보 참조 문제를 Positional Encoding, Feed Forward Network, Masking으로 보완하고 Multi-Head Attention, Scaling, Residual Connection, Layer Normalization을 결합한 Architecture다.**

그리고 앞 차시 Attention과의 관계를 가장 짧게 정리하면 다음과 같다.

```text
Seq2Seq Attention
= Decoder가 Encoder에서
  필요한 정보를 검색한다.

Self-Attention
= 각 Token이 같은 Sequence에서
  자신의 표현에 필요한 정보를 검색한다.

Transformer
= 이 Self-Attention을
  Sequence 처리의 중심 Architecture로 사용한다.
```