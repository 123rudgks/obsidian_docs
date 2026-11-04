
데이터 모델링 3대 특징
- 추상화
	현실의 복잡한 정보를 그대로가 아닌 모델의 목적에 부합하는 **핵심 정보**만을 선별하여 표현하는 것
- 단순화
	복잡한 관계나 구조를 누구나 쉽게 이해할 수 있도록 표현 방식 단순하게 만드는 것 (ERD 사용)
- 명확화
	해석의 모호함이 없게 모델의 모든 구성 요소를 명확하게 구축해야 한다. ( ex : 컬럼 이름 명확히 설정)

데이터 모델링 3단계
- 개념적 데이터 모델링
	가장 추상적인 단계, 기술적 요소 배제하고 엔터티들과 이들의 큰 틀에서의 관계 정의
- 논리적 데이터 모델링
	개념 모델을 바탕으로 논리적 구조 상세 설계. Key, 속성, 관계 등 구체적 표현
	특정 데이터 베이스 기술에 종속되지 않고 재사용 가능하다.
- 물리적 데이터 모델링
	논리적 모델을 특정 DBMS에 실제로 구현하기 위한 최종 단계
	데이터 타입, 인덱스, 저장 방식 등 물리적 요소 모두 결정

데이터 베이스의 3계층 스키마 (ANSI-SPARC)
- 외부 스키마
	사용자,응용 프로그램 관점에서 필요한 데이터만 선별하여 보여주는 개인화 된 뷰(View)
- 개념 스키마
	설계자 관점에서 데이터 베이스 전체를 정의하는 통합된 뷰(View)
- 내부 스키마
	개발자 관점에서 데이터가 디스크에 실제로 어떻게 저장될 지를 정의하는 물리적인 뷰(View)

> 데이터 독립성을 확보하기 위함. 데이터 베이스의 논리적 구조나 물리적 저장 방식이 변경되어도 다른 계층에 미치는 영향을 최소화 하기 위함


데이터 모델링은 해당 업무 범위 내에서 의미있고 관리할 가치가 있는 데이터만을 다룬다.


엔터티 특징
- 업무에서 관리,사용되어야 하는 정보 집합
- 공유한 식별자(기본키)를 가져야 한다
- 2개 이상의 인스턴스를 포함해야 한다
- 반드시 하나 이상의 속성을 가져야 한다.
- 다른 엔터티와 최소 하나 이상의 관계를 맺어야 한다.

엔티티 분류 기준
1. 형태에 따른 분류
	1. 유형 엔터티
		물리적인 형태가 있음
	2. 개념 엔터티
		물리적인 형태 없음 (ex : 학과, 강의)
	3. 사건 엔터티
		업무 수행에 따라 발생하는 행위나 사건 (ex : 수강 신청, 성적)
2. 발생 시점에 따른 분류
	1. 기본 엔터티
		업무에 원래부터 존재하는 정보. 다른 엔터티에 의존하지 않고 독립적으로 생성되는 엔터티 (ex : 학생, 학과, 교수)
	2. 중심 엔터티
		기본 엔터티에서 파생되어 업무 처리의 중심 역할을 하는 엔터티 (ex : 학기, 강의)
	3. 행위 엔터티
		두 개 이상의 엔터티 사이에서 발생하는 '행위'에 의해 파생되는 엔터티. (ex : 수강신청, 성적, 출결)



속성
	의미상 더 이상 분리되지 않는 최소의 데이터 단위

속성의 분류
1. 특성에 따른 분류
	기본 속성 : 자연스럽게 정의 되는 속성 ( 고객명, 생년월일 등 )
	설계 속성 : 모델링 과정에서 인위적으로 생성하는 속성 ( 상품 코드, 고객 번호 등)
2. 구성 방식에 따른 분류
	PK
	FK
	일반 속성

식별자의 종류
1. 대표성 여부
	주 식별자
	보조 식별자
2. 스스로 생성 되었는지 여부
	내부 식별자
	외부 식별자 : 다른 엔터티와의 관계를 통해 외부에서 받아온 식별자 ( ex 주문 엔터티에서 받아온 주문 상세의 주문 번호 엔터티 )
