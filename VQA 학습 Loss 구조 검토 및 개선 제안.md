
# VQA 학습 Loss 구조 검토 및 개선 제안

## 1. 검토 목적

현재 Baseline 및 E01(OCR 강화 English Prompt) 코드의 학습 방식을 확인한 결과, **모델의 최종 목표인 객관식 정답(a/b/c/d)뿐 아니라 입력 프롬프트 전체에 대해서도 Loss가 계산되고 있음**을 확인하였다.

본 챌린지의 최종 목표는 이미지·질문·선택지를 입력받아 올바른 선택지를 출력하는 것이므로, 학습 Loss 역시 최종 답변 부분에 집중시키는 방식이 더 적절한지 검토하였다.

---

## 2. 현재 코드 구조

Baseline에서는 학습 데이터를 다음 형태로 구성한다.

```text
SYSTEM
  모델에게 역할 및 출력 형식 지시

USER
  이미지
  질문
  선택지 a/b/c/d

ASSISTANT
  실제 정답 (예: c)
```

이후 `DataCollator`에서 다음 코드를 사용한다.

```python
enc["labels"] = enc["input_ids"].clone()
```

즉 모델에 입력되는 전체 토큰을 그대로 정답 Label로 사용한다.

따라서 실제 Loss 계산 대상은 개념적으로 다음과 같다.

```text
SYSTEM Prompt        → Loss 계산
USER 질문            → Loss 계산
USER 선택지          → Loss 계산
ASSISTANT 정답 c      → Loss 계산
```

결과적으로 모델은 단순히 **정답 `c`를 잘 예측하도록 학습되는 것이 아니라, 전체 대화 문장을 다음 토큰으로 잘 예측하도록 학습**된다.

---

## 3. 현재 방식의 문제점

이번 챌린지의 실제 목적은 다음과 같다.

```text
이미지 + 질문 + 선택지
        ↓
      모델
        ↓
       c
```

즉 입력 프롬프트 자체를 생성하는 능력보다 **주어진 입력을 바탕으로 올바른 정답을 출력하는 능력**이 중요하다.

그러나 현재 Loss 구조에서는 Prompt에 포함된 많은 토큰과 최종 정답 1개가 함께 Loss 계산에 포함된다.

예를 들어,

```text
Prompt 관련 토큰 : 100개
정답 토큰         : 1개
```

와 같은 상황이라면 모델이 Prompt 문장 예측 능력을 크게 개선하고 정답 예측 능력은 거의 개선하지 못하더라도 전체 Loss는 감소할 수 있다.

따라서 다음과 같은 상황이 가능하다.

```text
Training Loss ↓

하지만

객관식 정답 Accuracy → 변화 없음
```

또는 경우에 따라서는:

```text
Training Loss ↓
Accuracy ↓
```

도 가능하다.

즉 **현재 Loss 값과 실제 챌린지 성능이 완전히 동일한 목표를 측정하고 있지는 않다.**

---

## 4. E01에서 이미 개선된 부분

E01에서도 학습 Label은 기본적으로 다음과 같이 전체 `input_ids`를 복사한다.

```python
labels = enc["input_ids"].clone()
labels[enc["attention_mask"] == 0] = -100
enc["labels"] = labels
```

Baseline과 달리 Padding Token은 `-100`으로 변경하여 Loss 계산에서 제외하고 있다. 하지만 System Prompt, User Prompt, 질문 및 선택지는 여전히 Loss 계산 대상이다.

따라서 **E01 역시 아직 Answer-only Loss 방식은 아니다.**

다만 E01에서는 검증 방식이 개선되었다.

Baseline은 validation 데이터에 정답까지 포함한 후 전체 sequence의 `validation loss`를 계산한다.

반면 E01은 validation 시 정답을 모델에 알려주지 않고 실제 추론을 수행한다.

```python
pred = predict_row(row)
gold = str(row["answer"]).strip().lower()

correct = int(pred == gold)
```

이를 이용하여 실제 객관식 정답률인 `validation accuracy`를 계산한다. 현재 실행 결과는 `13/20`, 즉 `0.65`이다.

따라서 E01에서는 **평가 방식은 챌린지 목표에 더 적합하게 개선되었지만, 학습 Loss 구조는 아직 개선 여지가 있다.**

---

## 5. 개선안: Answer-only Loss Masking

