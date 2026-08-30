# 2. Quantization이란?

Quantization은 가장 직관적인 Model Compression 방법 중 하나다.

원래 Model Weight가

```text
FP32
```

로 저장되어 있다고 하자.

이를

```text
FP16

INT8

INT4

...
```

처럼 더 적은 Bit로 표현한다.

강의에서는 Quantization을 Memory와 Arithmetic Overhead를 줄이는 대표적인 방법으로 설명한다.

---

# 3. 기본 아이디어

FP32는 매우 넓은 Range를 제공한다.

하지만 실제 Neural Network의 Weight가 FP32 전체 Range를 모두 사용하는 것은 아닐 수 있다.

예를 들어 대부분의 Weight가

```text
-1 ~ +1
```

사이에 있다면 FP32의 거대한 표현 영역이 필요하지 않을 수 있다.

그러면 이 좁은 Range를

```text
8 bit
```

등의 작은 Number System에 Mapping할 수 있다.

---

# 4. 핵심 문제 — 어떤 Range를 작은 Bit에 넣을 것인가?

예를 들어 INT8에 값을 넣는다고 해서 끝이 아니다.

원래 Floating-Point Value의

```text
어디서부터 어디까지를
INT8 영역에 Mapping할 것인가?
```

를 결정해야 한다.

이를 위해 **Scaling Factor**가 필요하다.

---

# 5. Quantization의 기본 구조

개념적으로

```text
Floating-Point Value
↓
Scaling
↓
Low-bit Quantized Value
```

로 Mapping한다.

즉 앞에서 배운 Fixed Point의

```text
실제 값
=
Scaling Factor × Integer
```

와 자연스럽게 연결된다.

---

# 6. Model마다 Quantization 가능 정도가 다르다

모든 Model이 같은 Bit-width까지 압축되는 것은 아니다.

```text
Model Architecture

Task

Weight Distribution

Activation Distribution
```

등에 따라

```text
INT8까지 거의 손실 없음

INT4부터 급격한 성능 저하

2 bit까지도 잘 버팀
```

등 차이가 발생할 수 있다.

---

# 7. Hardware도 중요하다

Quantization 결과가

```text
GPU가 직접 지원하는 Format
```

이라면 효율적으로 가속할 수 있다.

반대로 아주 특수한 Low-bit Format이라면

```text
Dedicated Accelerator
NPU
Custom Hardware
```

가 필요할 수도 있다.

즉 Quantization도 단순 Algorithm 문제가 아니라

```text
Quantization
+
Hardware Support
```

문제다.

---

# 8. Quantization을 이해하는 첫 번째 축

강의에서 가장 먼저 구분하는 것은

```text
QAT
vs
PTQ
```

다.

---

# 9. QAT — Quantization-Aware Training

**QAT = Quantization-Aware Training**

학습 과정에서부터

```text
최종적으로 낮은 Bit로
Quantization될 것
```

을 고려하여 Model을 Training한다.

```text
Training
+
Quantization Effect 고려
↓
Low-bit Model
```

이다.

---

# 10. PTQ — Post-Training Quantization

**PTQ = Post-Training Quantization**

이미 Training이 끝난 Model을 가져와서

```text
Pretrained FP Model
↓
Quantization
↓
Low-bit Model
```

로 만든다.

Training Process 자체는 다시 수행하지 않는다.

강의에서는 이 차이를 Quantization을 이해하는 가장 중요한 첫 번째 축으로 제시한다.

---

# 11. QAT의 장점

Model이 Training 단계에서부터 Low Precision Error를 경험한다.

따라서

```text
Quantization으로 발생하는 Error
↓
Training 과정에서 적응
```

할 수 있다.

결과적으로 같은 Bit-width라면 일반적으로 PTQ보다 Accuracy를 더 잘 방어할 가능성이 높다.

---

# 12. QAT의 단점

Training 자체가 훨씬 복잡해진다.

원래 Training도 비싼데

```text
Quantization Effect까지 Simulation
```

해야 하기 때문이다.

따라서

```text
Training Complexity ↑
Training Cost ↑
```

가 된다.

---

# 13. PTQ의 장점

이미 존재하는 Model만 있으면 된다.

```text
Training Dataset 전체

원래 Training Pipeline
```

이 없어도 적용할 수 있다.

따라서

```text
적용이 상대적으로 간단
Training Cost 없음
```

이라는 장점이 있다.

---

# 14. PTQ의 단점

Model이 Quantization Error에 적응하도록 Training된 것이 아니다.

따라서 같은 Bit까지 줄이면 QAT보다 Accuracy Drop이 클 수 있다.

---

# 15. QAT vs PTQ

|구분|QAT|PTQ|
|---|---|---|
|시점|Training 중|Training 후|
|Training 필요|O|X|
|Quantization Error 적응|가능|제한적|
|Accuracy 보존|상대적으로 유리|상대적으로 불리할 수 있음|
|비용|큼|작음|
|적용 편의성|낮음|높음|

