
## 1. `nunique()`
`nunique()`는 **서로 다른 값의 개수**를 반환한다.
예:

    y = [0, 0, 1, 1, 2]
    y.nunique()  # 3
### 기억
`nunique()`  
→ 고유한 값의 **종류가 몇 개인가?**

---

## 2. `value_counts()`

`value_counts()`는 **각 값이 몇 번 등장했는지** 계산하여 Series 형태로 반환한다.

예:

    y = [0, 0, 1, 1, 1, 2]

    y.value_counts()

결과:

    1    3
    0    2
    2    1
### `nunique()`와 차이

`nunique()`  
→ 종류가 몇 개?

`value_counts()`  
→ 각 종류가 몇 개?

---

## 3. `loc[]`

`loc[]`는 DataFrame에서 **원하는 행과 열을 선택**할 때 사용한다.

기본 형태:

    df.loc[행, 열]

예:

    df.loc[3, "quality"]

→ index가 `3`인 행에서 `"quality"` 열의 값을 가져온다.

조건식을 이용해서 행을 선택할 수도 있다.

    df.loc[행 조건, 가져올 열]

예:

    df.loc[df["age"] >= 20, "name"]

→ age가 20 이상인 행들의 name 열을 가져온다.

---

## 4. `corr(numeric_only=True)`

`corr()`는 **숫자형 컬럼들 사이의 상관계수**를 계산한다.

    df.corr(numeric_only=True)

`numeric_only=True`는

**숫자형 컬럼만 사용해서 상관관계를 계산하라**

특정 컬럼과 다른 컬럼들의 상관관계만 확인하고 싶다면:

    df.corr(numeric_only=True)["alcohol"]

상관관계의 **방향은 무시하고 강도만 비교**하고 싶다면 절댓값을 사용한다.

    df.corr(numeric_only=True)["alcohol"].abs()

자기 자신과의 상관관계는 항상 `1`이므로 제외하려면:

    df.corr(numeric_only=True)["alcohol"].drop("alcohol")

가장 강한 상관관계를 가진 컬럼을 찾으려면:
```python
 df.corr(numeric_only=True)["alcohol"].drop("alcohol").abs().idxmax()
```

---
# Heatmap 시험 직전 정리

## 1. `np.ones_like(corr, dtype=bool)`

`ones_like()`는 **기준이 되는 배열과 같은 모양(shape)의 배열을 새로 만드는 함수**이다.

```python
np.ones_like(corr)
```

→ `corr`과 **같은 행/열 크기**를 가진 배열을 만들고, 모든 값을 `1`로 채운다.

예를 들어 `corr`이 3×3이면:

```text
[[1, 1, 1],
 [1, 1, 1],
 [1, 1, 1]]
```

### `dtype=bool`

```python
np.ones_like(corr, dtype=bool)
```

`dtype=bool`은 배열의 값 타입을 **True / False 형태의 boolean 타입으로 만들라**는 의미이다.

따라서 결과는:

```text
[[True, True, True],
 [True, True, True],
 [True, True, True]]
```

가 된다.

### 기억

`ones_like(corr)`  
→ corr과 **같은 모양의 1로 채운 배열**

`dtype=bool`  
→ 값을 **True / False 타입으로 생성**

---

## 2. `np.triu()`

`triu()`는 배열에서 **대각선을 포함한 위쪽 삼각형 부분만 남기는 함수**이다.

예:

```text
[[True, True, True],
 [True, True, True],
 [True, True, True]]
```

에 `np.triu()`를 적용하면:

```text
[[True,  True,  True],
 [False, True,  True],
 [False, False, True]]
```

가 된다.

즉:

```python
mask = np.triu(
    np.ones_like(corr, dtype=bool)
)
```

는 `corr`과 같은 크기의 boolean 배열을 만든 뒤,  
**대각선과 위쪽 부분을 True로 만든 mask**를 생성하는 코드이다.

Heatmap에서는 `mask=True`인 부분을 가리므로  
결과적으로 **상관관계 행렬의 아래쪽 부분만 표시**할 수 있다.

### 기억

`triu()`  
→ 대각선 포함 **위쪽 삼각형**

`mask=True`  
→ 해당 부분을 **숨김**

즉:

```text
triu → 위쪽을 True
mask → True를 숨김
결과 → 아래쪽만 보임
```

---

## 3. `annot=True`

```python
sns.heatmap(
    data=corr,
    annot=True
)
```

`annot`는 **annotation(주석)**의 약자이다.

`annot=True`를 사용하면 Heatmap의 각 칸에  
**실제 상관계수 값을 숫자로 표시**한다.

예:

```text
0.82
-0.31
1.00
```

처럼 색깔뿐만 아니라 실제 수치도 확인할 수 있다.

### 기억

`annot=True`  
→ Heatmap 각 칸에 **숫자 표시**

---

## 전체 코드

```python
mask = np.triu(
    np.ones_like(corr, dtype=bool)
)

sns.heatmap(
    data=corr,
    annot=True,
    fmt=".2f",
    cmap="coolwarm",
    mask=mask
)
```

> `cmp="coolwarm"`이 아니라 `cmap="coolwarm"`이다.

---
## 1. `sort_values()`

Series의 **값을 기준으로 정렬**한다.

```python
series.sort_values(ascending=False)
```

- `ascending=True` → 작은 값부터
- `ascending=False` → 큰 값부터

---

## 2. `.index`

Series에서 **값이 아니라 각 값의 이름(index)** 을 가져온다.

예:

```text
proline       0.8
flavanoids    0.6
alcohol       0.3
```

여기서:

```python
series.index
```

결과:

```text
Index(["proline", "flavanoids", "alcohol"])
```

---

