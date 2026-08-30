[강의 링크](https://www.youtube.com/watch?v=92H8Vb1_lRs)

## 시험 범위 포함되는 내용
| 시험 토픽        | 근거                                                                       |
| ------------ | ------------------------------------------------------------------------ |
| **회귀식과 오차항** | 실제 데이터가 `y = f*(x) + ε` 형태이며, `f*`는 참함수이고 `ε`는 관측오차/noise라는 구조를 직접 설명한다. |
# 0. 강의 전체 개요

이번 강의에서는 AI와 머신러닝의 가장 기본적인 원리를 다룬다.

주요 학습 목표는 다음과 같다.

1. AI, Machine Learning, Deep Learning은 어떤 관계인가?
    
2. 머신러닝에서 데이터는 어떤 역할을 하는가?
    
3. Feature와 Label은 무엇인가?
    
4. 머신러닝에서 “학습한다”는 것은 정확히 무엇인가?
    
5. 우리가 실제로 관측할 수 있는 것과 관측할 수 없는 것은 무엇인가?
    
6. 참함수(True Function)는 무엇인가?
    
7. 가설 공간(Hypothesis Space)은 무엇인가?
    
8. 모델(Model)은 가설 공간과 어떤 관계인가?
    
9. 왜 데이터 하나하나를 완벽하게 맞추는 것이 머신러닝의 진짜 목적이 아닌가?
    
10. 학습된 함수가 새로운 데이터에서도 잘 동작해야 하는 이유는 무엇인가?
    

이 강의에서 설명하는 개념들은 이후 머신러닝과 딥러닝을 이해하기 위한 기본 뼈대가 된다.

---

# 1. 우리가 이미 사용하고 있는 머신러닝

머신러닝은 일상생활에서 이미 광범위하게 사용되고 있다.

강의에서는 대표적으로 두 가지 사례를 제시한다.

## YouTube 추천

YouTube가 사용자의 취향을 파악해서 적절한 영상을 추천한다.

여기서 중요한 것은 개발자가

```text
이 사람에게는 A 영상을 추천하고,
저 사람에게는 B 영상을 추천해라.
```

와 같은 모든 규칙을 직접 코딩하는 것이 아니라는 점이다.

대신 사용자의 행동 데이터를 통해

> **어떤 사용자가 어떤 콘텐츠를 선호하는지를 데이터로부터 학습한다.**

## Spam Mail Filtering

메일 시스템 역시

- 정상 메일
    
- 스팸 메일
    

을 구분한다.

이 역시 사람이 가능한 모든 스팸 규칙을 직접 작성하는 방식보다는 데이터를 통해 스팸과 정상 메일의 패턴을 학습하는 방식으로 해결할 수 있다.

즉 머신러닝의 중요한 특징은

> **사람이 모든 규칙을 직접 hard coding하는 대신, 데이터 속에 존재하는 규칙과 패턴을 모델이 학습한다는 것**

이다.

---

# 2. AI, Machine Learning, Deep Learning의 관계

세 용어는 서로 같은 개념이 아니다.

포함 관계로 보면 다음과 같다.

```text
Artificial Intelligence
└── Machine Learning
    └── Deep Learning
```

즉

`Deep Learning ⊂ Machine Learning ⊂ AI`

이다.

---

# 3. AI(Artificial Intelligence)

AI는 세 개념 중 가장 넓은 범주다.

강의에서는 AI를

> 주어진 환경에서 인지, 학습, 추론 등을 통해 특정 목표를 달성하기 위해 예측하거나, 행동을 선택하거나, 계획하는 시스템

이라는 넓은 개념으로 설명한다.

따라서 AI가 반드시 머신러닝이어야 하는 것은 아니다.

예를 들어 사람이 미리 정한 규칙에 따라 작동하는 **Rule-based AI**도 AI의 범주에 들어갈 수 있다.

---

# 4. Machine Learning

Machine Learning은 AI 안의 한 접근 방법이다.

핵심은

> **데이터로부터 규칙을 학습하여 목표를 달성한다.**

는 것이다.

목표는 다양한 형태가 될 수 있다.

- 예측
    
- 행동 선택
    
- 계획
    
- 분류
    
- 추천
    

강의에서 제시하는 예:

- 생성 AI
    
- Language Model
    
- Image Classification
    
- Recommendation System
    
- Spam Filtering
    

등은 데이터를 기반으로 학습하는 머신러닝 시스템이다.

---

# 5. Deep Learning

Deep Learning은 다시 Machine Learning의 부분집합이다.

Deep Learning도 결국 데이터로부터 규칙을 학습한다는 점에서는 머신러닝과 같다.

차이는 **사용하는 함수의 형태**에 있다.

Deep Learning에서는 모델로 **Neural Network**를 사용한다.

즉

> **Neural Network를 모델로 사용하여 Machine Learning을 수행하는 것이 Deep Learning이다.**

라고 이해할 수 있다.

---

# 6. 모든 AI가 Machine Learning인 것은 아니다

포함 관계를 다시 정확하게 보면

```text
AI
├─ Machine Learning
│   ├─ Deep Learning
│   └─ Deep Learning이 아닌 ML
│
└─ Machine Learning이 아닌 AI
```

도 존재한다.

예를 들어

- Rule-based system
    
- 규칙 기반 algorithm
    

등은 AI일 수 있지만 Machine Learning은 아니다.

다만 이번 강좌에서는 주로 **데이터 기반 Machine Learning AI**를 중심으로 다룬다.

---

# 7. 머신러닝의 전체 Pipeline

머신러닝의 기본 흐름은 다음과 같이 볼 수 있다.

```text
Data
 ↓
Model 설정
 ↓
Learning
 ↓
Evaluation
 ↓
Model 개선 / 선택
 ↓
다시 Learning
```

즉 한 번 학습하고 끝나는 것이 아니라 평가 결과를 바탕으로

- 모델을 바꾸거나
    
- 학습 방식을 수정하거나
    
- 데이터를 추가하거나
    

하면서 반복적으로 개선할 수 있다.

강의에서는 앞으로 학습할 내용을

- Data
    
- Model
    
- Learning
    
- Evaluation
    

이라는 큰 틀 안에서 이해하도록 한다.

---

# 8. Machine Learning에서 Data가 중요한 이유

Machine Learning에서는 사람이 모든 규칙을 직접 작성하지 않는다.

대신 데이터 속에 존재하는 패턴을 이용한다.

```text
Data
 ↓
Pattern
 ↓
Learning
 ↓
Model
```

따라서 어떤 데이터를 사용하느냐가 학습 결과에 직접적인 영향을 준다.

강의에서는

> **데이터의 분포와 데이터 내부의 관계가 머신러닝 학습 결과를 결정한다.**

고 강조한다.

즉 학습 방법을 아직 정확히 몰라도

> **데이터가 학습의 근원이다.**

라는 점은 반드시 기억해야 한다.

---

# 9. Feature

데이터를 이해하기 위해 가장 먼저 구분해야 할 개념이 **Feature**와 **Label**이다.

Feature는

> **모델이 예측을 위해 사용하는 입력 정보**

다.

즉

`Feature = Input`

이라고 우선 이해할 수 있다.

---

# 10. Spam Mail에서의 Feature

Spam mail을 예로 들면 feature에는 다음과 같은 것이 포함될 수 있다.

- 이메일 제목
    
- 이메일 본문의 text
    
- 발신자 주소
    
- 특정 단어의 등장 빈도
    
- 특정 단어들의 분포
    
- 이메일에서 확인할 수 있는 기타 특성
    

모델은 이러한 feature들을 보고

```text
Spam?
Normal?
```

을 판단한다.

---

# 11. Label

Label은

> **모델이 예측하려고 하는 정답**

이다.

즉

`Label = Target / Output`

으로 볼 수 있다.

Spam mail 예제에서는

```text
Spam
또는
Normal
```

이 label이 된다.

---

# 12. YouTube 추천에서 Feature

추천 시스템에서는 영상 자체와 사용자에 관한 여러 정보가 feature가 될 수 있다.

### 영상 Feature

- Genre
    
- Creator
    
- 조회수
    
- 좋아요 수
    

### 사용자 Feature

- 어떤 콘텐츠를 시청했는가?
    
- 무엇을 구독했는가?
    
- 어떤 콘텐츠와 상호작용했는가?
    

이러한 정보들이 Recommendation Model의 input으로 들어간다.

---

# 13. YouTube 추천에서 Label

추천 시스템의 label은 사용자가 실제로 어떤 행동을 했는지가 될 수 있다.

예:

- 추천한 영상을 시청했는가?
    
- 추천했지만 보지 않았는가?
    
- 좋아요를 눌렀는가?
    

이러한 user feedback이 label 역할을 할 수 있다.

따라서 추천 시스템은 feature와 label 사이의 관계를 학습하여

> **사용자가 볼 가능성이 높거나 좋아할 가능성이 높은 콘텐츠**

를 추천하는 방향으로 발전한다.

---

# 14. Feature와 Label 핵심

강의에서 가장 단순하게 정리한 관계:

```text
Feature = Input
Label   = Output
```

이다.

---

# 15. 가장 단순한 학습 문제 — 1D Feature

머신러닝의 원리를 이해하기 위해 먼저 feature가 하나뿐인 단순한 문제를 생각한다.

강의의 예:

### Feature

`Years of Education`

즉 몇 년 동안 교육을 받았는가?

### Label

`Income`

즉 수입은 얼마인가?

따라서 모델이 알고 싶은 관계는

```text
교육 연수
   ↓
수입
```

이다.

---

# 16. 하나의 Data Point

그래프에서 각각의 빨간 점은 한 사람의 데이터다.

예를 들어

```text
Education = 17 years
Income    = $60,000
```

인 사람이 있다면 하나의 점으로 표현된다.

즉 하나의 데이터는

`(x, y)`

형태로 표현할 수 있다.

여기서

- `x` = Feature
    
- `y` = Label
    

이다.

여러 사람이 있다면

```text
(x₁, y₁)
(x₂, y₂)
(x₃, y₃)
...
```

와 같은 여러 data point가 존재한다.

---

# 17. 데이터 뒤에는 어떤 관계가 존재한다고 생각한다

교육 연수와 income 사이에는 어떤 평균적인 관계가 존재한다고 생각할 수 있다.

강의에서는 이를 하나의 함수로 표현한다.

`f*`

여기서 `*`는 **실제 참값(True)** 을 의미하는 표기로 사용한다.

따라서 `f*`는

> **Feature와 Label 사이에 실제로 존재하는 미지의 참된 관계**

다.

---

# 18. True Function은 우리 눈에 보이지 않는다

매우 중요한 점이다.

데이터를 받았다고 해서 `f*`가 같이 주어지는 것이 아니다.

우리가 실제로 관측할 수 있는 것은 점들뿐이다.

```text
관측 가능:
● ● ● ● ●
Data Points

관측 불가능:
────────
True Function f*
```

즉 `f*`는 **미지의 참함수(Unknown True Function)** 다.

---

# 19. 실제 Data Point는 True Function 위에 정확하게 있지 않는다

관측된 데이터는 `f*`에 정확하게 붙어 있지 않는다.

```text
        ● 실제 데이터
        │
        │ ε
────────┼──── f*(x)
```

이 차이를 강의에서는 `ε`로 표현한다.

즉 하나의 데이터는 개념적으로

`y = f*(x) + ε`

형태로 이해할 수 있다.

여기서 `ε`는 관측 오차 또는 noise에 해당한다.

---

# 20. 왜 데이터에 오차가 존재하는가?

실제 데이터가 참함수에 정확하게 위치하지 않는 이유는 다양할 수 있다.

예:

- 측정 과정의 오차
    
- 관측 noise
    
- 현실에 존재하는 randomness
    
- 모델의 input으로 표현되지 않은 다른 영향
    

강의에서는 이 모든 세부 원인을 구분하기보다 `ε`를 이용하여

> **참된 평균적 관계에서 개별 데이터가 벗어나는 정도**

를 표현한다.

---

# 21. Dataset의 표현

강의에서는 전체 데이터를 `D`라고 표현한다.

예제에서 30명의 사람이 있다면

```text
D =
{
(x₁, y₁),
(x₂, y₂),
...
(x₃₀, y₃₀)
}
```

이라고 생각할 수 있다.

우리가 실제로 확인할 수 있는 것은 이 data point들이다.

---

# 22. 우리가 알 수 없는 두 가지

데이터를 받았을 때 다음 두 가지는 직접 관측할 수 없다.

### 1. True Function

`f*`

### 2. 각각의 Error

`ε`

왜냐하면 `ε`를 알고 있다면

`f*(x) = y - ε`

로 참함수를 바로 계산할 수 있기 때문이다.

따라서 실제 머신러닝에서는

```text
관측 가능
→ (x, y)

관측 불가능
→ f*, ε
```

이다.

---

# 23. 머신러닝에서 진짜 학습하려는 것은 무엇인가?

강의에서 반복적으로 강조하는 가장 중요한 개념이다.

교육 연수와 income 데이터가 주어지고

> “이들의 관계를 학습하세요.”

라고 했을 때 우리가 진짜 알고 싶은 것은 개별 점 자체가 아니다.

우리가 알고 싶은 것은

> **그 데이터 뒤에 숨어 있는 미지의 참함수 `f*`**

다.

즉 머신러닝 학습의 궁극적인 목표는

```text
Observed Data
      ↓
Learning
      ↓
True Function f*에 가까운 함수
```

를 찾는 것이다.

---

# 24. True Function을 봤다고 생각하면 안 된다

강의에서는 참함수를 설명하기 위해 그래프에 파란 선으로 `f*`를 보여준다.

하지만 실제 머신러닝 문제에서는 그 선을 볼 수 없다.

따라서 예제 문제에서

> “아까 파란색 True Function과 비슷하니까 이 모델이 정답이다.”

라고 판단하면 안 된다.

실제 학습자는 오직

```text
● ● ● ● ●
```

라는 관측 데이터만 가지고 판단해야 한다.

---

# 25. Model을 이해하기 전에 필요한 개념 — Hypothesis Space

학습의 목적이

> “Feature와 Label 사이의 함수를 찾는 것”

이라면 하나의 문제가 생긴다.

세상에는 가능한 함수가 무수히 많다.

그렇다면

> **어떤 종류의 함수들 중에서 찾을 것인가?**

를 먼저 결정해야 한다.

이를 **Hypothesis Space(가설 공간)** 라고 한다.

---

# 26. Hypothesis Space란?

가설 공간은

> **우리가 정답 함수가 존재한다고 가정하고 탐색하기로 한 함수들의 범위**

다.

예를 들어

> “나는 직선 형태의 함수 중에서만 답을 찾겠다.”

라고 결정했다고 하자.

그러면

```text
/
—
\
/
...
```

와 같은 모든 직선 함수의 집합이 가설 공간이 된다.

---

# 27. Linear Hypothesis Space

교육 연수와 income 사이의 관계를 **Linear Regression**으로 학습한다고 결정했다고 하자.

그 순간 이미

> **정답을 선형 함수들 사이에서 찾겠다.**

고 가설 공간을 선택한 것이다.

즉

```text
Hypothesis Space
=
모든 가능한 직선
```

이다.

---

# 28. Non-linear Hypothesis Space

반대로

> “직선만으로는 부족하니 좀 더 자유로운 곡선에서 찾겠다.”

고 할 수도 있다.

그러면 더 flexible한 함수들의 집합이 새로운 가설 공간이 된다.

```text
Linear Hypothesis Space
→ 직선만

Non-linear Hypothesis Space
→ 다양한 곡선
```

일반적으로 non-linear hypothesis space가 더 많은 종류의 함수를 표현할 수 있으므로 더 큰 함수 공간이 된다.

---

# 29. Neural Network를 선택한다는 의미

나중에

> “이 문제를 Neural Network로 풀겠다.”

또는

> “Deep Learning으로 풀겠다.”

고 결정하는 것도 결국 가설 공간을 결정하는 것이다.

즉

```text
Linear Regression 사용
→ Linear 함수 공간에서 찾겠다.

Neural Network 사용
→ Neural Network가 표현할 수 있는 함수 공간에서 찾겠다.
```

라는 의미다.

따라서 어떤 algorithm/model family를 선택하는 행위 자체가 **Hypothesis Space 선택**과 연결된다.

---

# 30. Hypothesis Space는 누가 선택하는가?

가설 공간은 학습 algorithm이 알아서 완전히 결정하는 것이 아니다.

대부분 **모델을 설계하는 사람**이 선택한다.

예를 들어 개발자가

```text
"Linear Regression을 쓰겠다."
```

라고 결정하는 순간

```text
"Linear 함수 공간에서 답을 찾겠다."
```

라는 가정을 함께 선택한 것이다.

---

# 31. Model이란 무엇인가?

가설 공간이 가능한 모든 후보 함수의 집합이라면

**Model은 그 가설 공간 안의 특정 함수 하나**다.

예를 들어 가설 공간이 모든 직선이라면

```text
Hypothesis Space
├── y = x + 1
├── y = 2x + 3
├── y = -x + 10
└── ...
```

여기서 특정 하나의 직선을 선택하면 그것이 하나의 model이다.

---

# 32. Hypothesis Space와 Model 관계

정리하면

```text
Hypothesis Space
= 가능한 모델들의 전체 집합

Model
= Hypothesis Space 안의 특정 함수 하나
```

이다.

따라서

> **학습한다는 것은 결국 가설 공간에서 적절한 모델 하나를 찾는 과정**

이라고 볼 수 있다.

---

# 33. Linear와 Non-linear

강의에서는 한 가지 용어를 명확히 구분한다.

머신러닝에서 **Linear**라고 할 때는 기본적으로 직선적인 관계를 의미한다.

1D에서는

```text
Linear
→ 직선
```

이고 곡선은

```text
Non-linear
→ 곡선
```

이다.

Non-linear hypothesis space는 linear space보다 더 flexible한 함수들을 포함할 수 있다.

---

# 34. 학습(Learning)이란 무엇인가?

지금까지의 개념을 연결하면 머신러닝의 학습을 정확하게 정의할 수 있다.

먼저 존재하는 것은

```text
Data
+
Hypothesis Space
```

이다.

그리고 가설 공간 안에는 매우 많은 후보 model이 있다.

학습에서는 주어진 데이터를 가장 잘 설명하도록 모델의 규칙 또는 parameter를 조금씩 조정한다.

즉

> **주어진 데이터에 대해 적절한 모델을 가설 공간에서 찾아가는 과정**

이 학습이다.

---

# 35. 좋은 Model인지 어떻게 판단하는가?

가설 공간 안에 여러 model이 있다면 어느 model이 더 좋은지 비교할 기준이 필요하다.

여기서 등장하는 것이 **Loss Function**이다.

강의에서는 아직 세부적인 loss function을 다루기 전이므로 우선

> **현재 model이 주어진 data에서 얼마나 틀렸는지를 하나의 숫자로 표현하는 함수**

라고 설명한다.

---

# 36. 학습의 전체 구조

따라서 학습을 다음과 같이 볼 수 있다.

```text
Data
  ↓
Hypothesis Space 설정
  ↓
현재 Model 선택
  ↓
Prediction
  ↓
Loss 계산
  ↓
Model Parameter 수정
  ↓
더 작은 Loss
  ↓
반복
```

즉 loss가 작아지는 방향으로 model의 parameter를 반복적으로 조정해 더 좋은 model을 찾는다.

---

# 37. 1D Feature에서 2D Feature로 확장

지금까지는 feature가 하나였다.

```text
x₁ = Years of Education
```

Label:

```text
y = Income
```

이제 feature 하나를 추가해보자.

예:

```text
x₁ = Years of Education
x₂ = Seniority
y  = Income
```

이제 input feature가 두 개가 된다.

---

# 38. 1D와 2D라는 말의 의미

여기서

- 1D Feature
    
- 2D Feature
    

라는 표현은 **Feature의 개수**를 의미한다.

### 1D Feature

```text
x = [x₁]
```

### 2D Feature

```text
x = [x₁, x₂]
```

이다.

Label dimension을 포함한 그래프 차원과 혼동하면 안 된다.

---

# 39. 2D Feature에서는 함수가 Surface가 된다

Feature가 하나일 때는

```text
x → y
```

이므로 그래프를 선으로 표현할 수 있었다.

Feature가 두 개라면

```text
(x₁, x₂) → y
```

가 되므로 함수가 3차원 공간의 **Surface(면)** 로 나타난다.

```text
        y
       /
      / Surface
     /
x₂ ─────
   /
 x₁
```

형태가 된다.

---

# 40. 차원이 증가해도 원리는 같다

Feature가 두 개로 늘어났더라도 기본 구조는 변하지 않는다.

여전히

```text
y = f*(x) + ε
```

관계가 존재한다.

다만 `x`가 숫자 하나가 아니라 여러 feature를 가진 vector가 되었을 뿐이다.

그리고 여전히

- `f*`는 관측 불가능
    
- `ε`도 관측 불가능
    
- Data point만 관측 가능
    

하다.

---

# 41. 2D Feature에서 Linear Model

2개의 feature를 가진 데이터에서 linear hypothesis space를 선택하면 결과는 직선이 아니라 **평면**이 된다.

```text
1D Linear Function
→ Line

2D Feature Linear Function
→ Plane
```

강의에서는 이를 더 일반적으로 **Hyperplane**이라고 설명한다.

---

# 42. Hyperplane

Linear function을 고차원으로 확장하면 hyperplane이 된다.

예:

```text
Feature 1개
→ 2차원 graph의 line

Feature 2개
→ 3차원 graph의 plane

Feature 여러 개
→ 고차원 hyperplane
```

따라서

> “Linear = 반드시 눈으로 보이는 선 하나”

라고 생각하면 안 된다.

차원이 증가하면 linear model 역시 고차원 공간의 평면 형태로 확장된다.

---

# 43. Non-linear Model의 경우

가설 공간을 더 flexible한 non-linear 함수들로 설정하면 평평한 plane 대신 굴곡진 surface를 학습할 수 있다.

```text
Linear
→ 평평한 Surface

Non-linear
→ 구불구불한 Surface
```

가설 공간을 어떻게 선택하느냐에 따라 최종적으로 학습되는 함수의 형태가 달라진다.

---

# 44. 어느 Model이 가장 좋은가?

여러 학습 결과를 관측 데이터에 겹쳐보면 매우 복잡한 함수가 모든 data point를 정확하게 통과할 수도 있다.

언뜻 보면

```text
Data Error = 0
```

이므로 최고의 모델처럼 보인다.

하지만 강의에서는

> **이것이 반드시 좋은 모델은 아니다.**

라고 설명한다.

---

# 45. 왜 모든 Training Data를 완벽하게 맞추면 안 될 수 있는가?

중요한 이유는 데이터 자체가 참함수 위에 정확하게 있지 않기 때문이다.

Recall:

`y = f*(x) + ε`

즉 각각의 data point에는 noise `ε`가 존재한다.

그런데 모델이 모든 data point에 정확히 맞도록 지나치게 구불구불해지면

> **참함수 `f*`뿐 아니라 각 데이터에 들어 있는 noise `ε`까지 따라갈 수 있다.**

그 결과 training data에는 완벽하지만 진짜 우리가 알고 싶은 `f*`에서는 오히려 멀어질 수 있다.

---

# 46. Training Error = 0이 최종 목적이 아니다

따라서 머신러닝의 목표는

```text
Training Data 각각의 Error = 0
```

을 만드는 것이 아니다.

진짜 목표는

```text
Unknown True Function f*
```

에 가까운 함수를 찾는 것이다.

강의에서 매우 강조하는 핵심 문장이다.

> **데이터 하나하나의 오차를 0으로 만드는 것이 목적이 아니라, 미지의 참함수를 학습하는 것이 목적이다.**

---

# 47. Generalization의 직관

왜 `f*`에 가까워야 할까?

실제 머신러닝 모델은 학습할 때 이미 본 데이터에만 사용할 것이 아니기 때문이다.

학습 이후에는 처음 보는 새로운 input이 들어온다.

```text
Training Data
      ↓
Learning
      ↓
Learned Function
      ↓
New x
      ↓
Prediction y
```

따라서 새로운 데이터에서도 올바른 prediction을 해야 한다.

이 능력이 이후 배우게 될 **Generalization**과 연결된다.

---

# 48. Overfitting의 직관적 출발점

강의에서는 아직 과적합을 정식으로 배우기 전이지만 지나치게 복잡한 함수가 모든 training data를 정확하게 통과하는 사례를 통해 개념을 먼저 제시한다.

```text
너무 단순
→ Data의 관계를 충분히 표현하지 못함

적절한 복잡도
→ True Function에 가까울 가능성

너무 복잡
→ Data의 Noise까지 외움
```

따라서 모델에는 **적절한 flexibility**가 중요하다.

---

# 49. 너무 단순한 Model도 문제다

반대로 지나치게 rigid한 model 역시 문제가 된다.

실제 관계가 어느 정도 곡선인데

```text
나는 무조건 직선만 사용한다.
```

고 제한한다면 데이터에 존재하는 중요한 관계를 충분히 표현하지 못할 수 있다.

따라서 좋은 모델을 찾는 문제에서는

```text
너무 단순하지도 않고
너무 복잡하지도 않은
적절한 함수
```

를 찾아야 한다.

강의에서는 이를 **적절한 flexibility**의 문제로 설명한다.

---

# 50. Feature의 일반적인 수학 표기

이제 교육 연수, seniority 같은 구체적인 이름 대신 일반적인 notation을 사용한다.

Label:

`y`

Feature가 하나라면:

`x`

Feature가 `p`개라면

```text
x₁
x₂
x₃
...
xₚ
```

로 표현한다.

이를 하나의 vector로 묶으면

`x = [x₁, x₂, ..., xₚ]`

인 `p`-dimensional feature vector가 된다.

---

# 51. 머신러닝 데이터의 기본 식

강의에서 설명한 관계를 일반화하면 다음과 같이 표현할 수 있다.

`y = f*(x) + ε`

여기서

- `x`: Feature vector
    
- `y`: Label
    
- `f*`: Unknown True Function
    
- `ε`: Error / Noise
    

이다.

---

# 52. Error ε에 대한 중요한 가정 ① — x와 독립

강의에서는 `ε`에 대해 중요한 통계적 가정을 소개한다.

`ε`는 feature `x`와 독립이라고 가정한다.

직관적으로 말하면

> **x가 커진다고 해서 error가 자동으로 커지는 것이 아니고, x가 작아진다고 해서 error가 자동으로 작아지는 것도 아니다.**

즉

```text
x 크기
≠
ε 크기
```

의 직접적인 관계가 없다고 가정한다.

---

# 53. Error ε에 대한 중요한 가정 ② — 평균이 0

또 하나의 가정은

`E[ε] = 0`

이다.

여기서 `E`는 Expected Value, 즉 평균을 의미한다.

따라서

> **Noise가 장기적으로 한쪽 방향으로 치우치지 않고 평균적으로 0이다.**

라고 가정한다.

강의에서는 이후 머신러닝을 공부하는 동안 매우 자주 암묵적으로 사용되는 가정이므로 기억할 것을 강조한다.

---

# 54. 왜 True Function을 학습하려고 하는가?

`f*`를 잘 학습하면 가장 먼저 얻는 장점은 **새로운 input에 대한 prediction**이다.

학습 데이터에 없는 새로운 `x`가 들어오더라도

`ŷ = f(x)`

를 이용해 적절한 `y`를 예측할 수 있다.

즉 머신러닝의 가치가 단순히 과거 데이터를 설명하는 데 있는 것이 아니라

> **보지 못한 새로운 데이터를 예측할 수 있다는 것**

에 있다.

---

# 55. True Function을 알면 Feature의 중요성도 이해할 수 있다

`f`를 잘 학습하면 prediction 외에도 데이터의 구조를 이해할 수 있다.

예를 들어 여러 feature가 있을 때

- 어떤 feature가 중요한가?
    
- 어떤 feature는 덜 중요한가?
    
- 어떤 feature는 label과 거의 관계가 없는가?
    

등을 분석할 수 있다.

또 함수의 구조를 통해

> **x의 변화가 y에 얼마나 영향을 주는가**

를 분석할 수도 있다.

강의에서는 이후 배울 Linear Regression이 이런 해석에 유리한 사례라고 설명한다.

---

# 56. 이 강의의 핵심 개념 관계

전체를 하나의 구조로 연결하면 다음과 같다.

```text
Artificial Intelligence
        ↓
Machine Learning
        ↓
데이터를 이용해 규칙을 학습
        ↓
Data = (Feature x, Label y)
        ↓
실제 세계에서는
y = f*(x) + ε
        ↓
우리가 볼 수 있는 것
= Data

볼 수 없는 것
= f*, ε
        ↓
우리의 목적
= f*에 가까운 함수를 찾는 것
        ↓
하지만 세상 모든 함수를
전부 탐색할 수는 없음
        ↓
Hypothesis Space 설정
        ↓
가능한 Model 후보들 정의
        ↓
Loss를 기준으로
좋은 Model을 탐색
        ↓
Learning
        ↓
학습 데이터뿐 아니라
새로운 데이터에서도 잘 예측
        ↓
Generalization
```

---

# 57. AI / ML / DL 최종 비교

|개념|의미|관계|
|---|---|---|
|AI|인지·학습·추론 등을 통해 목표를 달성하는 시스템|가장 큰 범주|
|Machine Learning|데이터로부터 규칙을 학습하는 AI 접근법|AI의 부분집합|
|Deep Learning|Neural Network를 사용하는 Machine Learning|ML의 부분집합|

---

# 58. Feature / Label 비교

|구분|Feature|Label|
|---|---|---|
|역할|모델의 입력|모델이 예측할 정답|
|표기|`x`|`y`|
|Spam 예|제목, 단어 빈도, 발신자 등|Spam / Normal|
|추천 예|사용자·영상 정보|시청 여부, 좋아요 여부|

---

# 59. 관측 가능한 것과 불가능한 것

|대상|관측 가능?|
|---|---|
|Feature `x`|가능|
|Label `y`|가능|
|Dataset `(x, y)`|가능|
|True Function `f*`|불가능|
|각 데이터의 Noise `ε`|불가능|

따라서 머신러닝은 **보이는 데이터만으로 보이지 않는 관계를 추정하는 문제**라고 볼 수 있다.

---

# 60. Hypothesis Space / Model / Learning 관계

```text
Hypothesis Space
"어떤 종류의 함수에서 찾을 것인가?"
        ↓
Model
"그 공간 안의 특정 함수 하나"
        ↓
Learning
"데이터를 이용해 좋은 Model을 찾는 과정"
```

예:

```text
Linear Regression 선택
        ↓
Linear Hypothesis Space
        ↓
많은 직선/Hyperplane 후보
        ↓
Data를 가장 잘 설명하는 하나 선택
        ↓
Learned Linear Model
```

---

# 61. 반드시 구분해야 할 두 종류의 Error

이번 강의에서 혼동하기 쉬운 부분이다.

## Noise / Measurement Error `ε`

True Function과 실제 관측 데이터 사이의 차이.

```text
True Function
      ↕ ε
Observed Data
```

이는 직접 관측할 수 없다.

## Model Error

학습한 함수와 실제 data 사이의 차이.

```text
Learned Model
      ↕
Observed Data
```

이 둘은 같은 개념이 아니다.

강의 후반의 복잡한 함수 예시에서도 학습된 surface와 data 사이의 작은 차이를 관측 오차 `ε`와 구분해서 설명한다.

---

# 62. 자주 헷갈리는 점 ① — 학습은 Data를 외우는 것인가?

아니다.

Training data를 잘 맞추는 것은 학습 과정에서 필요한 수단이지만 최종 목적은 아니다.

```text
목적 X
Training Data를 100% 외우기

목적 O
Data 뒤의 True Function을 잘 근사하기
```

그래야 새로운 data에도 적용할 수 있다.

---

# 63. 자주 헷갈리는 점 ② — 가장 Flexible한 Model이 가장 좋은가?

아니다.

더 flexible한 hypothesis space는 더 다양한 관계를 표현할 수 있다는 장점이 있다.

하지만 지나치게 flexible하면 noise까지 따라가는 함수를 만들 수 있다.

따라서

> **높은 표현력 = 항상 좋은 Generalization**

은 아니다.

강의에서는 이를 과적합으로 이어지는 중요한 직관으로 제시한다.

---

# 64. 자주 헷갈리는 점 ③ — Linear Model은 항상 선인가?

Feature가 하나일 때만 눈에 보이는 직선이다.

```text
Feature 1개
→ Line

Feature 2개
→ Plane

Feature 여러 개
→ Hyperplane
```

즉 Linear의 핵심은 시각적인 “선 모양”이 아니라 **linear functional relationship**이라는 점이다.

---

# 65. 자주 헷갈리는 점 ④ — Deep Learning은 Machine Learning과 완전히 다른 기술인가?

아니다.

Deep Learning 역시 Machine Learning이다.

다만 사용하는 함수 공간이 Neural Network라는 점에서 구분한다.

따라서

```text
Machine Learning
└─ Neural Network를 사용
       ↓
   Deep Learning
```

으로 이해해야 한다.

---

# 66. 강의에서 가장 중요하게 강조하는 문장

이번 강의에서 가장 중요한 문장을 하나 선택하면 다음과 같다.

> **우리가 학습하려는 대상은 관측된 데이터 그 자체가 아니라, 그 데이터 뒤에 존재하는 미지의 참함수 `f*`이다.**

이 관점이 잡혀 있어야 이후

- Loss Function
    
- Linear Regression
    
- Overfitting
    
- Generalization
    
- Bias–Variance
    
- Regularization
    
- Neural Network
    

등을 자연스럽게 이해할 수 있다.

---

# 67. 강의 전체 논리

이번 1차시 전체는 다음 흐름으로 이해하면 된다.

```text
우리는 일상에서 AI를 사용한다.
        ↓
그중 데이터에서 규칙을 배우는 것이 Machine Learning이다.
        ↓
Neural Network를 사용하는 ML이 Deep Learning이다.
        ↓
Machine Learning의 출발점은 Data다.
        ↓
Data에는 Feature x와 Label y가 있다.
        ↓
그 둘 사이에는 실제로
미지의 관계 f*(x)가 존재한다고 생각한다.
        ↓
하지만 실제 데이터에는 Noise ε가 있으므로
y = f*(x) + ε 형태로 관측된다.
        ↓
우리 눈에 보이는 것은 (x, y)뿐이다.
f*와 ε는 보이지 않는다.
        ↓
따라서 Data를 이용해
f*와 최대한 가까운 함수를 찾아야 한다.
        ↓
그런데 모든 함수를 탐색할 수는 없다.
        ↓
어떤 종류의 함수에서 찾을지
Hypothesis Space를 결정한다.
        ↓
Hypothesis Space 안의 하나의 함수가 Model이다.
        ↓
Loss를 기준으로 좋은 Model을 찾는 과정이 Learning이다.
        ↓
그러나 Training Data의 Error를
무조건 0으로 만드는 것이 목표는 아니다.
        ↓
Noise까지 외우면 True Function에서 멀어질 수 있다.
        ↓
궁극적인 목적은 새로운 Data까지 잘 예측하는
True Function에 가까운 Model을 찾는 것이다.
```

---

# 68. 이번 강의의 핵심을 한 문장으로 정리

> **머신러닝은 관측 가능한 Feature–Label 데이터만을 이용해, 직접 볼 수 없는 미지의 참함수에 가까운 모델을 미리 정한 가설 공간에서 찾아가는 과정이며, 그 목적은 학습 데이터를 단순히 외우는 것이 아니라 새로운 데이터까지 잘 예측하는 데 있다.**