---

# 16. 큰 Model을 압축하는 것이 유리할 수 있다

강의에서 흥미롭게 강조하는 결과가 있다.

비슷한 최종 Model Size를 만든다고 했을 때

```text
처음부터 작은 Model
```

을 Training하는 것보다

```text
큰 Model을 먼저 잘 Training
↓
Quantization으로 줄임
```

이 더 높은 Accuracy를 보이는 경우가 많다는 것이다.

강의의 표현을 구조화하면

```text
Large Model
→ 많은 Parameter
→ 더 좋은 Representation 학습
→ 이후 Compression
```

방향이 유리할 수 있다는 것이다.

---

# 17. 경량화의 대원칙으로 제시된 관점

강의에서는 이를 다음과 같은 원칙으로 설명한다.

> **가능하면 먼저 충분히 좋은 큰 Model을 만든 뒤 줄이는 방향을 생각한다.**

즉

```text
Small Model from Scratch
```

보다

```text
Large Good Model
↓
Compression
↓
Small Efficient Model
```

을 우선적으로 고려하는 관점이다.

---

# 18. 그런데 QAT가 좋다면 PTQ는 왜 필요한가?

작은 CNN처럼 Dataset과 Training Pipeline을 직접 가지고 있다면 QAT가 매우 좋은 선택일 수 있다.

하지만 Large Language Model에서는 상황이 달라진다.

---

# 19. LLM에서 QAT가 어려운 이유 ① Training Cost

LLM은 Model만 큰 것이 아니다.

```text
Training Data ↑↑

Training Compute ↑↑

Training Time ↑↑
```

도 매우 크다.

이 상태에서 Quantization까지 고려해 다시 Training하는 것은 지나치게 비쌀 수 있다.

---

# 20. 이유 ② 원래 Training Data가 없을 수 있다

공개된 Pretrained LLM을 사용하는 사람은 일반적으로 해당 Model이 정확히 어떤 Dataset과 Pipeline으로 Training되었는지 알지 못한다.

따라서

```text
Original Training을 처음부터 재현
```

하는 것 자체가 불가능할 수 있다.

강의는 이러한 현실적 이유로 Large Model에서는 PTQ의 중요성이 매우 커진다고 설명한다.

---

# 21. 작은 Domain Model에서는 QAT가 여전히 유용

반대로

```text
CNN

LSTM

GRU

MLP
```

등 상대적으로 작은 Model을 직접 Training하며 Dataset도 보유하고 있다면 QAT를 사용해 더 극단적인 Quantization을 시도할 수 있다.

---

# 22. PTQ에서 Calibration

PTQ에서는 Pretrained Model을 가져와

```text
Calibration Set
```

등을 이용해

```text
Weight / Activation Range
```

를 분석하고 적절한 Quantization Parameter를 결정한다.

목표는

```text
Original Model Performance
```

를 최대한 보존하면서 Bit-width를 낮추는 것이다.

---

# 23. Quantization의 두 번째 축

다음으로 구분해야 하는 것은

```text
Weight Quantization

vs

Activation Quantization
```

이다.

---

# 24. Weight와 Activation은 다르다

Neural Network의 연산은 크게

```text
Weight
×
Activation
```

으로 이루어진다.

Weight를 INT8로 Quantize했다고 해서 Activation도 자동으로 INT8이 되는 것은 아니다.

---

# 25. Weight + Activation Quantization

예:

```text
Weight = INT8
Activation = INT8
```

처럼 두 값을 모두 Quantize할 수 있다.

그러면 실제 Hardware가 Integer Arithmetic을 직접 지원한다면

```text
INT8 × INT8
```

연산을 바로 수행할 수 있다.

---

# 26. Weight-Only Quantization

다른 전략:

```text
Weight = INT4

Activation = FP16
```

처럼 Weight만 강하게 줄인다.

이를 **Weight-Only Quantization**이라고 볼 수 있다.

---

# 27. Weight-Only를 사용하는 이유

LLM처럼 Weight가 너무 커서

```text
Model 자체가 GPU Memory에 안 들어감
```

이 가장 큰 문제라면 일단 Weight Storage를 줄이는 것이 우선이다.

```text
Weight FP16
↓
INT4
↓
Model Memory 크게 감소
```

시킬 수 있다.

강의에서는 최근 Large Language Model에서 이런 전략이 많이 사용된다고 설명한다.

---

# 28. Weight-Only의 문제 — Arithmetic Format이 다르다

예:

```text
Weight = INT4
Activation = FP16
```

이면 두 Format을 그대로 Multiply할 수 없는 상황이 생긴다.

따라서 Weight를 내부에서 다시

```text
INT4
↓
Dequantization
↓
FP16
```

으로 복원한 뒤 FP16 Arithmetic을 수행할 수 있다.