```
fillna(df_missing.mean())
```
→ NaN을 컬럼 평균으로 채움

```python
df_no_outliers = df_filled[~df_filled.index.isin(outliers_alcohol.index)]
```

---
```python
# 1. Train/test 데이터를 나누어 `X_train`, `X_test`, `y_train`, `y_test`에 할당해주세요.
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X,y,test_size=0.3,stratify=y,random_state=42)

  

# 2. `StandardScaler`를 사용해 표준화를 진행해주세요.
from sklearn.preprocessor import StandardScaler
scaler = StandardScaler()

  

scaler.fit(X_train)
X_train_norm = scaler.transform(X_train)
X_test_norm = scaler.transform(X_test)
```


```python
clf = LogisticRegression()
# 2. `.fit` 메소드를 통해 모델을 학습시켜주세요
clf.fit(X_train_norm, y_train) #(-1)
# 3. `X_test`를 예측해서 `y_pred`에 할당해주세요
y_pred = clf.predict(X_test_norm)
```

```python
from sklearn.model_selection import cross_val_score
f1_scores = cross_val_score(estimator=clf, X = X_train_norm, y= y_train, cv=5, scoring="f1") # (-1)
```

```python
from sklearn.decomposition import PCA

X_scaled = scaler.transform(X) # 원본 X에 훈련 시 빌드된 평균/표준편차 스케일링 적용

pca = PCA(n_components=2)

X_pca = pca.fit_transform(X_scaled)

  

# 2. K-Means: X_pca를 2개 클래스로 군집화 (샘플 수: 178개)

from sklearn.cluster import KMeans

kmeans = KMeans(n_cluster=2,random_state=42)

y_cluster = kmeans.fit_predict(X_pca)
```

---
### Vector Store
문서를 임베딩하여 VectorDB에 저장
```python
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
embeddings = OpenAIEmbeddings(
model="text-embedding-3-small",
api_key=os.environ["GMS_KEY"],
base_url="https://gms.ssafy.io/gmsapi/api.openai.com/v1/"
)

vectorstore = Chroma.from_documents(documents=all_documents,embeddings=embeddings,collection_name="yes24_docs_page")
```

### Retriever
질문을 임베딩 벡터로 변환 → Vector Store에 저장된 문서 벡터들과 유사도 비교 → 가장 가까운 문서 k개 선택 → Document 객체로 반환
중요한 점은 Retriever가 **답변을 만드는 게 아니라, 관련 문서를 찾는 역할만 한다**는 거야

```python
retriever = vectorstore.as_retriever(search_kwargs={"k": 3})
```

### Chunking
Chunking은 **긴 문서를 작은 조각으로 나눠 Retriever가 질문과 관련된 부분을 더 정확하게 찾도록 하기 위해 사용**한다.  
또한 **토큰 제한과 비용을 줄이고, 검색 품질을 높이는 데 도움**이 된다.

```python
text_splitter = RecursiveCharacterTextSplitter(
	chunk_size=300,
	chuck_overlap=50,
	length_function=len
)

chunked_documents = text_splitter.split_documents(all_documents)
```

chunking된 문서로 새 Vector Store 생성
```python
vectorstore_chunked = Chroma.from_documents(
	documents=chunked_documents,
	embedding=embeddings,
	collection_name="yes24_docs_chunked"
)
retriever_chunked = vectorestore_chunked.as_retriever(search_kwargs={"k":3})
```
### Rag 파이프라인
START -> retrieve -> generate -> END

```python
# 1. State 타입 정의
# - question: 사용자 질문
# - context: 검색된 문서 내용
# - answer: 생성된 답변
class RAGState(TypedDict): # shared state
question: str
context: str
answer: str

# ========== 여기에 코드를 작성하세요 ==========
# 2. retrieve 노드: 질문으로 관련 문서 검색
# 힌트: retriever_chunked.invoke(question)을 사용하여 문서를 검색하고,
# 검색 결과를 문자열로 포맷팅하여 {"context": ...} 형태로 반환
def retrieve(state: RAGState) -> RAGState:
	"""Vector Store에서 관련 문서를 검색하는 노드"""
	# TODO: 구현하세요
	question = state["question"]
	# 청크된 데이터 조회
	docs = retriever_chunked.invoke(question)
	context = "\n\n".join([ doc.page_content for doc in docs ])
	state["context"] = context
	return {"context":context} # RAGState에 자동으로 업데이트 해줌!!

  
# 3. generate 노드: 검색된 문서로 답변 생성
# 힌트: state에서 question과 context를 가져와서
# ChatPromptTemplate으로 프롬프트를 생성하고
# llm.invoke(messages)로 답변을 생성한 후
# {"answer": response.content} 형태로 반환
def generate(state: RAGState) -> RAGState:
	"""검색된 문서를 기반으로 답변을 생성하는 노드"""
	# TODO: 구현하세요
	question = state["question"]
	context = state["context"]
	response = (prompt_template | llm).invoke({"context":context, "question": question})
	return {"answer":response.content}


# 4. StateGraph 구성
workflow = StateGraph(RAGState) # TODO: StateGraph(RAGState)를 생성하세요
# - 노드 추가: workflow.add_node("retrieve", retrieve)
workflow.add_node("retrieve", retrieve)
workflow.add_node("generate", generate)
# - 엣지 연결: workflow.add_edge(START, "retrieve")
workflow.add_edge(START, "retrieve")
workflow.add_edge("retrieve","generate")
workflow.add_edge("generate", END)
# - 그래프 컴파일: workflow.compile()
rag_graph = workflow.compile() # TODO: workflow.compile()을 실행하세요


# 그래프 실행
result = rag_graph.invoke({"question": question})
print(result["answer"])
```