3. 단일 속성 여부
	단일 식별자 : 하나의 속성으로만 구성된 식별자
	복합 식별자 : 두 개 이상의 속성을 합쳐야만 데이터를 유일하게 구분할 수 있는 식별자
4. 대체 여부
	본질 식별자
	인조 식별자 : 본질 식별자가 너무 길거나 복잡해 시스템이 식별하기 위해 인위적으로 만든 식별자 (ex : 학생 코드, 강의 코드)

식별자의 특징
- 유일성
- 최소성
- 불변성 (한번 정해지면 변하지 말아야 함)
- 존재성 ( NULL을 가질 수 없음 )

식별자 관계
	식별자 관계 : 부모 엔터티의 기본키(pk)를 자신의 기본키 일부로 포함하여 상속 받는 관계
	비식별자 관계 : 기본키를 상속 받되 외래키(fk)로만 사용하는 관계


정규화
: 데이터의 중복을 최소화하고, 데이터가 일관되고 정확한 상태를 유지하도록 테이블 구조를 체계적으로 정리하는 과정. 이상 현상 예방

정규화의 trade-off
정규화
	데이터 중복 제거 -> 데이터 무결성 높이기 -> 모델의 유연성 및 확장성 증가
	테이블이 여러개로 분리 -> Join 수 상승 -> 조회 성능 감소
반정규화
	의도적으로 중복을 허용 또는 테이블 통합 -> 조회 성능 증가


제1정규형
	테이블의 모든 속성값이 원자값이 되어야 한다. (한 칸에는 하나의 정보만 넣고 같은 종류의 정보를 위해 여러 열을 만들면 안된다. )
제2정규형
	부분 함수 종속이 없는 상태 ( 일반 속성이 기본키 전체가 아닌 일부분에만 종속되는 관계)
제3정규형
	이행 함수 종속이 없는 상태 (일반 속성 끼리 꼬리 물기 처럼 종속 관계를 가지는 것, 학번->학과코드 -> 학과명)


NULL의 연산
1. 개별 데이터 연산(산술 연산)
	산술 연산자(+, -, \*, / )는 한 줄의 데이터 안에서 개별적으로 계산한다. 이때 연산에 필요한 값 중 하나라도 NULL이면 전체 결과도 NULL
2. 여러 행 연산(집계 함수)
	NULL은 없는 데이터(결측치)로 취급하여 계산에서 제외한다.


DISTINCT
조회된 결과에서 중복된 행을 제거하고 고유한 값만 보고 싶을 때 사용하는 키워드
중복된 경우 중복된 값 하나만 남김

숫자 함수
ABS : 절댓값
SIGN : 부호 (양수면 1, 음수면 -1, 0이면 0)
ROUND(숫자, 반올림할 위치) : 반올림
TRUNC(숫자, 버림할 위치) : 버림
CEIL : 올림 (숫자보다 큰 가장 가까운 정수)
FLOOR : 내림 (숫자보다 작은 가장 가까운 정수)
MOD(나누어질 수, 나누는 수) : 첫 번째 숫자를 두 번째 숫자로 나눈 나머지 값

NULL 관련 함수
NVL(값, NULL일 경우 대체할 값) : NULL일 경우 대체할 값으로 대체
COALESCE(값1, 값2, 값3..) : 여러 개의 값을 순서대로 검사하여 가장 먼저 나오는 NULL이 아닌 값 반환
NVL2(검사할 값, NULL이 아닐 경우 값, NULL일 경우 값) : 검사할 값이 NULL인지에 따라 2가지 케이스로 값 반환
NULLIF(값1, 값2) : 입력한 두 개의 값이 서로 같으면 NULL, 같지 않으면 첫 번째 값 반환

CASE
단순 CASE
```sql
CASE [기준 컬럼]
	WHEN [비교값 1] THEN [결과값1]
	WHEN [비교값 2] THEN [결과값2]
	ELSE [위 조건에 해당하지 않을 경우 값]
```