---

# 29. Dequantization Overhead

Memory에는 INT4로 작게 저장했지만 Arithmetic 전에

```text
Dequantization
```

이 필요하다.

따라서

```text
Memory Saving
```

은 크더라도

```text
Compute Speed-up
```

이 기대한 만큼 크지 않을 수 있다.

---

# 30. Weight-Only의 목표를 정확히 이해

Weight-Only Quantization은 특히

```text
Model Storage

GPU VRAM

Memory Bandwidth
```

관점의 이점이 크다.

항상 Arithmetic 자체를 극단적으로 빠르게 만드는 방법이라고 보면 안 된다.

---

# 31. Weight + Activation Quantization의 장점

```text
Weight = INT8
Activation = INT8
```

이고 Hardware가 INT8 연산을 지원한다면

```text
Dequantization 없이
Integer Arithmetic
```

을 수행할 수 있다.

따라서 Compute Efficiency 측면에서 더 유리할 수 있다.

---

# 32. 하지만 Activation Quantization은 어렵다

특히 LLM에서는 Activation을 너무 낮은 Bit로 Quantize하면 Model Quality가 크게 떨어질 수 있다.

따라서

```text
Weight는 강하게 Quantize

Activation은 높은 Precision 유지
```

라는 선택을 할 수 있다.

---

# 33. 결국 선택은 Application에 따라 다르다

```text
CNN?

LSTM?

Transformer?

Image Classification?

Segmentation?

Video?

Language?

Multimodal?
```

에 따라 어떤 Quantization이 유리한지가 달라진다.

---

# 34. Weight-Only vs Weight+Activation

|구분|Weight-Only|Weight + Activation|
|---|---|---|
|Weight|Low-bit|Low-bit|
|Activation|FP16 등|Low-bit|
|Model Size|크게 감소 가능|감소|
|Dequantization|필요할 수 있음|적거나 없음|
|Compute Efficiency|제한될 수 있음|Hardware 지원 시 유리|
|Accuracy 보존|상대적으로 유리할 수 있음|Activation Quantization 때문에 어려울 수 있음|

---

# 35. Quantization의 세 번째 축

다음은

```text
Symmetric Quantization

vs

Asymmetric Quantization
```

이다.

---

# 36. Symmetric Quantization

0을 중심으로 동일한 Range를 Positive와 Negative에 배정한다.

개념적으로

```text
        0
        |
<-------+------->
  -Range       +Range
```

이다.

---

# 37. Weight에서는 Symmetric이 자연스러울 수 있다

많은 Weight Distribution은 강의에서

```text
0을 중심으로 비교적 균형적인 Distribution
```

을 가진다고 설명한다.

따라서

```text
Weight
→ Symmetric Quantization
```

이 자연스러운 선택이 될 수 있다.

---

# 38. Activation에서는 상황이 다르다

ReLU 등을 통과하면

```text
Negative 영역 ↓

Positive 영역 ↑
```

이 될 수 있다.

즉 Activation Distribution이 0을 중심으로 대칭적이지 않을 수 있다.

---

# 39. Symmetric을 그대로 쓰면 Bit가 낭비될 수 있다

예를 들어 실제 Activation이 거의

```text
0 ~ +10
```

에 있는데

```text
-10 ~ +10
```

을 대칭적으로 Mapping하면 Negative Region의 많은 Quantization Level이 사용되지 않는다.

---

# 40. Asymmetric Quantization

실제 Data Range에 맞춰 Quantization Range를 이동시킨다.

예:

```text
Actual Range
=
-1 ~ +8
```

이라면 그 구간을 Low-bit Format에 효율적으로 배치한다.

---

# 41. 강의에서 제시한 실용적 방향

극단적인 Quantization 상황에서는

```text
Weight
→ Symmetric

Activation
→ Asymmetric
```

방향을 고려할 수 있다고 설명한다.

---

# 42. Symmetric vs Asymmetric

|구분|Symmetric|Asymmetric|
|---|---|---|
|기준|0 중심 대칭|실제 Range 중심|
|구현|상대적으로 단순|추가 Offset 고려|
|Weight|잘 맞는 경우 많음|필요 시 사용|
|Activation|비대칭 분포에서 낭비 가능|유리할 수 있음|

---

# 43. Quantization 결과가 반드시 Integer일 필요는 없다

Quantization은

```text
FP32
→ INT8
```

만을 의미하지 않는다.

```text
FP32
→ FP16
→ BF16
→ FP8
```

처럼 더 작은 Floating-Point Format을 사용할 수도 있다.

---

# 44. Uniform Quantization

Integer처럼 Quantization Level 사이 간격이 일정하면

**Uniform Quantization**

이라고 볼 수 있다.

```text
0
1
2
3
4
...
```

처럼 일정한 Step을 사용한다.

---

# 45. Non-uniform Quantization