학습 시 입력 Prompt는 그대로 모델에게 제공하되, Loss는 Assistant가 출력해야 하는 정답 부분에 대해서만 계산하도록 변경하는 방식을 제안한다.

현재:

```text
SYSTEM      → Loss
USER        → Loss
선택지       → Loss
ASSISTANT c → Loss
```

개선:

```text
SYSTEM      → Ignore
USER        → Ignore
선택지       → Ignore
ASSISTANT c → Loss
```

PyTorch에서는 Loss에서 제외할 Label을 `-100`으로 설정할 수 있다.

개념적으로 다음과 같이 구성한다.

```text
input_ids

[SYSTEM ... USER ... ASSISTANT c EOS]


labels

[-100  ... -100 ... -100      c EOS]
```

Prompt는 제거되는 것이 아니다.

모델은 여전히 다음 정보를 모두 입력으로 사용한다.

```text
이미지
+
System Prompt
+
질문
+
선택지
        ↓
      모델
        ↓
정답 c의 확률 계산
        ↓
      Loss
```

차이는 단지 **Prompt 자체를 얼마나 잘 재생성하는지는 채점하지 않고, 최종 답변을 얼마나 잘 예측했는지만 학습 목표로 사용한다는 것**이다.

---

## 6. 코드 변경 방향

E01은 이미 다음 두 메시지를 분리해서 가지고 있다.

```python
prompt_messages
full_messages
```

`prompt_messages`는:

```text
SYSTEM
USER
```

까지만 포함하며,

`full_messages`는:

```text
SYSTEM
USER
ASSISTANT 정답
```

까지 포함한다.

따라서 두 입력을 Tokenize하여 Prompt에 해당하는 Token 구간을 `-100`으로 Masking하는 방식으로 개선할 수 있다.

개념적인 코드는 다음과 같다.

```python
full_labels = full_input_ids.clone()

# System + User 부분은 Loss 계산 제외
full_labels[:, :prompt_length] = -100

# Padding 역시 제외
full_labels[attention_mask == 0] = -100

enc["labels"] = full_labels
```

최종적으로 Assistant의 `a`, `b`, `c`, `d` 및 필요한 종료 토큰만 Loss 계산 대상이 된다.

---

## 7. 실험 시 주의사항

현재 E01의 공식 실험 목적은 **Baseline 대비 OCR 강화 Prompt의 효과 확인**이며, 노트북에도 B00 대비 변경사항을 Prompt만으로 제한하도록 명시되어 있다.

따라서 E01에만 Answer-only Loss를 적용하면,

```text
OCR Prompt 변경
+
Loss 방식 변경
```

두 변수가 동시에 변경된다.

이 경우 Accuracy가 상승하더라도 어떤 변경이 원인이었는지 판단하기 어렵다.

따라서 기존 E01 실험과는 분리하여 다음과 같이 비교하는 것이 적절하다.

```text
B00
기본 Prompt + 기존 Loss

E01
OCR Prompt + 기존 Loss
→ OCR Prompt 효과 검증


별도 실험

B00-A
기본 Prompt + Answer-only Loss

E01-A
OCR Prompt + Answer-only Loss
→ Answer-only 환경에서도 OCR Prompt 효과 검증
```

또는 Answer-only Loss 자체를 하나의 별도 실험 항목으로 두어 기존 방식과 Accuracy를 비교할 수 있다.

---

## 8. 결론

현재 Baseline과 E01은 Prompt 및 정답을 포함한 전체 sequence에 대해 Loss를 계산하고 있기 때문에 **학습 목표와 실제 챌린지 평가 목표 사이에 일부 불일치가 존재한다.**

이번 과제는 최종적으로 `a/b/c/d` 중 올바른 답을 선택하는 것이 목적이므로, **System/User Prompt는 모델의 입력으로만 사용하고 Assistant 정답 부분에 대해서만 Loss를 계산하는 Answer-only Loss Masking을 적용하는 방안을 실험할 가치가 있다.**

다만 현재 E01은 OCR Prompt만 변경하는 통제 실험이므로 기존 E01 코드를 직접 변경하기보다는 **Answer-only Loss를 별도의 실험 변수로 분리하여 성능을 검증하는 것이 적절하다.**

한 줄로 요약해서 팀원에게 제안한다면 **“현재 SFT가 prompt 전체에 loss를 걸고 있는데, 객관식 정답 token에만 loss를 주는 answer-only masking을 별도 ablation으로 돌려보자”** 정도로 말하면 정확합니다.