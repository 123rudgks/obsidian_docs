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