검색 CASE
```sql
CASE
	WHEN [조건식1] THEN [결과값1]
	WHEN [조건식2] THEN [결과값2]
	ELSE [위 조건에 해당하지 않을 경우의 값]
END
```

ex :
```SQL
SELECT
	EMP_NAME,
	SALARY,
	CASE WHEN SALARY >= 5000 THEN '높음'
		WHEN SALARY >= 3000 THEN '중간'
		ELSE '낮음'
	END AS SAL_GRADE
FROM EMPLOYEES;	
```


LIKE
- % : 0개 이상의 모든 문자
- \_ : 정확히 1개의 모든 문자


집계 함수
COUNT
count vs distinct count
count : NULL이 아닌 모든 행의 수
distinct count : NULL이 아닌 고유한 값의 수

중복된 값이면 1개로 count? 아니면 아예 count no?

join의 종류
- EQUI JOIN
	조인 조건에서 등호(=)를 사용하여 두 테이블의 컬럼값이 정확히 일치하는 경우에만 데이터 연결하는 가장 기본적인 조인
- INNER JOIN
	조인 조건에 맞는 데이터만을 반환
- OUTER JOIN
	- LEFT OUTER JOIN
	- RIGHT OUTER JOIN
	- FULL OUTER JOIN
- NATURAL JOIN
	ON 없이 두 테이블의 이름과 유형이 모두 동일한 컬럼끼리 EQUI JOIN
- CROSS JOIN (카테시안 곱)
	한 쪽 테이블의 모든 행이 다른쪽 테이블의 모든 행과 한 번씩 연결되는 조인

셀프 조인
```SQL
SELECT 별칭1.컬럼, 별칭2.컬럼
FROM 테이블 별칭1, 테이블 별칭 2
WHERE 조인 조건;
```


UNION ALL / UNION
UNION : 두 SELECT문의 결과를 합친다. 중복된 결과는 하나로 합친다.
UNION ALL : UNION과 동일하되 중복된 행 제거 없이 그대로 모두 보여줌

INTERSECT
	양쪽에 공통으로 존재하는 행만 반환

MINUS / EXCEPT
	첫 번째 SELECT 결과에서 두 번째 SELECT문의 결과와 겹치는 행을 제외한 나머지(차집합)를 반환


그룹 함수 
> 실제 사용 예제 보면서 확인

ROLLUP
```SQL
SELECT A, B, 집계 함수
FROM 테이블
GROUP BY ROLLUP(A, B)
```
- A와 B 모두 기준으로 합계 계산
- A 하나만으로 합계 계산
- 기준 없이 전체 총합 계산

CUBE
ROLLUP에 B에 대한 요약까지 추가하여 모든 경우의 수 계산
```SQL
SELECT A, B, 집계 함수
FROM 테이블
GROUP BY CUBE(A, B)
```
- A와 B 모두 기준으로 합계 계산
- A 하나만으로 합계 계산
- B 하나만으로 합계 계산
- 기준 없이 전체 총합 계산

GROUPING SETS
ROLLUP이나 CUBE와 달리 사용자가 필요로 하는 그룹 조합만을 명시적으로 지정하여 요약 정보 얻을 때 사용
```SQL
SELECT 그룹컬럼1,그룹컬럼2,집계함수
FROM 테이블
GROUP BY GROUPING SETS(그룹1, 그룹2)
```

GROUPING
ROLLUP이나 CUBE를 사용하면 요약 정보를 나타내는 행의 그룹 컬럼에 NULL이 표시된다.
또 원본 데이터에도 NULL이 있었다면 이 둘을 구분하기 힘들다.
GROUPING은 이 문제를 해결하기 위한 도구로 요약된 행이면 1, 실제 데이터 행이면 0을 반환한다.
```SQL
SELECT 그룹컬럼1, 집계함수, GROUPING(그룹컬럼1) AS 별칭
FROM 테이블
GROUP BY ROLLUP(그룹컬럼1);
```


윈도우 함수
> 실제 사용 예시를 보면서 이해