Quantization Interval을 일정하지 않게 만들 수도 있다.

```text
중요한 영역
→ 더 촘촘

덜 중요한 영역
→ 더 넓게
```

배치하는 방식이다.

즉 Quantization Design Space는 매우 넓다.

---

# 46. Floating-Point Quantization

앞 차시에서 배웠듯 Floating Point는

```text
Sign
Exponent
Mantissa
```

로 구성된다.

Bit를 줄일 때

```text
Exponent를 얼마나 남길까?

Mantissa를 얼마나 남길까?
```

라는 선택이 필요하다.

---

# 47. FP16의 문제를 설명하는 강의의 관점

일반적인 Half Precision에서는 FP32보다 Exponent Bit도 줄어든다.

그런데 Deep Learning Training에서는

```text
0에 매우 가까운 Weight

작은 Gradient
```

등이 많이 존재한다.

따라서 너무 작은 Range를 제대로 표현하지 못하면 Training Quality에 영향을 줄 수 있다.

---

# 48. 그래서 ML-specific Floating Format

일반 Software를 위해 만들어진 기존 Floating-Point Format이 ML에 최적이라고 보장할 수 없다.

따라서 Deep Learning에 맞춰

```text
Range

Precision
```

배분을 다시 설계한 Format들이 등장했다.

---

# 49. BF16

강의에서는 대표적으로 **BF16(BFloat16)** 을 소개한다.

핵심 직관:

```text
FP32의 넓은 Exponent Range는
가능한 많이 유지

대신

Mantissa Precision을 크게 줄임
```

이다.

즉

```text
Range 보존 ↑
Precision 희생 ↑
```

전략이다.

---

# 50. 왜 이런 선택을 하는가?

Training에서는 아주 작은 값과 큰 값을 모두 표현할 수 있는 Range가 중요할 수 있다.

따라서 제한된 16 bit를

```text
Mantissa에 많이 투자
```

하기보다

```text
Exponent를 유지
```

하는 선택을 한 것이다.

---

# 51. TF32 등의 Format

강의에서는 GPU/ML용으로 Range와 Precision을 서로 다른 방식으로 조합한 Floating-Point Format들도 소개한다.

핵심은 특정 이름을 모두 암기하는 것이 아니라

> **같은 Bit Budget 안에서도 Exponent와 Mantissa 배분을 바꾸면 ML에 맞는 다른 Number Format을 만들 수 있다.**

는 것이다.

---

# 52. FP8

Bit를 8개까지 줄이면 더욱 극단적인 Trade-off가 생긴다.

```text
1 Sign bit
+
남은 Bit를
Exponent / Mantissa에 나눔
```

해야 한다.

---

# 53. FP8의 두 방향

Exponent를 더 많이 배정하면

```text
Range ↑
Precision ↓
```

이다.

Mantissa를 더 많이 배정하면

```text
Range ↓
Precision ↑
```

이다.

---

# 54. 하나의 Format만 사용하지 않을 수도 있다

상황에 따라

```text
Range가 중요한 Tensor
→ Exponent가 큰 Format

Precision이 중요한 Tensor
→ Mantissa가 큰 Format
```

을 섞어 사용할 수도 있다.

---

# 55. Hardware도 계속 Low Precision을 지원

강의에서는 GPU 발전과 함께

```text
FP16

BF16

TF32 계열

FP8

더 작은 Format
```

지원이 확대되고 있다고 설명한다.

핵심은

```text
Low Precision Arithmetic
```

이 전용 Accelerator만의 영역이 아니라 일반 AI GPU에서도 점점 중요한 Hardware Capability가 되고 있다는 점이다.

---

# 56. Quantization의 네 번째 축 — Mixed Precision

모든 Layer에 같은 Bit를 사용할 필요는 없다.

예:

```text
Layer 1
→ 8 bit

Layer 2
→ 3 bit

Layer 3
→ 2 bit

Layer 4
→ 4 bit
```

처럼 Layer별로 다른 Precision을 사용할 수 있다.

이를 **Mixed Precision** 관점으로 볼 수 있다.

---

# 57. 왜 Mixed Precision을 사용하는가?

Layer마다 Quantization Error에 대한 민감도가 다를 수 있기 때문이다.

```text
중요한 Layer
→ 높은 Precision

덜 민감한 Layer
→ 낮은 Precision
```

으로 배분한다.

---

# 58. 평균 Bit-width를 낮출 수 있다

전체를 3 bit로 사용하는 대신

```text
일부 = 3 bit

일부 = 2 bit
```

로 구성해 평균

```text
2.x bit
```

수준으로 줄이면서 3-bit Model과 유사한 Quality를 노릴 수 있다.

---

# 59. 하지만 Mixed Precision도 Hardware 문제가 있다

Layer마다 Bit-width가 다르면 Hardware가 각 Format을 효율적으로 지원해야 한다.

