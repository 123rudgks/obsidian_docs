[강의 링크](https://www.youtube.com/watch?v=nRsYJjCTnV0)
## 시험 범위에 포함되는 내용
| 시험 토픽                 | 매칭 내용                                                              |
| --------------------- | ------------------------------------------------------------------ |
| **RAG**               | Retrieval → Relevant Documents → LLM Context → Generation          |
| **문장 임베딩과 코사인 유사도**   | Query/Document를 Embedding으로 바꾸고 Vector Similarity로 Semantic Search |
| **AI Agent 동작**       | 후반부 Agentic RAG에서 검색 여부·재검색·Query 수정 등을 동적으로 결정                    |
| **AI Agent의 Tool 활용** | Search/Retrieval을 LLM의 외부 Tool로 사용                                 |
## Information Retrieval & Retrieval-Augmented Generation(RAG)

# 0. 강의 전체 개요

앞 차시에서는 Language Model 자체를 더 잘 만드는 **Post-training**을 배웠다.

```text
Pre-training
↓
Instruction Tuning
↓
RLHF / DPO / RLVR
↓
더 유용하고 정렬된 Language Model
```

이번에는 다른 방향으로 간다.

> **Model 자체를 계속 학습시키는 대신, Model 외부의 Information System을 연결하면 어떨까?**

전체 흐름은 다음과 같다.

```text
Language Model
↓
자체 Parameter에 지식 저장

하지만
├─ 모든 지식을 저장하지 못함
├─ 희귀한 지식에 약함
├─ 지식이 오래될 수 있음
└─ 기업 내부 Data를 알 수 없음
↓
외부 Search System을 붙이자.
↓
Information Retrieval
↓
Query
↓
Retriever
↓
Relevant Documents
↓
Top-K Documents
↓
LLM Context에 추가
↓
Generation
↓
RAG


그런데 검색도 완벽하지 않다.
↓
Context Length 문제
Retrieval Noise
Knowledge Conflict
Multi-document Reasoning
False Document
↓
단순 고정 Pipeline만으로 부족
↓
검색 여부·횟수·Query 수정 등을
LLM이 스스로 결정
↓
Agentic RAG / Agent
```

---

# 1. Information Retrieval이란?

**Information Retrieval(정보 검색)** 은

> **사용자의 Query와 관련된 정보를 거대한 Document Collection에서 찾아오는 기술**

이다.

기본 구조는

```text
Query
↓
Retriever
↓
Document Collection / Index
↓
Relevant Documents
```

이다.

검색 결과는 일반적으로 하나만 반환하는 것이 아니라 관련도에 따라 **Ranking**한다.

```text
Document 1
Document 2
Document 3
...
```

그리고 상위 `K`개의 문서를 가져온다.

이를 **Top-K Retrieval**이라고 볼 수 있다.

---

# 2. Retrieval은 오래된 분야다

RAG 때문에 Retrieval이 최근에 갑자기 등장한 것은 아니다.

Information Retrieval은 오래전부터

- Google/Naver 같은 Web Search
    
- Amazon 같은 Product Search
    
- Recommendation System
    
- Document Search
    

등에서 사용되어 온 Computer Science의 큰 분야다.

강의에서는 Retrieval 자체만으로도 한 학기 동안 배울 만큼 큰 학문이라고 설명한다.

---

# 3. Recommendation과 Retrieval

추천도 Retrieval과 구조적으로 비슷하다.

### Search

```text
User Query
↓
많은 Documents
↓
관련 Document 검색
```

### Recommendation

```text
User Preference
↓
많은 Contents
↓
사용자가 좋아할 Content 검색
```

즉 둘 다

> **거대한 Candidate 집합에서 User의 Need와 관련된 Item을 찾아 Ranking한다.**

는 공통 구조가 있다.

---

# 4. Query란?

Information Retrieval에서 **Query**는

> 사용자가 원하는 정보를 표현한 입력

이다.

예:

```text
"Transformer가 무엇인가?"
```

가 Query가 될 수 있다.

---

# 5. Attention의 Query와 연결

강의에서는 Transformer Attention에서 배운 `Query`와도 추상적인 의미가 비슷하다고 설명한다.

Attention:

```text
Query
↓
어떤 Key와 관련 있는가?
↓
Relevant Value 검색
```

Information Retrieval:

```text
User Query
↓
어떤 Document가 관련 있는가?
↓
Relevant Document 검색
```

물론 Attention의 Query는 User가 직접 작성한 문장이 아니라 Token으로부터 계산되는 Representation이라는 차이는 있다.

---

# 6. Retriever

Query를 받아 관련 Document를 찾는 System을 **Retriever**라고 한다.

Retriever에는 크게

```text
Sparse Retriever

Dense Retriever
```

가 있다.

---

# 7. Sparse Retriever

Deep Learning 이전부터 사용되던 전통적인 Statistical Retrieval 방식이다.

대표적인 예:

```text
TF-IDF
```

이다.

---

# 8. TF의 직관

**TF = Term Frequency**

어떤 Document 안에서 특정 Word가 얼마나 많이 등장하는지를 본다.

```text
Document에서
Query Word가 많이 등장
↓
관련 가능성 ↑
```

이다.

---

# 9. IDF의 직관

하지만

```text
a
is
the
```

같은 단어는 거의 모든 Document에 존재한다.

따라서 많이 등장한다고 해서 중요한 Word라고 볼 수 없다.

그래서

> **전체 Document Collection에서는 희귀하면서 현재 Document에서는 자주 등장하는 Word**

를 중요하게 본다.

이 역할을 하는 것이 **Inverse Document Frequency**다.

---

# 10. TF-IDF

따라서 직관적으로

```text
현재 Document에서는 많이 등장하고
+
전체 Document에서는 희귀한 Word
↓
중요한 Word
```

가 된다.

강의에서는 이런 통계 기반 Retrieval을 Sparse Retriever와 연결한다.

---

# 11. Sparse Retriever의 장점

강의에서 언급한 장점은 다음과 같다.

```text
빠름
GPU가 필요하지 않을 수 있음
비용이 낮음
해석이 비교적 쉬움
```

특히 Word별 Weight를 확인할 수 있어 어떤 이유로 Score가 나왔는지 이해하기 비교적 쉽다.

---

# 12. Sparse Retriever의 한계

Word Matching에 크게 의존하기 때문에 Semantic Meaning에 유연하게 대응하기 어렵다.

예:

```text
자동차
↔
차량
```

처럼 의미는 비슷하지만 Surface Word가 다르면 단순 Keyword Matching에서는 놓칠 수 있다.

---

# 13. Dense Retriever

Deep Learning을 이용해

```text
Query
↓
Embedding

Document
↓
Embedding
```

으로 변환한 뒤 Vector Similarity를 이용한다.

---

# 14. Semantic Search

Query와 Document의 Embedding이 있다고 하자.

```text
Query Vector
↔
Document Vector
```

의 Similarity를 계산한다.

의미가 비슷하면 Vector Space에서도 가깝게 위치하도록 Model을 학습할 수 있다.

이를 이용한 검색을 넓은 의미에서 **Semantic Search**라고 볼 수 있다.

---

# 15. Contrastive Learning과 연결

Retriever Training Data가

```text
Query

Relevant Document

Irrelevant Document
```

로 주어진다면

```text
Query ↔ Positive Document
→ 가까워지게

Query ↔ Negative Document
→ 멀어지게
```

학습할 수 있다.

이는 앞 Image Foundation Model에서 배운 CLIP의 Contrastive Learning과 구조적으로 유사하다.

---

# 16. Dense Retriever의 장점

Keyword가 정확하게 일치하지 않아도 Context와 Semantic Meaning을 이용할 수 있다.

예:

```text
"차가 고장 났다."
```

와

```text
"자동차 수리 방법"
```

처럼 Word가 완전히 동일하지 않아도 의미 관계를 찾을 수 있다.

---

# 17. Dense Retriever의 비용

하지만 Deep Neural Network를 사용하므로

```text
Embedding Computation
GPU/Compute
Training Data
```

등이 필요할 수 있다.

즉 Sparse Retrieval보다 일반적으로 무겁다.

---

# 18. Dense Retrieval의 두 구조

강의에서는 대표적으로

```text
Bi-encoder

Cross-encoder
```

를 설명한다.

---

# 19. Bi-encoder

Query와 Document를 **서로 독립적으로 Encoding**한다.

```text
Query
↓
Query Encoder
↓
Query Embedding


Document
↓
Document Encoder
↓
Document Embedding
```

그다음

```text
Similarity(Query, Document)
```

를 계산한다.

---

# 20. Bi-encoder의 가장 큰 장점

Document는 Query가 들어오기 전에 미리 Encoding할 수 있다.

```text
Documents
↓
Offline Embedding
↓
Vector DB에 저장
```

한다.

Inference 시에는

```text
새 Query
↓
Query Embedding 생성
↓
기존 Document Vector와 Similarity 계산
```

만 하면 된다.

---

# 21. Document Caching

즉 Document Embedding을 **Caching**할 수 있다는 것이 핵심이다.

수백만 개 Document를 매 Query마다 Neural Network에 다시 통과시킬 필요가 없다.

강의에서는 Cosine Similarity 자체의 계산은 비교적 저렴하다는 점도 설명한다.

---

# 22. Bi-encoder의 한계

Query와 Document를 따로 Encoding하므로

```text
Query Token
↔
Document Token
```

사이에 직접 Attention을 수행할 수 없다.

즉 두 Text 사이의 세밀한 Interaction을 충분히 Modeling하지 못할 수 있다.

---

# 23. Cross-encoder

Cross-encoder에서는

```text
Query
+
[SEP]
+
Document
```

를 하나의 Sequence로 만든다.

그리고 하나의 Encoder에 함께 입력한다.

---

# 24. Cross-encoder의 장점

Query와 Document Token 사이에 직접 Attention이 발생한다.

따라서

```text
Query와 Document의 세밀한 관계
```

를 더 정확하게 Modeling할 수 있다.

즉 일반적으로 Ranking Quality가 좋을 수 있다.

---

# 25. Cross-encoder의 치명적인 단점

새로운 Query가 들어올 때마다

```text
Query + Document 1
Query + Document 2
Query + Document 3
...
```

모든 Pair를 다시 Neural Network에 넣어야 한다.

따라서 Document가 수백만 개라면 현실적으로 매우 비싸다.

---

# 26. Bi-encoder vs Cross-encoder

|구분|Bi-encoder|Cross-encoder|
|---|---|---|
|Query/Document Encoding|분리|함께|
|Document 사전 Embedding|가능|어려움|
|Token 간 직접 Interaction|제한적|가능|
|속도|빠름|느림|
|대규모 Retrieval|적합|부적합|
|정밀 Ranking|상대적으로 약함|강함|

---

# 27. 그러면 실제 Search System에서는 무엇을 쓰는가?

정답은

> **하나만 사용하지 않는다.**

이다.

강의에서는 현업 Search/Recommendation System이 여러 Stage의 Model을 조합한다고 설명한다.

---

# 28. Multi-stage Retrieval

예를 들어 100만 개 Candidate가 있다고 하자.

처음부터 Cross-encoder로 모두 비교하면 너무 비싸다.

그래서

```text
Stage 1
가벼운 Retrieval
↓
10,000 Candidates

Stage 2
Dense Bi-encoder
↓
50 Candidates

Stage 3
Cross-encoder
↓
Top 10
```

처럼 점점 Candidate 수를 줄인다.

---

# 29. Candidate Generation

첫 Stage에서는

> **정답 후보를 놓치지 않고 넓게 가져오는 것**

이 중요하다.

정밀 Ranking보다 Recall이 중요할 수 있다.

예:

```text
Legacy Rules
Sparse Search
Popularity
Recency
User Preference
```

등 여러 Module의 Candidate를 합칠 수 있다.

---

# 30. 두 번째 Stage

많은 Candidate를 Dense Retriever 같은 Model로 줄인다.

예:

```text
10,000
↓
50
```

정도로 축소한다.

---

# 31. 마지막 Stage — Reranking

후보가 50개 정도로 줄어들었다면 Cross-encoder처럼 비싼 Model을 사용할 수 있다.

```text
50 Candidates
↓
Cross-encoder
↓
정교한 Score
↓
Ranking 재정렬
```

이를 **Reranking**이라고 한다.

---

# 32. 검색은 단일 Model이 아니라 System이다

강의에서 매우 중요한 실무적 포인트다.

Commercial Search Engine은 보통

```text
Retriever 하나
```

로 이루어진 것이 아니다.

수많은

- Rule
    
- Ranking Model
    
- Business Logic
    
- Personalization
    
- Freshness Module
    
- Advertisement Module
    

등이 결합된 거대한 System이다.

따라서 연구 Benchmark의

```text
Model A vs Model B
```

비교와 실제 Search Engine은 구조가 크게 다를 수 있다.

---

# 33. 검색 시스템에는 Legacy가 많다

현업 Search Engine은 여러 Team이 오랜 기간 개발한 Module이 누적된다.

따라서

```text
하나의 최신 Deep Learning Model로
기존 System을 전부 교체
```

하는 것보다 기존 Module에 새로운 Model을 추가하는 경우도 많다.

---

# 34. 이제 Retrieval-Augmented Generation

Retrieval을 Language Model에 연결한다.

**RAG = Retrieval-Augmented Generation**

강의의 기본 정의는

```text
Query
↓
Retriever
↓
Relevant Documents
↓
LLM Input에 Document 추가
↓
Response
```

다.

즉 **검색으로 생성 과정을 증강(Augment)** 한다.

---

# 35. RAG에서 Data는 Training 때만 쓰이지 않는다

기존 Machine Learning의 익숙한 구조:

```text
Data
↓
Training
↓
Model
```

이었다.

RAG에서는 Data를 **Inference Time**에도 사용한다.

```text
User Query
↓
Inference 시 Document Retrieval
↓
Context에 Data 추가
↓
Generation
```

한다.

이 점이 매우 중요하다.

---

# 36. 과거 RAG와 현재 RAG의 차이

초기 RAG에서는 Language Model을

```text
좋은 Text Generator
```

로 보고,

실제 Knowledge는 주로

```text
External Database
```

에서 가져오는 관점이 강했다.

---

# 37. 현대 LLM은 자체 지식도 가지고 있다

하지만 현재의 Large Language Model은 Pre-training을 통해 많은 Knowledge를 Parameter 자체에 저장하고 있다.

따라서 현재 RAG는

```text
Model 내부 Knowledge
+
External Database Knowledge
```

를 함께 활용하는 System으로 보는 것이 더 적절하다.

---

# 38. Parametric Knowledge

Model Parameter에 저장된 Knowledge다.

```text
Pre-training Data
↓
Training
↓
Model Parameters
```

안에 Pattern 형태로 내재된다.

이를 강의에서는 **Parametric Knowledge**라고 설명한다.

---

# 39. Non-parametric Knowledge

외부

- Database
    
- Document Store
    
- Search Engine
    
- Knowledge Base
    

에 명시적으로 저장된 Knowledge다.

이를 **Non-parametric Knowledge**라고 설명한다.

---

# 40. 현대 RAG의 본질

```text
Parametric Knowledge
+
Non-parametric Knowledge
↓
Response
```

이다.

즉

> **LLM이 이미 알고 있는 것과 검색으로 새롭게 가져온 것을 함께 사용하는 것**

이다.

---

# 41. 왜 RAG가 필요한가? — ① 희귀 Knowledge

Foundation Model이 모든 Knowledge를 Parameter에 동일한 강도로 저장하는 것은 아니다.

Training Data에서 자주 등장하는 Knowledge는 더 강하게 학습될 수 있다.

반대로 희귀한 Entity나 사건은 상대적으로 약하게 기억될 수 있다.

---

# 42. Popular Entity와 Rare Entity

예를 들어 매우 유명한 Entity는 Training Corpus에 수없이 등장한다.

```text
Popular Entity
↓
많은 Training Examples
↓
Parameter에 강하게 반영
```

될 수 있다.

반면

```text
Rare Entity
↓
적은 Training Examples
↓
Parameter Knowledge가 약할 수 있음
```

이다.

---

# 43. Retrieval은 희귀 Knowledge를 직접 가져올 수 있다

Model이 잘 기억하지 못하는 Knowledge도 Database에 문서가 있다면

```text
Query
↓
Relevant Rare Document
↓
LLM Context
```

로 직접 제공할 수 있다.

강의에서는 Entity Popularity가 낮을수록 RAG가 상대적으로 도움이 되는 연구 결과를 설명한다.

---

# 44. 그런데 Popular Entity에서는 RAG가 항상 더 좋은가?

그렇지는 않다.

검색은 완벽하지 않기 때문이다.

Model 자체가 이미 정확하게 알고 있는데 잘못된 Document가 Retrieval되면 오히려 성능이 떨어질 수 있다.

따라서

```text
RAG 사용
→ 무조건 Performance ↑
```

라고 생각하면 안 된다.

---

# 45. 왜 RAG가 필요한가? — ② Knowledge가 오래된다

LLM은 매일 Parameter를 다시 학습하지 않는다.

따라서 Pre-training 이후 발생한

```text
새로운 News
새로운 CEO
새로운 Product
새로운 법률
```

등은 알 수 없을 수 있다.

---

# 46. RAG의 Freshness

External Database/Search Engine은 Model Weight보다 훨씬 자주 Update할 수 있다.

```text
Latest Information
↓
Database Update
↓
즉시 Retrieval 가능
```

하다.

따라서 Model을 다시 Fine-tuning하지 않아도 최신 Knowledge를 사용할 수 있다.

---

# 47. 왜 RAG가 필요한가? — ③ 근거와 검증

LLM Parameter 안의 Knowledge는 출처를 직접 확인하기 어렵다.

하지만 Retrieval된 Document가 있으면

```text
Answer
+
Reference Document
```

를 제공할 수 있다.

따라서 User가 근거를 직접 확인할 수 있다.

---

# 48. Citation이 있다고 답이 자동으로 정확한 것은 아니다

강의에서 중요한 주의사항이다.

```text
Reference 존재
≠
Answer가 반드시 Reference에 Grounded됨
```

이다.

실제로 Citation은 올바른데 Response가 Citation과 무관한 경우도 있을 수 있다.

즉 RAG는 **검증 가능성을 높일 수 있지만 정확성을 자동 보장하지는 않는다.**

---

# 49. 왜 RAG가 필요한가? — ④ 기업 내부 Knowledge

Public Foundation Model은 회사 내부의

- Internal Document
    
- 고객 Data
    
- 업무 Manual
    
- Product Specification
    
- Private Wiki
    

를 알지 못한다.

---

# 50. Enterprise RAG

회사 내부 Database를 Retriever와 연결하면

```text
User
↓
Internal Search
↓
Company Documents
↓
LLM
↓
Company-specific Answer
```

를 만들 수 있다.

이것이 Enterprise AI/AX에서 RAG가 중요한 이유 중 하나다.

---

# 51. RAG 기본 Pipeline

강의에서는 기본적인 RAG를 다음 구조로 설명한다.

```text
User Instruction / Question
        ↓
Query Extraction
        ↓
Retriever
        ↓
Database / Index
        ↓
Top-K Documents
        ↓
User Context
+
Retrieved Documents
        ↓
LLM
        ↓
Response
```

---

# 52. 왜 Query Extraction이 필요한가?

User Input이 항상 짧은 검색어인 것은 아니다.

예:

```text
"이번 프로젝트를 진행 중인데,
지난번 회의에서 논의했던 내용과 비교해서
현재 API 정책이 어떻게 바뀌었는지 알려 줘."
```

처럼 긴 Context가 있을 수 있다.

이 전체를 그대로 Search Query로 쓰는 것이 최선은 아닐 수 있다.

---

# 53. Query Reformulation

그래서 User Request에서 실제 검색에 필요한 Query를 다시 추출하거나 수정한다.

```text
Long User Context
↓
Search Intent 추출
↓
Effective Search Query
```

한다.

이후 Agentic RAG에서는 이 과정도 더 발전한다.

---

# 54. RAG의 Offline 준비 과정

회사 Document가 있다고 하자.

검색하기 전에 일반적으로

```text
Documents
↓
Chunking
↓
Embedding
↓
Vector Database
```

를 준비한다.

---

# 55. Chunking

긴 Document 전체를 하나의 Vector로 만들기보다는 여러 작은 **Chunk**로 나눈다.

```text
Document
├─ Chunk 1
├─ Chunk 2
├─ Chunk 3
└─ ...
```

그 이유는 Query와 관련된 특정 부분만 검색하기 위해서다.

---

# 56. Embedding

각 Chunk를 Embedding Model에 넣는다.

```text
Chunk
↓
Embedding Model
↓
Vector
```

그리고 Vector Database에 저장한다.

---

# 57. Query도 Embedding한다

Inference:

```text
User Query
↓
Embedding Model
↓
Query Vector
```

를 만든다.

그리고 Document Vector들과 Similarity를 계산한다.

---

# 58. Vector Database

Vector Database는

```text
Document Embeddings
```

을 저장하고

```text
Query Vector와 가까운 Vector
```

를 빠르게 찾아주는 역할을 한다.

---

# 59. Semantic Search

```text
Query Vector
↓
Vector DB
↓
Nearest Document Vectors
↓
Top-K Chunks
```

를 가져온다.

이것이 전형적인 Dense Retrieval 기반 RAG다.

---

# 60. LangChain은 어디에 들어가는가?

강의에서는 LangChain이 이런 일련의 Pipeline을 비교적 쉽게 연결할 수 있도록 지원한다고 설명한다.

예:

```text
Document Loading
↓
Chunking
↓
Embedding
↓
Vector DB
↓
Retrieval
↓
Prompt Construction
↓
LLM
↓
Response
```

를 연결한다.

---

# 61. 하지만 이번 강의의 목적은 LangChain API 암기가 아니다

강의에서는 구체적인 LangChain Function을 하나씩 설명하기보다

> **실제 RAG System을 만들 때 어떤 문제가 발생하는가**

에 더 초점을 둔다.

---

# 62. RAG Challenge ① Context 구성

검색 결과를 전부 LLM에 넣을 수는 없다.

예를 들어 Harry Potter 전체 책을 검색했다고 하자.

```text
책 전체
→ Context에 입력
```

하기는 어렵다.

Model의 Context Window가 제한되어 있기 때문이다.

---

# 63. Context Engineering

따라서

> **어떤 정보를 얼마만큼 Context에 넣을 것인가?**

를 설계해야 한다.

강의에서는 이를 **Context Engineering**과 연결한다.

---

# 64. Context Window가 커지면 문제가 사라지는가?

아니다.

Model이 수십만·수백만 Token을 받을 수 있게 되더라도

```text
Input 가능
```

과

```text
모든 Input을 효과적으로 활용
```

은 다른 문제다.

---

# 65. Long Context의 문제

Context가 너무 많으면

```text
Relevant Information 비율 ↓
Noise ↑
Attention 분산
Performance ↓
```

할 수 있다.

따라서 Context Window가 커져도 적절한 Context Selection은 여전히 중요하다.

---

# 66. Context 압축

검색된 Document를 그대로 넣는 대신

```text
Top-K Documents
↓
Summarization
↓
Compressed Context
↓
LLM
```

처럼 추가 Module을 사용할 수도 있다.

즉 Retrieval 이후에도 Context Processing이 필요할 수 있다.

---

# 67. RAG Challenge ② Retrieval Noise

Retriever는 완벽하지 않다.

```text
Top-K Documents
```

안에는 관련 없는 Document가 섞일 수 있다.

---

# 68. LLM의 문제

일반 LLM은 주어진 Context를

> **유용한 정보라고 가정하고 최대한 활용하려는 경향**

이 있을 수 있다.

그래서 잘못된 Document가 들어가면 그 정보를 이용해 오답을 만들 수 있다.

---

# 69. Context에 City Name이 들어가면

강의에서는 특정 인물의 출생 도시를 질문했을 때 Context에 우연히

```text
Saratoga
```

가 있으면 Saratoga라고 답하고,

다른 Context에서는

```text
Sunnyvale
```

라고 답하는 사례를 설명한다.

즉 Model은 Retrieval Context를 매우 강하게 참고할 수 있다.

---

# 70. Retrieval Noise에 Robust한 Model

이를 해결하기 위해 Training 단계부터

```text
Correct Document
+
Irrelevant / Wrong Documents
```

를 함께 넣는다.

그리고

```text
잘못된 Document는 무시하고
올바른 Document만 이용
```

하도록 훈련할 수 있다.

---

# 71. Noise Robustness Training

예:

```text
Question:
2022년 Nobel Prize 수상자는?

Context:
2022년 관련 Document
+
2021년 관련 Document
```

를 함께 제공한다.

Model은 2021년 Document를 무시하고 올바른 Evidence를 선택해야 한다.

---

# 72. Negative Rejection

더 극단적인 경우에는 올바른 Document 자체를 제거한다.

```text
Question
+
관련 없는 Documents만 제공
```

한다.

이때 Model이

```text
Context에 근거가 없어 답할 수 없다.
```

고 거부하도록 훈련한다.

---

# 73. 왜 Negative Rejection이 중요한가?

실제 Service에서는 항상 정답 Document가 Retrieval된다는 보장이 없다.

따라서

```text
Document 없음
↓
아무거나 답변
```

하는 것보다

```text
Document 없음
↓
답변 거부 / 추가 검색
```

하는 것이 안전하다.

---

# 74. Train/Test 환경 차이도 고려해야 한다

Training Dataset에서는 유명 Entity에 대한 좋은 Document가 충분할 수 있다.

하지만 실제 Service에서는

- 새로운 Trend
    
- 신인
    
- 희귀 기업
    
- 동명이인
    

등을 검색할 수 있다.

이 경우 Retrieval Noise가 훨씬 많아질 수 있다.

따라서 일부러 어려운 Retrieval 상황을 Training에 포함해 Robustness를 높일 수 있다.

---

# 75. RAG Challenge ③ Parametric vs Non-parametric Knowledge Conflict

다음 문제는 두 Knowledge Source가 충돌하는 것이다.

```text
LLM Parameter:
A

Retrieved Document:
B
```

이면 무엇을 믿어야 할까?

---

# 76. 예: 최신 정치 정보

Model이 과거 Knowledge를 가지고 있고 외부 Document에는 현재 정보가 있을 수 있다.

이때 External Document를 더 우선해야 할 수 있다.

---

# 77. 하지만 항상 Document가 옳은 것은 아니다

Internet Search에서는

- 오래된 Document
    
- 잘못된 Document
    
- Fake Information
    

이 검색될 수 있다.

따라서 단순히

```text
무조건 Retrieval Document를 믿어라.
```

라고 할 수도 없다.

---

# 78. Domain RAG에서는 Grounding Rule을 더 명확히 할 수 있다

회사 내부 Document처럼 신뢰할 수 있는 Source라면

```text
Retrieved Internal Document
>
Model Internal Knowledge
```

가 되도록 Training/Prompting할 수 있다.

---

# 79. 법률 사례

강의에서는 AI Lawyer를 예로 든다.

General LLM은 미국 Law Knowledge를 많이 가지고 있을 수 있다.

하지만 한국에서 사용하는 Legal AI라면 한국 Law Database를 검색해야 한다.

```text
Internal Parametric Knowledge:
미국 법률

Retrieved Document:
한국 법률
```

이 충돌하면 Korean Legal Document를 Ground Truth Source로 사용해야 한다.

---

# 80. Grounding

이처럼 Model이 특정 External Evidence에 기반해서 답변하도록 만드는 것을 **Grounding** 관점으로 이해할 수 있다.

```text
Answer
↓
Retrieved Evidence에 근거
```

하도록 한다.

---

# 81. Knowledge Conflict는 매우 어려운 문제다

현실의 정보 자체가 항상 하나의 정답으로 합의되어 있는 것도 아니다.

예:

```text
건강 정보

Diet

Nutrition

의학적 조언
```

은 Source마다 의견이 다를 수 있다.

즉 Retrieval System이 여러 Source를 가져왔다고 해서 무엇이 Truth인지 자동으로 해결되지는 않는다.

강의에서도 이 문제를 쉽지 않은 연구 문제로 설명한다.

---

# 82. RAG Challenge ④ Multi-document Reasoning

어떤 질문은 Document 하나로 답할 수 없다.

예:

```text
ChatGPT iOS App 출시일은?
+
ChatGPT API 출시일은?
```

이라고 묻는다면

```text
Document A
→ iOS 정보

Document B
→ API 정보
```

를 함께 사용해야 한다.

---

# 83. Multi-hop Question

여러 Document에서 각각 필요한 정보를 찾고 이를 결합해야 하는 문제를 **Multi-hop Reasoning** 관점으로 볼 수 있다.

```text
Query
↓
Document A에서 정보 1
↓
Document B에서 정보 2
↓
정보 1 + 정보 2 결합
↓
Answer
```

이다.

---

# 84. Deep Research는 훨씬 복잡하다

예:

```text
"최근 관련 논문들을 조사해서
보고서를 작성해 줘."
```

는 몇 개 Document로 끝나지 않는다.

```text
많은 Search Queries
↓
수십 개 Papers
↓
Selection
↓
Reading
↓
Comparison
↓
Synthesis
↓
Report
```

가 필요하다.

---

# 85. 초기 RAG의 한계

기본적인 RAG는

```text
Search
↓
Top-K
↓
LLM
```

이라는 **정적인 Pipeline**이다.

한 번 정해 놓은 Flow대로 실행한다.

---

# 86. 복잡한 문제에서는 Decision이 필요하다

Deep Research에서는 System이 스스로 결정해야 한다.

```text
검색을 더 해야 하나?

Query를 다시 써야 하나?

다른 Search Engine을 쓸까?

Document를 요약해야 하나?

관련 없는 Document인가?

새로운 질문을 만들어야 하나?
```

등이다.

---

# 87. Agentic RAG

이러한 Decision을 LLM/Agent가 수행하게 하면 RAG가 **Agentic System**으로 발전한다.

```text
Question
↓
Agent
↓
Search 필요?
├─ No → Answer
└─ Yes
    ↓
Query 생성
    ↓
Search
    ↓
Result 평가
    ↓
충분한가?
    ├─ No → 다시 Search
    └─ Yes → Synthesis
```

이다.

---

# 88. Static RAG vs Agentic RAG

### Static RAG

```text
Query
→ Search
→ Top-K
→ LLM
```

고정 Workflow.

### Agentic RAG

```text
Query
↓
Reasoning
↓
Search 여부 결정
↓
Query Reformulation
↓
Search
↓
Result Evaluation
↓
필요하면 반복
↓
Answer
```

동적인 Workflow다.

강의에서는 이런 복합적 Retrieval System이 결국 다음 차시의 **Agent**로 이어진다고 설명한다.

---

# 89. Counterfactual Robustness

검색 결과에는 사실이 아닌 Document도 들어올 수 있다.

따라서

```text
False Document
```

가 있어도 Model이 무조건 믿지 않는 능력이 필요하다.

강의에서는 이러한 반사실적 정보에 대한 Robustness도 발전시켜야 할 능력으로 언급한다.

---

# 90. RAG가 완벽하지 않은 실제 사례

강의에서는 Search 기반 AI System도 우스운 오답을 만드는 사례를 소개한다.

예:

```text
"하루에 돌을 몇 개 먹어야 하나?"
```

라는 질문에 Search Result를 잘못 해석해

```text
"매일 작은 돌을 하나 먹는 것이 좋다."
```

는 식의 답을 생성하는 사례다.

---

# 91. 왜 이런 일이 발생하는가?

문제는 RAG Pipeline의 어느 한 부분만 실패해도 전체 답변이 틀릴 수 있다는 것이다.

```text
Bad Query
↓
Bad Retrieval
↓
Bad Context
↓
LLM이 Context를 신뢰
↓
Bad Response
```

가 가능하다.

---

# 92. RAG는 자동 Fact Checker가 아니다

RAG를 붙였다고

```text
Hallucination = 0
```

이 되는 것은 아니다.

오히려

```text
Model Hallucination
+
Retrieval Error
```

라는 두 종류의 문제가 함께 존재할 수도 있다.

---

# 93. Search System은 지속적으로 유지보수해야 한다

강의에서는 Search 결과가

```text
Top-1이 언제나 정확
```

한 System이 아니기 때문에 계속

- Evaluation
    
- 개선
    
- Ranking Tuning
    
- Data Update
    
- Maintenance
    

해야 한다고 강조한다.

---

# 94. RAG 최종 정의

이번 강의의 표현을 정리하면

> **RAG는 Search/Retrieval System을 Language Model의 외부 Tool로 연결하여 Model Parameter 안의 Parametric Knowledge와 Database에서 검색한 Non-parametric Knowledge를 함께 이용해 답변을 생성하는 Augmented Language Model 구조다.**

---

# 95. Retrieval은 LLM의 Tool이다

강의 마지막에서는 Retrieval을

```text
LLM이 사용할 수 있는
외부 Tool
```

이라는 관점으로 설명한다.

이 관점이 중요하다.

왜냐하면 다음 차시 Agent에서는 Retrieval뿐 아니라

```text
Search
Calculator
Code
API
Browser
Database
```

등 여러 Tool을 LLM에 붙이게 되기 때문이다.

---

# 96. Embedding API를 이용한 간단한 구현

Dense Retrieval Model을 직접 Training할 필요도 없다.

OpenAI나 다른 LLM Provider의 **Embedding Service**를 사용할 수 있다.

```text
Text
↓
Embedding API
↓
Vector
```

를 얻고,

그 Vector를 Vector Database에 저장하여 Semantic Search를 구현할 수 있다.

강의에서는 이를 Retrieval System을 비교적 쉽게 구축하는 방법으로 소개한다.

---

# 97. Sparse vs Dense Retriever

|항목|Sparse Retriever|Dense Retriever|
|---|---|---|
|대표|TF-IDF 등|Neural Embedding|
|기준|Keyword/통계|Semantic Meaning|
|GPU|불필요 가능|필요 가능|
|속도|빠름|상대적으로 무거움|
|해석|쉬운 편|어려운 편|
|의미적 유연성|낮음|높음|
|운영|전통 Search에서 강함|Semantic Search에 강함|

실무에서는 둘 중 하나만 고집하기보다 함께 사용할 수 있다.

---

# 98. Bi-encoder vs Cross-encoder

```text
Bi-encoder
=
미리 Document Vector 생성
→ 빠른 Retrieval

Cross-encoder
=
Query와 Document를 함께 처리
→ 정밀한 Ranking
```

따라서

```text
Bi-encoder
→ Candidate Retrieval

Cross-encoder
→ Reranking
```

처럼 조합할 수 있다.

---

# 99. Retrieval System을 계층적으로 보면

```text
Huge Document Pool
      ↓
Cheap Candidate Generator
      ↓
Thousands
      ↓
Dense Retriever
      ↓
Dozens
      ↓
Cross-encoder Reranker
      ↓
Top-K
      ↓
LLM
```

이다.

핵심은

> **싼 Model은 넓게 찾고, 비싼 Model은 작은 후보를 정밀하게 본다.**

는 것이다.

---

# 100. Parametric vs Non-parametric Knowledge

|구분|Parametric|Non-parametric|
|---|---|---|
|위치|Model Parameters|External DB/Documents|
|생성|Pre-training|저장/Indexing|
|Update|Training 필요|DB Update|
|최신성|떨어질 수 있음|빠르게 갱신 가능|
|희귀 Knowledge|약할 수 있음|직접 저장 가능|
|Source 확인|어려움|가능|

---

# 101. Fine-tuning과 RAG 차이

### Fine-tuning

```text
Knowledge / Behavior
↓
Parameter Update
```

한다.

### RAG

```text
External Knowledge
↓
Inference Context
```

에 넣는다.

즉 RAG는 일반적으로 Model Weight를 수정하지 않고도 Knowledge를 추가할 수 있다.

---

# 102. RAG와 Long Context도 다르다

Long-context Model에 모든 Document를 넣을 수 있다고 해서 Retrieval이 필요 없어지는 것은 아니다.

```text
많이 넣을 수 있다
≠
필요한 정보를 잘 찾는다
```

이기 때문이다.

Retrieval은 Context 자체를 **선별**하는 역할도 한다.

---

# 103. RAG와 Search Engine도 다르다

Search Engine:

```text
Query
↓
Documents 반환
```

RAG:

```text
Query
↓
Documents Retrieval
↓
Documents를 LLM Context로 사용
↓
Natural Language Answer 생성
```

한다.

즉 Search 위에 Generation 단계가 붙는다.

---

# 104. RAG와 Agent의 차이

### 기본 RAG

Search Flow가 미리 정해져 있다.

```text
검색
→ Document
→ Answer
```

### Agent

LLM이

```text
검색할까?

몇 번 검색할까?

어떤 Query를 쓸까?

다른 Tool도 써야 하나?
```

를 스스로 결정한다.

---

# 105. RAG에서 성능을 좌우하는 부분

RAG가 실패하면 흔히 LLM만 의심하기 쉽다.

하지만 Pipeline을 분리해서 봐야 한다.

```text
Query Generation

Retrieval

Ranking

Chunking

Context Selection

Context Ordering

LLM Generation
```

각 단계가 모두 전체 Performance에 영향을 준다.

---

# 106. Retrieval이 실패하면 Generation이 잘할 수 없다

```text
Correct Evidence를 못 찾음
↓
LLM에게 Evidence 없음
↓
Correct Answer 어려움
```

이다.

따라서 RAG 평가에서는 Generator뿐 아니라 Retriever Performance도 중요하다.

---

# 107. Retrieval이 너무 많이 찾아와도 문제다

```text
K 너무 작음
→ Correct Document 누락

K 너무 큼
→ Noise 증가
```

한다.

따라서 `Top-K`도 중요한 Hyperparameter가 될 수 있다.

---

# 108. RAG의 Knowledge Flow

```text
User Knowledge Need
        ↓
Query
        ↓
Retriever
        ↓
Non-parametric Knowledge
        ↓
Context
        ↓
LLM
        ↕
Parametric Knowledge
        ↓
Answer
```

즉 Response는 두 Knowledge Source의 결합 결과다.

---

# 109. RAG의 네 가지 주요 Motivation

강의 내용을 기준으로 기억하면 다음 네 가지가 핵심이다.

```text
1. Rare Knowledge
   → Parameter에 약하게 저장될 수 있음

2. Outdated Knowledge
   → 최신 DB로 보완

3. Verification / Citation
   → 외부 Evidence 제공

4. Private / Enterprise Knowledge
   → Model이 학습하지 않은 Data 사용
```

---

# 110. RAG의 주요 Challenge도 네 축으로 기억

강의 후반의 구조를 정리하면

```text
1. Context Construction
   → 무엇을 얼마나 넣을까?

2. Retrieval Noise
   → 틀린 문서를 어떻게 무시할까?

3. Knowledge Conflict
   → Internal Knowledge vs Document

4. Complex Reasoning
   → 여러 Document를 어떻게 검색·통합할까?
```

이다.

---

# 111. Challenge 1 해결 방향

```text
Chunking

Reranking

Summarization

Context Compression

Context Engineering
```

등을 생각할 수 있다.

---

# 112. Challenge 2 해결 방향

```text
Better Retriever

Noise Robustness Training

Relevant + Irrelevant Document Training

Negative Rejection
```

등이다.

---

# 113. Challenge 3 해결 방향

```text
Source Grounding

Trusted Database 우선

Conflict-aware Training

Answer Refusal
```

등이다.

---

# 114. Challenge 4 해결 방향

```text
Multi-hop Retrieval

Query Expansion

Iterative Search

Document Synthesis

Agentic RAG
```

이다.

---

# 115. 자주 헷갈리는 점 ① — RAG는 Training 방법인가?

기본적인 RAG는 반드시 Training을 요구하지 않는다.

이미 학습된

```text
Retriever
+
LLM
```

을 연결해 Inference Pipeline으로 구성할 수 있다.

---

# 116. 자주 헷갈리는 점 ② — RAG는 LLM이 모르는 지식을 Parameter에 넣는가?

아니다.

일반적으로 External Document를 **Context**로 전달한다.

Model Parameter가 반드시 바뀌는 것은 아니다.

---

# 117. 자주 헷갈리는 점 ③ — Retriever = Vector Database인가?

아니다.

Vector DB는 Document Vector를 저장하고 검색하는 Infrastructure의 일부다.

Retriever는

```text
Query를 받고
관련 Document를 선택하는
전체 Retrieval Logic/System
```

으로 더 넓게 볼 수 있다.

---

# 118. 자주 헷갈리는 점 ④ — Dense Retriever가 Sparse보다 항상 좋은가?

아니다.

Dense Retriever는 Semantic Flexibility가 좋지만 Compute와 운영 비용이 존재한다.

강의에서는 실제 System에서 여러 Retriever를 함께 쓰는 것이 일반적이라고 설명한다.

---

# 119. 자주 헷갈리는 점 ⑤ — Cross-encoder가 가장 정확하니 처음부터 모든 Document에 쓰면 되는가?

대규모 Document Collection에서는 너무 비싸다.

그래서 Candidate 수를 먼저 줄인 뒤 Reranking에 사용한다.

---

# 120. 자주 헷갈리는 점 ⑥ — Bi-encoder가 빠른 이유는?

Document Embedding을 미리 계산해 저장할 수 있기 때문이다.

새 Query만 Encoding하면 된다.

---

# 121. 자주 헷갈리는 점 ⑦ — RAG를 붙이면 최신 정보 문제가 완전히 해결되는가?

아니다.

Database가 최신이어야 하고 Retriever가 올바른 Document를 찾아야 한다.

---

# 122. 자주 헷갈리는 점 ⑧ — Citation이 있으면 Hallucination이 없는가?

아니다.

Model이 Citation과 상관없는 내용을 생성할 수도 있다.

Reference와 Answer의 Grounding까지 확인해야 한다.

---

# 123. 자주 헷갈리는 점 ⑨ — Context Window가 매우 크면 RAG가 필요 없는가?

아니다.

많은 Context를 넣는 것과 필요한 Context를 정확히 선별하는 것은 별개의 문제다.

---

# 124. 자주 헷갈리는 점 ⑩ — 검색 결과는 모두 사실인가?

아니다.

Retriever는 **관련성**을 찾는 System이지 Truth를 완벽하게 판별하는 System은 아니다.

---

# 125. 자주 헷갈리는 점 ⑪ — Model Knowledge와 Document가 다르면 항상 Document가 맞는가?

아니다.

Domain 내부의 신뢰된 Database라면 Document를 우선하도록 설계할 수 있지만 Public Web에는 잘못된 정보도 존재한다.

---

# 126. 자주 헷갈리는 점 ⑫ — 관련 Document가 없으면 Parametric Knowledge로 그냥 답하면 되는가?

Application에 따라 다르다.

법률·기업 내부 Data처럼 Evidence 기반 답변이 중요한 System에서는 Document가 없다면 답변을 거부하도록 Training할 수도 있다.

---

# 127. 자주 헷갈리는 점 ⑬ — RAG는 Agent인가?

기본 RAG는 고정 Pipeline이다.

Search 여부와 반복 Search 등을 Model이 스스로 결정하기 시작하면 Agentic System으로 발전한다.

---

# 128. 자주 헷갈리는 점 ⑭ — LangChain = RAG인가?

아니다.

LangChain은 RAG를 포함해

```text
LLM
Prompt
Retriever
Tool
Agent
```

등 여러 Component를 연결하는 Framework다.

RAG는 그 Framework로 만들 수 있는 System Pattern 중 하나다.

---

# 129. 앞 차시와 연결

## 1차시 — Post-training

```text
Language Model 자체를 수정

Pre-training
↓
Instruction Tuning
↓
RLHF
↓
Alignment
```

---

## 2차시 — Augmentation

이번에는 Model Weight를 반드시 수정하지 않는다.

```text
Language Model
+
External Search Tool
↓
Augmented Language Model
```

이다.

---

# 130. 두 접근의 차이

```text
Post-training
= Model 내부 Behavior를 개선

RAG
= Model 외부 Knowledge를 연결
```

따라서 둘은 경쟁 관계가 아니라 같이 사용할 수 있다.

```text
Post-trained LLM
+
RAG
```

가 가능하다.

---

# 131. 그리고 다음 차시 Agent로

RAG에서 Retrieval은 최초의 대표적인 **Tool**이다.

```text
LLM
+
Search
```

에서 시작해

```text
LLM
+
Search
+
Calculator
+
Code
+
Browser
+
APIs
```

로 Tool이 늘어난다.

그리고 LLM이

```text
어떤 Tool을
언제
몇 번
어떤 순서로
사용할지
```

스스로 결정하면 **Agent**가 된다.

---

# 132. 이번 강의에서 반드시 기억할 핵심

### 1. Information Retrieval은 Query와 관련된 Document를 거대한 Collection에서 찾아 Ranking하는 기술이다.

### 2. RAG는 Retrieval된 Document를 LLM Context로 제공하여 Response Generation을 증강한다.

### 3. RAG에서는 Data를 Training뿐 아니라 Inference Time에도 활용한다.

### 4. Information Retrieval에는 Sparse Retriever와 Dense Retriever가 있다.

### 5. TF-IDF는 대표적인 전통적 Sparse Retrieval 방법이다.

### 6. TF는 현재 Document에서 Word가 얼마나 자주 등장하는지를 본다.

### 7. IDF는 전체 Document에서 흔하지 않은 Word에 상대적으로 큰 중요도를 준다.

### 8. Sparse Retrieval은 빠르고 해석하기 쉽지만 Semantic Flexibility가 제한될 수 있다.

### 9. Dense Retriever는 Query와 Document를 Embedding Vector로 변환해 Semantic Similarity를 계산한다.

### 10. Query와 Positive Document는 가깝게, Negative Document는 멀게 학습하는 Contrastive Training을 사용할 수 있다.

### 11. Bi-encoder는 Query와 Document를 독립적으로 Encoding한다.

### 12. Bi-encoder에서는 Document Embedding을 미리 계산하고 Caching할 수 있어 대규모 Retrieval에 적합하다.

### 13. Cross-encoder는 Query와 Document를 하나의 Sequence로 함께 Encoding한다.

### 14. Cross-encoder는 Query–Document 간 직접 Interaction을 Modeling할 수 있어 정밀하지만 계산량이 크다.

### 15. 실무 Search System은 한 Retriever만 사용하는 것이 아니라 여러 Stage와 Model을 조합한다.

### 16. 먼저 가벼운 Candidate Generation으로 많은 후보를 만들고 이후 더 비싼 Model로 후보를 줄인다.

### 17. Cross-encoder는 소수 Candidate를 최종적으로 다시 Ranking하는 Reranker로 활용할 수 있다.

### 18. 현대 RAG에서는 LLM 자체도 많은 Knowledge를 가지고 있기 때문에 Parametric Knowledge와 Non-parametric Knowledge를 함께 사용한다.

### 19. Parametric Knowledge는 Model Parameter 안에 학습된 Knowledge다.

### 20. Non-parametric Knowledge는 Database/Document Store처럼 외부에 저장된 Knowledge다.

### 21. RAG의 첫 번째 Motivation은 Foundation Model이 희귀한 Knowledge를 충분히 기억하지 못할 수 있다는 것이다.

### 22. 두 번째 Motivation은 Model의 Knowledge가 시간이 지나면서 Outdated될 수 있다는 것이다.

### 23. External Database는 Model Weight보다 쉽게 Update할 수 있어 최신 정보를 제공할 수 있다.

### 24. 세 번째 Motivation은 Retrieved Document를 Reference로 제공해 User가 Answer를 검증할 수 있다는 것이다.

### 25. 하지만 Citation이 있다고 해서 Answer가 자동으로 정확한 것은 아니다.

### 26. 네 번째 Motivation은 기업 내부 Data처럼 Foundation Model이 가지고 있지 않은 Private Knowledge를 사용할 수 있다는 것이다.

### 27. 기본 RAG Pipeline은 User Input → Query Extraction → Retrieval → Top-K Document → Context Construction → LLM → Response로 구성된다.

### 28. Long User Input에서 Retrieval에 필요한 Search Query를 별도로 추출하거나 Rewrite할 수 있다.

### 29. RAG Dataset 준비에서는 Document를 Chunk로 분리할 수 있다.

### 30. 각 Chunk를 Embedding Vector로 변환하여 Vector Database에 저장할 수 있다.

### 31. Inference 시 Query를 Embedding하고 Document Embedding과 Similarity를 계산해 관련 Chunk를 검색한다.

### 32. LangChain은 Document Loading, Chunking, Embedding, Retrieval, Prompt, LLM Generation 같은 Component를 연결하는 데 활용할 수 있다.

### 33. RAG의 첫 번째 Challenge는 제한된 Context Window 안에 어떤 Document를 어떻게 넣을지 결정하는 Context Engineering이다.

### 34. Context Window가 커져도 불필요한 Context가 많으면 Performance가 떨어질 수 있으므로 Context Selection은 여전히 중요하다.

### 35. Retrieved Document를 요약하거나 압축한 뒤 Context에 넣는 방법도 고려할 수 있다.

### 36. 두 번째 Challenge는 Retriever가 Noise Document를 가져올 수 있다는 것이다.

### 37. 일반 LLM은 주어진 Context를 적극적으로 활용하려 하기 때문에 잘못된 Document에 의해 오답이 발생할 수 있다.

### 38. Correct Document와 Noise Document를 함께 Training하여 잘못된 Evidence를 무시하는 Robustness를 학습할 수 있다.

### 39. 정답 Evidence가 아예 없는 경우에는 답변을 거부하도록 Negative Rejection Training을 할 수 있다.

### 40. 세 번째 Challenge는 Model 내부 Knowledge와 Retrieved Knowledge가 충돌할 수 있다는 것이다.

### 41. 신뢰할 수 있는 Domain Database를 사용하는 Application에서는 Retrieved Evidence에 더 Grounding하도록 Model을 설계할 수 있다.

### 42. 하지만 Public Web의 Retrieved Document 역시 틀릴 수 있기 때문에 Retrieval Knowledge가 무조건 Truth인 것은 아니다.

### 43. 네 번째 Challenge는 여러 Document를 함께 사용해야 하는 Multi-hop/Complex Reasoning이다.

### 44. 복잡한 Research Task에서는 한 번의 Top-K Retrieval로 충분하지 않을 수 있다.

### 45. Query Expansion, 반복 Search, Summarization, Document Integration 등이 필요할 수 있다.

### 46. 기본 RAG는 Search→Document→LLM으로 이어지는 정적인 Workflow다.

### 47. Agentic RAG에서는 LLM이 검색 여부, 검색 횟수, Query 수정, 결과 평가 등을 자율적으로 결정한다.

### 48. False/Counterfactual Document가 Retrieval되어도 올바르게 판단할 수 있는 Robustness도 중요하다.

### 49. RAG가 Hallucination을 자동으로 제거하는 것은 아니다.

### 50. Search Error와 Generation Error가 함께 존재할 수 있다.

### 51. 실제 Search System은 지속적인 Evaluation과 Maintenance가 필요하다.

### 52. 강의에서는 RAG를 LLM에 Search라는 외부 Tool을 붙인 구조로 최종 정리한다.

---

# 133. 이번 강의 전체 논리

```text
LLM은 강력하다.
      ↓
하지만
모든 Knowledge를
Parameter에 저장할 수 있는가?
      ↓
아니다.
      ↓
희귀한 지식은 약할 수 있고
Knowledge는 오래될 수 있고
Private Data는 알 수 없다.
      ↓
그럼 외부에서 찾아오자.
      ↓
Information Retrieval


Retrieval이란?
      ↓
Query
↓
Document Collection
↓
관련 Document Ranking
↓
Top-K


어떻게 검색할까?
      ↓
Sparse Retrieval
↓
TF-IDF

또는
↓
Dense Retrieval
↓
Embedding Similarity


Dense Retriever
      ↓
Bi-encoder
vs
Cross-encoder

Bi-encoder
= 빠르다.
  Document를 미리 Embedding 가능.

Cross-encoder
= 정확하지만 비싸다.
      ↓
그러면 같이 쓰자.
      ↓
Cheap Candidate Generation
↓
Dense Retrieval
↓
Cross-encoder Reranking


이 검색 System을
LLM에 붙인다.
      ↓
RAG


User Query
↓
Retriever
↓
Top-K Documents
↓
LLM Context
↓
Answer


이때 Knowledge가 두 종류다.
      ↓
LLM Parameter
= Parametric Knowledge

External DB
= Non-parametric Knowledge
      ↓
두 Knowledge를 함께 사용


왜 RAG가 필요한가?
      ↓
Rare Knowledge

Outdated Knowledge

Verification / Reference

Private Enterprise Data


그런데 RAG도 어렵다.
      ↓

[1]
Context가 너무 많다.
↓
Context Engineering

[2]
검색이 틀릴 수 있다.
↓
Noise Robustness
Negative Rejection

[3]
Model Knowledge와
Document Knowledge가 다르다.
↓
Knowledge Conflict
Grounding

[4]
한 문서로 답할 수 없다.
↓
Multi-hop Retrieval
Deep Research


기본 RAG
= 정적 Pipeline
      ↓
복잡한 Task에서는
검색 과정 자체에 판단 필요
      ↓
Search?
Query Rewrite?
More Documents?
Summarize?
Search Again?
      ↓
Agentic RAG
      ↓
Agent
```

---

# 134. 이번 강의 핵심 한 문장

> **Retrieval-Augmented Generation은 사용자의 Query와 관련된 Document를 Information Retrieval System에서 찾아 Language Model의 Context에 제공함으로써 Model Parameter에 저장된 Parametric Knowledge와 Database의 Non-parametric Knowledge를 함께 활용하는 Augmented Language Model 구조이며, Sparse/Dense Retrieval과 Bi-encoder·Cross-encoder를 단계적으로 조합해 검색 성능과 비용을 조절할 수 있지만 실제 System에서는 Context 구성, Retrieval Noise, 내부·외부 Knowledge Conflict, Multi-document Reasoning 등의 문제가 존재하므로 이를 해결하기 위해 RAG는 점차 검색 여부와 방법을 스스로 판단하는 Agentic RAG로 발전한다.**

가장 짧게 기억하면 다음과 같다.

```text
Retrieval
= Query와 관련된 문서를 찾는다.

RAG
= 찾은 문서를
  LLM에게 읽히고 답하게 한다.

Parametric Knowledge
= LLM 머릿속 지식

Non-parametric Knowledge
= 외부 DB의 지식

Sparse
= 단어 중심 검색

Dense
= 의미 Vector 중심 검색

Bi-encoder
= 빠른 검색

Cross-encoder
= 느리지만 정밀한 Reranking

RAG의 문제
= 검색한 문서가
  항상 맞는 것은 아니다.

Agentic RAG
= 검색 과정 자체를
  LLM이 판단하고 반복한다.
```