GROUP BY는 전체 행을 묶어서 결과 행을 반환하는 것에 반해 윈도우 함수는 원본 행의 개수를 유지하면서 각 행에 별도의 컬럼을 추가하여 결과를 보여준다.
- `GROUP BY`: 여러 행을 하나의 집계 결과로 압축
- 윈도우 함수: 원본 행을 유지하면서 각 행에 계산 결과를 추가

OVER절
1. PARTITION BY
```SQL
SELECT
	EMP_NAME, DEPT_NAME,AVG(SALARY) OVER (PARTITION BY DEPT_NAME) AS DEPT_AVG_SALARY
FROM EMPLOYEES;
```

2. ORDER BY
```SQL
SELECT EMP_NAME, SALARY, RANK() OVER (ORDER BY SALARY DESC) AS SALARY_RANK
FROM EMPLOYEES;
```


순위 함수
1. RANK
	ORDER BY로 지정된 컬럼 값을 기준으로 각 행의 순위를 계산
	공동 순위가 있다면 그 다음 순위는 공동 순위의 수 만큼 건너뛴 수부터 시작된다.
```SQL
SELECT
	EMP_NAME, DEPT_NAME, SALARY, 
	RANK() OVER (PARTITION BY DEPT_NAME ORDER BY SALARY DESC) AS DEPT_RANK
FROM EMPLOYEES;
```
2. DENSE_RANK
	RANK이지만 공동 순위의 수 만큼 건너 뛰지 않고 공동 순위 다음 수부터 시작한다.
```SQL
SELECT
	EMP_NAME, DEPT_NAME, SALARY
	DENSE_RANK() OVER (PARTITION BY DEPT_NAME ORDER BY SALARY DESC) AS DEPT_DENSE_RANK
FROM EMPLOYEES;
```
3. ROW_NUMBER
	ORDER BY로 정렬된 순서에 따라 각 행에 순차적인 순번을 부여한다.
	동일한 값이 있어도 건너뛰지 않고 순차적인 순번을 이어간다.
```SQL
SELECT
	EMP_NAME, DEPT_NAME, SALARY
	ROW_NUMBER() OVER (PARTITION BY DEPT_NAME ORDER BY SALARY DESC) AS DEPT_ROW_NUM
FROM EMPLOYEES;
```
4. PERCENT_RANK
	그룹 내에서 현재 행의 백분위 순위를 0(가장 낮은 순위), 1(가장 높은 순위) 사이의 값으로 반환한다.
```SQL
SELECT
	EMP_NAME, DEPT_NAME, SALARY,
	PERCENT_RANK() OVER (PARTITION BY DEPT_NAME ORDER BY SALARY ASC) AS PERCENTILE_RANK
FROM EMPLOYEES;
```


집계 함수 with OVER절
1. SUM
	ORDER BY로 정의된 순서에 따른 누적 합계 계산
```SQL
SELECT
	EMP_NAME, DEPT_NAME, HIRE_DATE, SALARY,
	SUM(SALARAY) OVER (PARTITION BY DEPT_NAME ORDER BY HIRE_DATE) AS RUNNING_TOTAL
FROM EMPLOYEES;
```
2. MAX, MIN
	PARTITION 내의 MAX,MIN 값 표시
```SQL
SELECT
	EMP_NAME, DEPT_NAME, SALARY,
	MAX(SALARY) OVER (PARTITION BY DEPT_NAME) AS DEPT_MAX_SALARY,
	MIN(SALARY) OVER (PARTITION BY DEPT_NAME) AS DEMPT_MIN_SALARY
FROM EMPLOYEES;
```
3. AVG
	PARTITION 냬의 AVG값 표시
```SQL
SELECT
	EMP_NAME, DEPT_NAME, SALARY,
	TRUNC(AVG(SALARY) OVER (PARTITION BY DEPT_NAME)) AS DEPT_AVG_SALARY
FROM EMPLOYEES;
```
3. COUNT
	PARTITION의 행의 갯수