따라서 극단적인 Mixed Precision에서는 Dedicated Accelerator가 더 유리할 수도 있다.

---

# 60. Quantization의 실제 GPU Speed-up

중요하다.

예를 들어

```text
FP16
→ INT4
```

이면 Bit 수는 4분의 1이다.

그러면

```text
Inference가 정확히 4배 빨라지는가?
```

아니다.

---

# 61. 왜 이론적인 Bit 감소만큼 빨라지지 않는가?

특히 Weight-Only Quantization에서는

```text
INT4 Weight
↓
Dequantization
↓
FP16
↓
FP16 Activation과 연산
```

과정이 존재할 수 있다.

또한 Batch가 커지면 Activation Traffic의 비중이 커질 수도 있다.

---

# 62. 따라서

```text
Bit-width 4× 감소
≠
Runtime 4× 감소
```

이다.

강의에서는 Weight-Only Quantization이 Model을 Memory에 넣는 데는 명확한 가치가 있지만 실제 GPU Speed-up은 별도 문제라고 강조한다.

---

# 64. Pruning이란?

**Pruning = 가지치기**

Training이 끝난 Neural Network의 Weight를 보면

```text
절댓값이 매우 작은 Weight
```

들이 존재할 수 있다.

---

# 65. 왜 작은 Weight를 제거할 수 있을까?

Neural Network의 기본 Operation을 단순화하면

```text
Weight × Activation
```

의 합이다.

어떤 Weight가 거의 0이라면

```text
0에 가까운 Weight × Activation
≈ 작은 영향
```

이 된다.

따라서 일부 Weight는 제거해도 Model Output에 큰 영향을 주지 않을 수 있다.

---

# 66. Pruning의 기본 아이디어

```text
중요 Weight
→ 유지

중요하지 않은 Weight
→ 0
→ 저장하지 않음
```

이다.

강의에서는 이것을 Training 이후 이미 학습된 Model에서 영향이 작은 Weight를 제거하는 방식으로 설명한다.

---

# 67. Dropout과 Pruning은 다르다

강의 중 질문에서 명확히 구분한다.

### Dropout

```text
Training 중
Random하게 일부 Neuron/Connection 제거
↓
Generalization 향상 목적
```

### Pruning

```text
학습된 Weight의 중요도를 평가
↓
중요하지 않은 Weight 제거
↓
Compression 목적
```

이다.

---

# 68. Quantization vs Pruning

Quantization:

```text
Weight 수는 그대로

각 Weight의 Bit 수 ↓
```

Pruning:

```text
각 살아남은 Weight Precision은 유지 가능

Weight 개수 ↓
```

이다.

---

# 69. 예

Original:

```text
100개의 Weight
×
32 bit
```

Quantization:

```text
100개의 Weight
×
4 bit
```

Pruning:

```text
100개 중
50개만 유지
```

처럼 접근 방식이 다르다.

---

# 70. Pruning의 새로운 문제 — 위치를 저장해야 한다

Dense Matrix에서는 모든 Weight 위치가 정해져 있다.

하지만 Pruning 후

```text
살아남은 Weight만 저장
```

한다면

```text
그 Weight가 원래 Matrix의
어디에 있었는가?
```

도 알아야 한다.

---

# 71. Metadata / Index

따라서 Pruned Model에는

```text
Weight Value
+
Weight Position
```

정보가 필요할 수 있다.

강의에서는 이를

```text
Index

Metadata

CSR 등 Sparse Format
```

과 연결한다.

---

# 72. Pruning의 실제 Memory

즉

```text
제거된 Weight만큼
무조건 그대로 Memory가 감소
```

한다고 생각하면 안 된다.

Sparse Representation을 위해 추가 Metadata도 필요하다.

---

# 73. Quantization과 Pruning을 함께 사용할 수 있다

Quantization에는 한계가 있다.

예:

```text
4 bit
→ 잘 버팀

3 bit
→ Accuracy 급격히 감소
```

할 수 있다.

이때 3 bit로 내리지 않고

```text
4-bit Model
+
Pruning
```

을 적용할 수 있다.

---

# 74. 예: 4-bit + 50% Pruning

개념적으로 Weight Storage만 생각하면

```text
4 bit × 50%
```

이므로 평균적으로

```text
2 bit/원래 Weight 수준
```

의 저장효과를 생각할 수 있다.

하지만 Accuracy는 직접 2 bit Quantization하는 것보다 더 잘 유지될 수도 있다.

강의에서는 이러한 이유로 Quantization을 한계까지 적용한 뒤 Pruning을 추가하는 방향을 강조한다.

---

# 75. Pruning의 큰 단점 — GPU와의 궁합

Dense Matrix:

```text
모든 위치에 값 존재
↓
Regular Pattern
↓
GPU 병렬화 쉬움
```

Pruned Matrix:

```text
어떤 위치는 0
어떤 위치는 값 존재
↓
Irregular Pattern
```