```SQL
SELECT
	EMP_NAME, DEPT_NAME,
	COUNT(*) OVER (PARTITION BY DEPT_NAME) AS DEPT_EMP_COUNT
FROM EMPLOYEES;
```

KEEP 문법
	선택된 행들만 집계 대상으로 사용
```SQL
집계함수(컬럼A)
KEEP (DENSE_RANK FIRST | LAST ORDER BY 컬럼B ASC | DESC)
```


행 순서 함수
	ORDER BY로 정렬된 그룹 내에서 현재 행을 기준으로 특정 행의 값을 직접 참조 할수 있게 하는 윈도우 함수
	행과 행 간의 관계 분석시 유용
1. FIRST_VALUE
	ORDER_BY 순서에서 가장 첫 번째에 위치한 행의 값 반환
```SQL
SELECT
	EMP_NAME,DEPT_NAME,HIRE_DATE,
	FIRST_VALUE(EMP_NAME) OVER (PARTITION BY DEPT_NAME ORDER BY HIRE_DATE ASC) AS FIRST_HIRED
FROM EMPLOYEES;
```
2. LAST_VALUE
	ORDER BY 순서에서 가장 마지막에 위치한 행의 값 반환
```SQL
SELECT
	EMP_NAME,DEPT_NAME,HIRE_DATE,
	LAST_VALUE(EMP_NAME) OVER (
		PARTITION BY DEPT_NAME
		ORDER BY HIRE_DATE
		ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
		) AS LAST_HIRED
FROM EMPLOYEES;
```
> [!note]
> 윈도우 함수는 계산 범위를 기본적으로 그룹의 처음부터 **현재 행**까지로 제한한다. 따라서 그냥 LAST_VALUE를 하면 그룹 마지막이 아닌 현재 행이 나온다.
> 따라서 ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING 이라는 구문 추가해야함.


계층형 질의
START WITH, CONNECT BY
```SQL
SELECT LEVEL 컬럼목록
FROM 테이블
START WITH 시작조건
CONNECT BY NOCYCLE PRIOR 부모컬럼=자식컬럼
ORDER SIBILINGS BY EMP_NAME ASC;
```
- START WITH : 계층 탐색을 시작할 행 지정
- CONNECT BY : 부모 행과 자식 행 사이의 관계 정의. PRIOR 연산자는 연결 조건에서 부모 행의 컬럼을 가리킨다.
- LEVEL : (옵셔널) 가상 컬럼이 추가되어, 각 직원이 계층 구조의 몇 번째 깊이에 위치하는지 숫자로 보여줌
- ORDER SIBLINGS BY : (옵셔널) 계층 구조는 유지하며 같은 레벨의 행끼리만 정렬
	그냥 ORDER BY 쓰면 START WITH..CONNECT BY 구조가 무너지고 ORDER BY가 덮어씀
- NOCYCLE : 순환 관계가 발생할 때, 이미 지나온 행 방향으로 더 이상 탐색하지 않도록 막아줌

CONNECT_BY_ROOT
	현재 행이 속한 계층의 최상위 루트 행의 값을 반환
```SQL
SELECT
	EMP_NAME,
	CONNECT_BY_ROOT EMP_NAME AS TOP_MANAGER
FROM EMPLOYEES
START WITH MNAGER_ID IS NULL
CONNECT BY PRIOR EMP_ID = MANAGER_ID;
```
SYS_CONNECT_BY_PATH
	루트 행부터 현재 행까지의 경로를 지정된 구분자를 포함한 문자열로 반환
```SQL
SELECT
	EMP_NAME,
	SYS_CONNECT_BY_PATH(EMP_NAME,'>') AS PATH
FROM EMPLOYEES
START WITH MANAGER_ID IS NULL
CONNECT BY PRIOR EMP_ID = MANAGER_ID;
```


정규 표현식
기본 문법

| 메타 문자  | 설명                       | 예시                                |
| ------ | ------------------------ | --------------------------------- |
| ^      | 문자열 시작                   | ^A : 'A'로 시작하는 문자열                |
| $      | 문자열 끝                    | A$ : 'A'로 끝나는 문자열                 |
| .      | 임의의 한 글자                 | A.B : 'A'와 'B' 사이에 한 글자가 있는 문자열   |
| \*     | 바로 앞 문자가 0번 이상 나타남       | A\*B : 'B'앞에 'A'가 없거나 여러 개 있는 문자열 |
| +      | 바로 앞 문자가 1번 이상 나타남       | A+B : 'B' 앞에 A가 최소 1개 이상 있는 문자열   |
| ?      | 바로 앞 문자가 0번 또는 1번 나타남    | A?B : 'B' 앞에 'A'가 없거나 1개 있는 문자열   |
| [ ]    | 괄호 안 문자 중 하나 의미          | \[ABC] : 'A', 'B', 'C' 중 한 글자     |
| \|     | 여러 단어나 패턴 중 하나           | A\|B : 'A' 또는 'B'                 |
| {m, n} | 바로 앞 문자가 m번 이상 n번 이하 나타남 | A{2,3} : 'A'가 2번 또는 3번 반복되는 문자열   |

정규 표현식 함수
1. REGEXP_LIKE
	주어진 문자열이 특정 정규표현식 패턴을 포함하는지 여부 검사하여 True,False 반환
```SQL
SELECT EMP_NAME, PHONE_NUMBER
FROM EMP_DATA
WHERE REGEXP_LIKE(PHONE_NUMBER, '^[0-9]{3}-[0-9]{4}-[0-9]{4}$');
```
2. REGEXP_SUBSTR
	문자열에서 정규표현식 패턴과 일치하는 부분을 찾아 추출하는 함수
```SQL
SELECT EMP_NAME, EMAIL, REGEXP_SUBSTR(EMAIL,'@[A-Za-z0-9.-]+') AS DOMAIN
FROM EMP_DATA;
```
3. REGEXP_REPLACE
	문자열에서 정규표현식 패턴과 일치하는 부분을 찾아 다른 문자열로 치환하거나 삭제하는 함수
```SQL
SELECT EMP_NAME, PHONE_NUMBER, REGEXP_REPLACE(PHONE_NUMBER, '-[0-9]{4}-','-****-') AS MASKED_PHONE
FROM EMP_DATA
WHERE REGEXP_LIKE(PHONE_NUMBER,'^[0-9]{3}-[0-9]{4}-[0-9]{4}$')
```
4. REGEXP_COUNT
	문자열 안에서 특정 정규표현식 패턴이 몇 번 나타나는지 그 횟수를 숫자로 반환
```SQL
SELECT EMP_NAME, PHONE_NUMBER, REGEXP_COUNT(PHONE_NUMBER,'-') AS DASH_COUNT
FROM EMP_DATA
```
5. REGEXP_INSTR
	특정 정규표현식 패턴이 시작되는 위치를 숫자로 반환
```SQL
SELECT EMP_NAME,EMAL,REGEXP_INSTR(EMAIL,'@') AS DOMAIN_START_POS
FROM EMP_DATA;
```


DML
1. INSERT
```SQL
INSERT INTO EMPLOYEES(EMP_ID, EMP_NAME, DEPT_NAME,SALARY)
VALUES(103,'임한울', 'Sales', 7200)
```
2. UPDATE
```SQL
UPDATE EMPLOYEES
SET DEPT_NAME = 'Marketing'
WHERE EMP_ID = 101;
```
3. DELETE
```SQL
DELETE FROM EMPLOYEES
WHERE EMP_ID = 102;
```
4. MERGE
	두 테이블 데이터 비교하여 새로운 데이터면 추가, 존재하면 수정을 하는 작업
```SQL
MERGE INTO EMPLOYEES e
USING EMP_UPDATES u
ON (e.EMP_ID = u.EMP_ID)
WHEN MATCHED THEN
	UPDATE SET e.DEPT_NAME = u.DEPT_NAME
WHEN NOT MATCHED THEN
	INSERT(e.EMP_ID, e.EMP_NAME, e.DEPT_NAME)
	VALUES(u.EMP_ID, u.EMP_NAME,u.DEPT_NAME);
```