이 된다.

---

# 76. 압축을 풀어야 한다

Hardware 입장에서는

```text
이 값이 원래 어디에 있었는가?
```

를 Metadata에서 읽어 복원하며 계산해야 한다.

이 작업이 GPU의 Regular Parallel Computation과 잘 맞지 않을 수 있다.

---

# 77. 그래서

```text
Model Size ↓
```

는 크게 얻었는데

```text
GPU Runtime
```

은 빨라지지 않거나 오히려 느려질 수도 있다.

이것은 앞 차시에서 배운

```text
Model Size Reduction
≠
Speed-up
```

의 대표 사례다.

---

# 78. Pruning을 이해하는 첫 번째 축 — Importance

어떤 Weight를 제거할 것인가?

가장 단순한 기준은

```text
|Weight|
```

즉 **Magnitude**다.

---

# 79. Magnitude Pruning

```text
|Weight| 작음
↓
영향 작다고 가정
↓
제거
```

한다.

하지만 단순한 Magnitude만으로 Weight Importance를 완벽하게 평가할 수 있는 것은 아니다.

---

# 80. Better Importance Criterion

따라서 연구에서는

```text
Weight 크기뿐 아니라

Model Output에 미치는 영향

Activation과의 관계

Loss 변화
```

등을 고려하여 더 나은 Importance Score를 찾으려 한다.

---

# 81. 좋은 Importance Criterion이 중요한 이유

같은

```text
50% Pruning
```

이라도 어떤 Weight를 제거하느냐에 따라 Accuracy/Perplexity가 크게 달라질 수 있다.

즉

> **Pruning Ratio만큼이나 “누구를 제거하느냐”가 중요하다.**

---

# 82. Pruning의 두 번째 큰 축

```text
Unstructured Pruning

vs

Structured Pruning
```

이다.

---

# 83. Unstructured Pruning

Weight 하나하나를 독립적으로 판단한다.

```text
0 1 0 1 1 0 0 1 ...
```

처럼 살아남는 위치가 불규칙할 수 있다.

---

# 84. 장점

가장 중요하지 않은 Weight를 세밀하게 선택할 수 있다.

따라서 같은 Pruning Ratio에서는 Accuracy를 더 잘 보존할 가능성이 있다.

---

# 85. 단점

Matrix Pattern이 매우 불규칙해진다.

```text
GPU Parallelization 어려움

Metadata 필요

Sparse Operation 필요
```

하다.

---

# 86. Structured Pruning

Weight를 하나씩 랜덤하게 제거하지 않고

```text
Row

Column

Channel

Block

Head
```

등 일정한 Structure 단위로 제거한다.

---

# 87. 장점

남아 있는 Matrix Structure가 Regular하다.

따라서

```text
GPU / Hardware
```

에서 병렬화하기 쉽다.

---

# 88. 단점

Importance가 높은 Weight까지 Group 때문에 함께 제거될 수 있다.

따라서

```text
Accuracy Drop ↑
```

가 될 수 있다.

---

# 89. Structured vs Unstructured의 본질적 Trade-off

```text
Unstructured
→ Accuracy 친화적
→ Hardware 비친화적


Structured
→ Hardware 친화적
→ Accuracy 손실 가능
```

이다.

---

# 90. Semi-Structured Pruning

둘 사이의 절충 형태도 있다.

완전히 Random하지 않지만 너무 큰 Structure 단위도 아닌 일정한 규칙을 강제한다.

대표적인 예로 강의에서는 **2:4 Pruning**을 소개한다.

---

# 91. 2:4 Pruning

연속된 Weight 4개 중

```text
2개는 유지
2개는 제거
```

하는 Rule이다.

즉

```text
4개의 Weight마다
50% Sparsity
```

를 강제한다.

---

# 92. 왜 이런 Rule을 만들까?

완전 Random Sparse Matrix보다 Pattern이 규칙적이다.

따라서 GPU Hardware가

```text
4개 중 2개가 존재
```

한다는 사실을 미리 알고 가속할 수 있다.

---

# 93. Hardware Support

강의에서는 NVIDIA GPU가 이러한 Semi-structured Sparsity를 지원하는 사례를 소개한다.

즉

```text
Pruning Algorithm
+
Hardware Rule
```

을 함께 설계한 사례다.

---

# 94. 하지만 모든 Model에서 잘 되는 것은 아니다

강의에서는 CNN 계열에서는 이러한 Structured/Semi-structured Pruning이 효과적이었던 사례가 많았지만 Transformer/LLM으로 가면서 동일 방식의 Pruning이 Perplexity를 크게 악화시킬 수 있다고 설명한다.

---

# 95. Sparse Matrix가 언제 GPU에서 빨라지는가?

중요한 질문이다.

Pruning했다고 무조건 Sparse Kernel이 Dense Kernel보다 빠르지 않다.

왜냐하면 Sparse Operation에는

```text
Index 확인

Metadata Access

Irregular Memory Access

Compression 해제
```

Overhead가 있기 때문이다.

---

# 96. Dense vs Sparse Kernel

강의에서는

```text
Dense Matrix
→ cuBLAS 계열

Sparse Matrix
→ cuSPARSE 계열
```

과 같은 구조를 소개한다.

기존 구현 사례에서는 매우 높은 Sparsity에 도달해야 Sparse Arithmetic이 실제 Dense Arithmetic보다 빨라지는 경우가 있다고 설명한다.

---

# 97. 핵심은 특정 숫자가 아니다

중요한 원리는

> **Sparsity가 충분히 높지 않으면 “0을 건너뛰어 얻는 이득”보다 Sparse Format을 처리하는 Overhead가 더 클 수 있다.**

는 것이다.

---

# 98. CNN과 LLM의 차이

강의에서는 과거 CNN에서는 매우 높은 Pruning Ratio에서도 Accuracy를 잘 유지하는 Technique이 존재해 Sparse Acceleration을 활용하기 쉬운 경우가 있었다고 설명한다.

반면 LLM에서는 높은 Pruning Ratio에서 Quality가 훨씬 민감하게 떨어지는 문제가 있다고 설명한다.

---

# 99. 그래서 LLM Compression의 현실적 흐름

```text
Quantization
↓
4 bit 정도까지 줄임

더 줄이면
Accuracy 급락
↓
Pruning 추가
```

처럼 여러 Compression Technique을 조합하는 방향을 고려할 수 있다.

---

# 100. 예

```text
4 bit → 3 bit
```

로 Quantization하는 대신

```text
4 bit
+
25% Pruning
```

을 적용했을 때 비슷한 Storage Reduction을 얻으면서 Accuracy를 더 잘 보존하는 경우를 강의에서 설명한다.

---

# 109. Knowledge Distillation이란?

세 번째 대표 Compression Technique은

**Knowledge Distillation(지식 증류)** 이다.

기본 구조:

```text
Large / Powerful Model
=
Teacher

↓

Knowledge 전달

↓

Small Model
=
Student
```

이다.

---

# 110. 목적

> **큰 Model이 학습한 지식을 이용해 작은 Model을 더 잘 학습한다.**

단순히 작은 Model을 처음부터 Training하는 것보다 큰 Teacher의 Guide를 활용한다.

강의에서는 이를 “큰 똑똑한 Teacher의 도움을 받아 작지만 잘 학습된 Student를 만드는 방법”으로 설명한다.

---

# 111. 일반적인 Student Training

Teacher가 없다면 Student는 보통

```text
Student Prediction
vs
Ground Truth
```

의 차이만 이용해 Training한다.

```text
Ground Truth
↓
Student
↓
Student Loss
```

이다.

---

# 112. Distillation에서는 Teacher 정보가 추가된다

Student가

```text
Ground Truth
```

뿐 아니라

```text
Teacher가 어떻게 판단하는가?
```

도 학습한다.

즉

```text
Ground Truth Loss
+
Distillation Loss
```

를 함께 사용할 수 있다.

---

# 113. Feature Distillation

강의에서는 Teacher와 Student의 Intermediate Feature를 비교하는 형태도 설명한다.

```text
Teacher Feature
↕
Student Feature
```

가 최대한 비슷해지도록 Training한다.

---

# 114. 목적

Student가 단순히

```text
정답만 맞히기
```

보다

```text
Teacher와 비슷한 Representation
Teacher와 비슷한 판단
```

을 배우게 한다.

---

# 115. Total Loss의 직관

개념적으로

```text
Total Loss
=
Student Task Loss
+
Distillation Loss
```

형태로 생각할 수 있다.

즉

```text
정답도 잘 맞히고

Teacher도 잘 따라가도록
```

Training한다.

---

# 116. 왜 Teacher가 도움이 되는가?

Ground Truth가

```text
정답 Class 하나
```

만 알려준다면 Teacher의 Output/Feature에는 더 풍부한 정보가 들어 있을 수 있다.

예를 들어 Teacher가 어떤 Input을 보고

```text
Class A: 매우 높음
Class B: 조금 가능
Class C: 거의 불가능
```

처럼 판단한다면 Student는 단순 Correct Label 외에도 Class 간 관계를 배울 수 있다.

강의에서는 세부 Soft Label 이론보다 Teacher의 Feature/Knowledge를 Student가 따라간다는 관점에 집중한다.

---

# 117. Knowledge Distillation의 중요한 문제 — Capacity Gap

Teacher는 클수록 무조건 좋은가?

직관적으로는

```text
Teacher가 더 똑똑하면
Student도 더 잘 배우지 않을까?
```

라고 생각할 수 있다.

하지만 강의에서는 반드시 그렇지 않다고 설명한다.