TCL
COMMIT
ROLLBACK
SAVEPOINT

DDL
1. CREATE
```SQL
CREATE TABLE 테이블명 (
	컬럼명1 데이터 유형 [CONSTRAINT 제약 조건이름 제약조건],
	컬럼명2 데이터 유형 [CONSTRAINT 제약 조건이름 제약조건],
)
```
	데이터 유형
	- VARCHAR2 : 가변길이 문자열
	- CHAR : 고정길이 문자열
	- CLOB : 대용량텍스트
	- NUMBER : 숫자 (정수 및 소수)
	- DATE : 날짜와 시간
	제약조건
	- PRIMARY KEY
	- FOREIGN KEY
	- NOT NULL
	- UNIQUE : 해당 컬럼에 중복된 값이 들어오는 걸 막음. NULL 은 허용
	- CHECK : 입력될 수 있는 값의 범위, 조건을 직접 지정
		CONSTRAINT CHK_SALARY CHECK (SALARY > 0 )
2. ALTER
	1. ADD COLUMN
		ALTER TABLE 테이블명 ADD 컬럼명 데이터 유형;
	2. DROP COLUMN
		ALTER TABLE 테이블명 DROP COLUMN 컬럼명;
	3. MODIFY COLUMN
		ALTER TABLE 테이블명 MODIFY (컬럼명1 데이터 유형 \[DEFAULT값] \[NOT NULL]);
	4. RENAME COLUMN
		ALTER TABLE 테이블명 RENAME COLUMN 기존 컬럼명 TO 새 컬럼명;
	5. ADD CONSTRAINT
		ALTER TABLE 테이블명 ADD CONSTRAINT 제약조건명 제약조건 (컬럼명);
3. RENAME TABLE
	RENAME 테이블명 TO 새테이블명;
4. DROP TABLE
	DROP TABLE 테이블명 \[CASCADE CONSTRAINTS];
5. TRUNCATE TABLE
	TRUNCATE TABLE 테이블명;

DCL
USER 관련 명령어
1. CREATE USER
	CREATE USER 사용자명 IDENTIFIED BY 비밀번호
2. ALTER USER
	ALTER USER 사용자명 IDENTIFIED BY 비밀번호
3. DROP USER
	DROP USER 사용자명

권한 관련 명령어
1. GRANT
	GRANT 권한 TO 사용자;
2. REVOKE
	REVOKE 권한 FROM 사용자;

ROLE 관련 명령어
1. 역할 생성
	CREATE ROLE 역할 이름;



FROM절에 여러 테이블을 쉼표(,)로 나열하고 WHERE 절에 조인 생략하면 카테시안 곱(크로스 조인)이 발생함


NULL이 포함된 산술 연산가 집계 함수의 처리 순서
집계 함수는 NULL을 무시한다.
SUM(Q1 + Q2 + Q3) : 먼저 연산을 수행하고 각 행별 NULL 제외하고 SUM 처리

단순 산술 연산에 NULL 섞이면 결과 NULL
10 + NULL : NULL
10 > NULL : NULL

집계 함수는 NULL 무시하고 연산
SUM(10, NULL, 20) : 30


해시 조인


SQL의 논리적 실행 순서
FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY
> note. WHERE -> HAVING 순서


NOT IN 과 NULL 관계
```SQL
WHERE prod_id NOT IN (1,2,3)
```
위 연산은 논리적으로 아래와 같다.
```SQL
WHERE prod_id <> 1 AND prod_id <> 2 AND prod_id <> 3
```

따라서 NULL이 섞이면 연산 값이 `UNKNOWN`이 된다.

다중 컬럼 NOT IN에선 다른 케이스도 있는데, NULL 이 아닌 컬럼값이 이미 다른게 확실하면 NULL과 무관하게 이미 NOT IN이 확정 되므로 TRUE 판정 가능
```SQL
(1, 사과) NOT IN (NULL, 사과) // 이건 UNKOWN
(1, 사과) NOT IN (NULL, 바나나) // 이건 TRUE
```