---

# 118. Teacher가 너무 클 수 있다

Teacher와 Student의 Capability 차이가 너무 커지면

```text
Teacher
↓↓↓↓↓↓
Student
```

사이의 Representation Gap이 지나치게 커질 수 있다.

Student가 Teacher를 효과적으로 따라가지 못하는 상황이 발생한다.

---

# 119. 반대 방향으로 봐도 같다

Teacher Size를 고정하고 Student를 계속 작게 만들면 어느 순간

```text
Student Capacity
```

가 너무 작아 Teacher Knowledge를 흡수하기 어려워질 수 있다.

---

# 120. 그래서 Teacher Assistant

Teacher와 Student 사이에

```text
Teacher Assistant(TA)
```

를 둘 수 있다.

```text
Large Teacher
↓
Medium TA
↓
Small Student
```

구조다.

---

# 121. TA의 역할

Teacher와 Student 사이의 Knowledge Gap을 단계적으로 줄인다.

```text
너무 어려운 Teacher Knowledge
↓
중간 Capacity Model이 먼저 학습
↓
Student가 다시 학습
```

한다.

강의에서는 실제 조교가 교수와 학생 사이에서 설명을 보완하는 것에 비유한다.

---

# 122. 중요한 교훈

Knowledge Distillation에서도

```text
Teacher가 무조건 클수록 좋음
```

이 아니다.

핵심은

```text
Teacher–Student Capacity Gap
```

까지 고려하는 것이다.

---

# 123. LLM 시대의 Knowledge Distillation

강의에서는 LLM에서 Distillation의 중요성이 더욱 커졌다고 설명한다.

이유 중 하나는

```text
Strong Teacher Model
```

을 구하기가 과거보다 쉬워졌기 때문이다.

---

# 124. 과거 CNN

직접 매우 강력한 Teacher를 Training해야 했다면 상당한 Cost가 필요했다.

---

# 125. LLM 시대

이제는

```text
Large Open Model

Strong Existing Model
```

등을 Teacher로 활용하고 Smaller Model을 Training하는 방향을 생각할 수 있다.

강의에서는 이런 방식의 연구가 활발하다고 설명한다.

---

# 126. 그래도 Distillation이 쉬운 것은 아니다

Teacher를 구했다고 모든 문제가 해결되는 것은 아니다.

Student를 Training하려면 여전히

```text
Training Data
```

가 필요하다.

강의는

```text
Model도 Resource지만
Data도 Resource다.
```

라는 점을 다시 강조한다.

---

# 127. Distillation vs From Scratch

작은 Student를

```text
처음부터 혼자 Training
```

하는 것보다

```text
Teacher의 Knowledge를 이용해 Training
```

하면 동일 Student Size에서 더 좋은 성능을 얻는 것을 목표로 한다.

---

# 128. 강의에서 언급된 LLM 관련 논쟁

강의 후반에는 일부 Small/Open LLM이 다른 강력한 Model을 Teacher로 사용해 Distillation했을 가능성을 둘러싼 산업계 논쟁 사례를 소개한다.

이 부분은 특정 Model이 실제로 어떤 Teacher를 사용했다는 사실을 확정하는 내용이라기보다,

> **강력한 Teacher Model의 Output을 활용해 작은 Model을 만드는 Distillation이 LLM 시대에 중요한 기술적·산업적 이슈가 되었다**

는 사례로 이해하는 것이 적절하다.

---

# 129. Knowledge Distillation의 본질

```text
Teacher가 이미 투자한
Data + Compute + Learning
↓
Student Training에 활용
↓
# 134. 강의 전체에서 계속 반복되는 원칙

> **하나의 경량화 기법이 모든 Model·Task·Hardware에 항상 최적인 것은 아니다.**

항상

```text
Model

Task

Target Accuracy

Memory Constraint

Latency Constraint

Target Hardware
```

를 함께 봐야 한다.

---

# 135. Quantization에서 특히 볼 것

```text
Training을 다시 할 수 있는가?
→ QAT / PTQ

Memory만 줄이면 되는가?
→ Weight-Only 고려

Arithmetic도 줄이고 싶은가?
→ Weight + Activation

Distribution이 대칭적인가?
→ Symmetric / Asymmetric

모든 Layer 민감도가 같은가?
→ Mixed Precision
```

이다.

---

# 136. Pruning에서 특히 볼 것

```text
어떤 Weight가 중요하지 않은가?

몇 % 제거할 수 있는가?

Structured인가?

Unstructured인가?

Sparse Metadata Overhead는?

GPU가 가속할 수 있는가?
```

다.

---

# 137. Distillation에서 특히 볼 것

```text
Teacher는 충분히 좋은가?

Student Size는 얼마나 작은가?

Teacher–Student Gap이 너무 크지 않은가?

TA가 필요한가?

Distillation Data를 확보할 수 있는가?
```

다.

---

