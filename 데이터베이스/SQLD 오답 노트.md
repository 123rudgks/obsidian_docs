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
  논리적 모델을 특정 DBMS에 실제로 구현하기 위한 최종 단계$
  데이터 타입, 인덱스, 저장 방식 등 물리적 요소 모두 결정

데이터 베이스의 3계층 스키마 (ANSI-SPARC)

- 스키마 : 데이터베이스의 구조, 데이터 간 관계, 제약조건 등을 정의한 것
- 외부 스키마
  개별 사용자나 응용 프로그램 관점에서 필요한 데이터만 정의한 부분적인 논리 구조(사용자 뷰)
  사용자마다 필요한 데이터가 다르므로 하나의 데이터베이스에 여러 외부 스키마가 존재할 수 있다.
  예 : 학생은 자신의 성적을 보고, 교수는 담당 강의의 수강생 성적을 본다.
- 개념 스키마
  모든 사용자 관점을 통합한 **조직 전체 관점의 데이터베이스 논리 구조**
  전체 데이터의 구조, 관계, 제약조건 등을 정의하며, 하나의 데이터베이스에 하나의 개념 스키마가 존재한다.
  예 : 학생, 교수, 강의, 수강, 성적 데이터와 이들 사이의 관계를 전체적으로 정의한다.
- 내부 스키마
  데이터가 저장 장치에 **실제로 어떻게 저장되는지** 정의한 물리적 구조
  파일 구성, 레코드 저장 형식, 인덱스, 접근 경로 등을 정의한다.
  예 : 성적 데이터를 어떤 파일 구조로 저장하고 어떤 인덱스로 접근할지 정의한다.

> [!tip] 문제에서 구분하는 기준
> **외부 = 개별 사용자에게 보이는 부분 / 개념 = 조직 전체의 논리 구조 / 내부 = 실제 물리적 저장 구조**
> “모든 사용자 관점을 통합한 조직 전체 관점의 통합적 표현” → **개념 스키마(②)**
> 직무 이름보다는 **개별 사용자 / 전체 통합 / 물리적 저장**이라는 설명을 기준으로 구분한다.

> 데이터 독립성을 확보하기 위함. 데이터 베이스의 논리적 구조나 물리적 저장 방식이 변경되어도 다른 계층에 미치는 영향을 최소화 하기 위함

데이터 모델링은 해당 업무 범위 내에서 의미있고 관리할 가치가 있는 데이터만을 다룬다.

엔터티 특징

- 업무에서 관리,사용되어야 하는 정보 집합
- 공유한 식별자(기본키)를 가져야 한다
- 2개 이상의 인스턴스를 포함해야 한다
- 반드시 하나 이상의 속성을 가져야 한다.
- 다른 엔터티와 최소 하나 이상의 관계를 맺어야 한다.

엔터티 분류 기준

1. 유무형(형태)에 따른 분류 : **어떤 성격의 대상인가?**
   1. 유형 엔터티
      물리적인 형태가 있는 대상 (ex : 사원, 상품, 건물)
   2. 개념 엔터티
      물리적인 형태가 없는 개념이나 관리 정보 (ex : 조직, 보험상품)
   3. 사건 엔터티
      업무 수행에 따라 발생하는 사건을 관리하는 대상 (ex : 주문, 청구, 미납)
2. 발생 시점(생성 시점)에 따른 분류 : **업무에서 어떤 순서와 의존 관계로 생기는가?**
   1. 기본 엔터티
      업무에 원래부터 존재하는 정보. 다른 엔터티에 의존하지 않고 독립적으로 생성되는 엔터티 (ex : 고객, 상품, 부서)
   2. 중심 엔터티
      기본 엔터티에서 파생되어 업무 처리의 중심 역할을 하는 엔터티 (ex : 주문, 계약)
   3. 행위 엔터티
      두 개 이상의 엔터티 사이에서 발생하는 업무 행위로 생성되는 엔터티. 상세 내역이나 이력 등을 관리한다. (ex : 주문변경이력, 사원부서이력)

> [!tip] 분류 기준부터 확인하기
> **형태 = 유형·개념·사건 / 발생 시점 = 기본·중심·행위**
> “생성 시점에 따른 분류가 아닌 것”을 묻는 이번 문제의 정답은 **② 사건 엔터티**이다. 사건 엔터티는 형태에 따른 분류에 속한다.
> 사건과 행위는 뜻이 비슷해도 분류 기준이 다르다. **사건은 대상의 성격**, **행위는 생성 과정과 의존 관계**를 보고 구분한다.
> 두 분류 기준은 함께 적용할 수 있다. 예를 들어 주문은 형태 기준으로는 **사건 엔터티**, 발생 시점 기준으로는 **중심 엔터티**로 분류할 수 있다.

속성
의미상 더 이상 분리되지 않는 최소의 데이터 단위

속성의 분류

1. 특성(생성 원인)에 따른 분류
   기본 속성 : 업무에서 본래 존재하며 직접 얻는 속성 (ex : 고객명, 생년월일, 주문수량, 단가)
   설계 속성 : 업무를 시스템으로 구현하기 위해 설계 과정에서 인위적으로 정의한 속성 (ex : 고객번호, 상품코드)
   파생 속성 : 다른 속성의 값을 계산하거나 가공하여 얻는 속성 (ex : 주문수량 × 단가로 계산한 주문금액, 생년월일로 계산한 나이)
   조회 성능 향상을 위해 계산 결과를 미리 저장하기도 한다. 저장한 경우 원본 값이 변경되면 파생 값도 함께 갱신하여 일관성을 유지해야 한다.
2. 엔터티 구성 방식에 따른 분류
   PK 속성 : 각 인스턴스를 유일하게 식별하는 기본키를 구성하는 속성
   FK 속성 : 다른 엔터티를 참조하여 관계를 연결하는 외래키를 구성하는 속성
   일반 속성 : PK나 FK에 해당하지 않는 나머지 속성

> [!tip] 기본·설계·파생 구분하기
> **기본 = 업무에서 직접 얻음 / 설계 = 시스템에 필요해서 만듦 / 파생 = 다른 값으로 계산·가공함**
> 이번 문제의 “다른 속성의 값을 이용해 미리 계산한 값을 저장” → **③ 파생 속성**
> 성능을 위해 의도적으로 만들었더라도, **다른 속성에서 계산해 얻은 값**이라는 점이 파생 속성을 구분하는 핵심이다.
> 보기의 **복합 속성**은 주소를 시·구·도로명 등으로 나누는 것처럼 여러 하위 속성으로 분해할 수 있는 속성이다. 기본·설계·파생과는 분류 기준이 다르다.

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

ERD 관계선 기호 (까마귀발 표기법)

- 원(○) : 최소 참여 수가 0 → 연결된 데이터가 없어도 되는 선택 참여
- 세로선(│) : 1을 의미한다. 최소 참여 위치에서는 최소 1(필수), 최대 개수 위치에서는 최대 1을 뜻한다.
- 까마귀발(세 갈래) : 최대 개수가 여러 개(N)임을 뜻한다.

| 관계선 끝의 기호 조합 | 연결 가능한 개수 | 의미             |
| --------------------- | ---------------- | ---------------- |
| 원 + 세로선           | 0..1             | 없거나 하나      |
| 세로선 + 세로선       | 1..1             | 반드시 하나      |
| 원 + 까마귀발         | 0..N             | 없거나 여러 개   |
| 세로선 + 까마귀발     | 1..N             | 반드시 하나 이상 |

> [!note] 관계선을 읽는 방향
> **A 하나에 B가 몇 개 연결되는지 알려면 B 쪽 끝의 기호를 본다.**
> 반대로 B 하나에 A가 몇 개 연결되는지는 A 쪽 끝의 기호를 본다.
> 표기법에 따라 ‘반드시 하나’를 세로선 하나로 간략히 표시하기도 한다. 이번 문제의 대학·교수 쪽 세로선은 각각 ‘반드시 하나’를 뜻한다.

이번 문제의 관계 해석

- 대학 ↔ 교수
  - 교수 쪽 끝의 원 + 까마귀발 : 대학 하나에 교수는 **0명 이상** → 교수가 없는 대학도 가능 (①, ② 옳음)
  - 대학 쪽 끝의 세로선 : 교수 한 명은 **반드시 대학 하나**에 소속됨
- 교수 ↔ 강의
  - 강의 쪽 끝의 원 + 까마귀발 : 교수 한 명이 담당하는 강의는 **0개 이상** → 담당 강의가 없는 교수도 가능
  - 교수 쪽 끝의 세로선 : 강의 하나는 **반드시 교수 한 명**이 담당함 → 담당 교수 없는 강의는 불가능 (③ 옳음, **④ 틀림**)

> [!tip] 선택 참여의 방향 주의
> 강의 쪽에 원이 있다고 해서 ‘강의에 교수가 없어도 된다’는 뜻은 아니다.
> **교수 한 명을 기준으로 연결된 강의가 없어도 된다**는 뜻이다. 강의에 교수가 필요한지는 **교수 쪽 끝의 기호**로 판단한다.

식별자 관계
식별자 관계 : 부모 엔터티의 기본키(PK)를 자식의 외래키(FK)로 받아, 자식 기본키의 전부 또는 일부로도 사용하는 관계
비식별자 관계 : 부모 엔터티의 기본키를 자식의 외래키로 받되, 자식 기본키에는 포함하지 않는 관계
까마귀발 표기법에서는 보통 식별 관계를 실선, 비식별 관계를 점선으로 표시한다.
실선·점선은 식별 관계 여부를, 원·세로선·까마귀발은 참여 여부와 개수를 나타내므로 각각 구분해서 읽는다.

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

NULL의 의미와 확인 방법

- NULL은 값이 없거나 아직 알 수 없는 상태를 나타낸다. 숫자 0이나 공백 문자와 다르다.
- NULL인지 확인 : `WHERE 컬럼 IS NULL`
- NULL이 아닌지 확인 : `WHERE 컬럼 IS NOT NULL`
- `컬럼 = NULL`, `컬럼 <> NULL`은 NULL 여부를 검사하지 못한다. 결과가 **UNKNOWN**이 되며, WHERE는 TRUE인 행만 반환하므로 조회 대상에서 제외된다. `NULL = NULL`도 TRUE가 아니다.

```sql
SELECT * FROM TBL1 WHERE C1 IS NULL;     -- NULL인 행
SELECT * FROM TBL1 WHERE C1 IS NOT NULL; -- NULL이 아닌 행
```

ORDER BY에서 NULL의 기본 정렬 위치
정렬 방향을 생략하면 **ASC(오름차순)**이다. NULL의 기본 위치는 **DBMS마다 다르다.**

| DBMS       | 정렬 시 NULL 취급     | ASC 예시                     | DESC 예시                    |
| ---------- | --------------------- | ---------------------------- | ---------------------------- |
| Oracle     | 가장 큰 값처럼 취급   | `10, 20, NULL` (NULL 마지막) | `NULL, 20, 10` (NULL 처음)   |
| SQL Server | 가장 작은 값처럼 취급 | `NULL, 10, 20` (NULL 처음)   | `20, 10, NULL` (NULL 마지막) |

‘가장 큰 값/작은 값’은 **정렬 위치를 기억하기 위한 표현**이다. NULL을 일반 비교 연산자로 크기 비교할 수 있다는 뜻은 아니다.
Oracle에서는 `ORDER BY C1 ASC NULLS FIRST`, `ORDER BY C1 DESC NULLS LAST`처럼 NULL 위치를 직접 지정할 수도 있다.

> [!tip] 이번 문제의 핵심
> **NULL 확인 = IS NULL / IS NOT NULL**, **정렬 = Oracle은 큰 값처럼, SQL Server는 작은 값처럼 취급**
> 정답은 **③**이다. SUM, AVG 등의 집계 함수는 NULL을 제외한다. COUNT의 처리 차이는 아래 집계 함수 항목 참고.

참고 : [Oracle NULL](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Nulls.html), [Oracle ORDER BY](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/SELECT.html), [SQL Server ORDER BY](https://learn.microsoft.com/en-us/sql/t-sql/queries/select-order-by-clause-transact-sql)

NULL의 연산

1. 개별 데이터 연산(산술 연산)
   산술 연산자(+, -, \*, / )는 한 줄의 데이터 안에서 개별적으로 계산한다. 이때 연산에 필요한 값 중 하나라도 NULL이면 전체 결과도 NULL
2. 여러 행 연산(집계 함수)
   SUM, AVG, MAX, MIN, COUNT(컬럼)은 NULL을 계산에서 제외한다. COUNT(\*)와 COUNT(1)은 NULL 포함 여부와 관계없이 전체 행 수를 센다.

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

날짜 함수 (`SYSDATE`, 날짜의 `TRUNC` / `ROUND`, Oracle 기준)

- `SYSDATE` : DB 서버의 현재 날짜와 시간을 DATE 값으로 반환한다. 괄호 없이 사용한다.
- TRUNC와 ROUND는 **숫자뿐 아니라 날짜에도 사용**한다. 날짜를 받으면 두 번째 인자로 `'YEAR'`, `'MM'` 같은 **처리 단위**를 지정한다. 결과도 날짜(DATE)이다.
- `TRUNC(날짜, 단위)` : 지정한 단위보다 작은 부분을 버려 **그 단위의 시작 시점**으로 만든다.
- `ROUND(날짜, 단위)` : 해당 단위의 반올림 기준에 따라 **현재 단위 또는 다음 단위의 시작 시점**으로 만든다.

YEAR(연도) 단위의 처리

- `TRUNC(SYSDATE, 'YEAR')` : 날짜가 그해 언제이든 **올해 1월 1일 00:00:00**이 된다.
- `ROUND(SYSDATE, 'YEAR')` : **7월 1일을 경계**로 반올림한다.
  - 1월 1일~6월 30일 → 올해 1월 1일
  - 7월 1일~12월 31일 → 다음 해 1월 1일

| 입력 날짜  | TRUNC(날짜, 'YEAR') | ROUND(날짜, 'YEAR') |
| ---------- | ------------------- | ------------------- |
| 2025-01-01 | 2025-01-01          | 2025-01-01          |
| 2025-06-30 | 2025-01-01          | 2025-01-01          |
| 2025-07-01 | 2025-01-01          | 2026-01-01          |
| 2025-12-31 | 2025-01-01          | 2026-01-01          |

이번 문제는 **2025년 1월 1일에 실행**한다고 가정한다.

```sql
SELECT
    TRUNC(SYSDATE, 'YEAR') AS T_DATE,
    ROUND(SYSDATE, 'YEAR') AS R_DATE
FROM DUAL;
```

TRUNC는 그해 시작인 2025-01-01을 반환한다. ROUND도 7월 1일 전이므로 2025-01-01을 반환한다. 따라서 **③ 두 결과 모두 2025-01-01**이다.

> [!tip] 입력 자료형과 두 번째 인자를 함께 보기
> `ROUND(123.45, 1)`은 숫자의 소수 첫째 자리 기준 반올림이고, `ROUND(SYSDATE, 'YEAR')`는 날짜의 연도 단위 반올림이다.
> **날짜 TRUNC = 해당 단위의 시작 / 날짜 ROUND = 반올림 경계를 보고 해당 단위 또는 다음 단위의 시작**
> 예를 들어 `TRUNC(SYSDATE, 'MM')`은 이번 달 1일 00:00:00이다. 날짜에서 단위를 생략한 `TRUNC(SYSDATE)`는 일 단위가 기본이므로 오늘 00:00:00이다.

참고 : [Oracle 날짜 ROUND·TRUNC의 단위와 경계](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ROUND-and-TRUNC-Date-Functions.html), [Oracle TRUNC(date)](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/TRUNC-date.html), [Oracle SYSDATE](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/SYSDATE.html)

문자열 함수 (`SUBSTR` / `INSTR`, Oracle 기준)

- `SUBSTR(문자열, 시작 위치, 추출 길이)` : 시작 위치부터 지정한 **글자 수**만큼 추출한다. 마지막 인자는 끝 위치가 아니다.
  - 문자 위치는 **1부터** 센다. 밑줄이나 공백도 한 글자로 센다.
  - 양수 위치 : 앞에서부터 센다.
  - 음수 위치 : 마지막 글자를 -1로 하여 뒤에서부터 시작 위치를 찾는다. **추출은 그 위치에서 오른쪽으로** 진행한다.
  - Oracle에서는 시작 위치 0을 지정해도 1로 취급한다.
  - 추출 길이를 생략하면 시작 위치부터 끝까지 반환한다.
- `INSTR(문자열, 찾을 문자열)` : 처음 일치하는 문자열의 시작 위치를 **1부터 센 숫자**로 반환한다. 찾지 못하면 0을 반환한다.

이번 문제의 문자열 위치

```text
문자:   S   Q   L   _   P   R   O   G   R   A   M
양수:   1   2   3   4   5   6   7   8   9  10  11
음수: -11 -10  -9  -8  -7  -6  -5  -4  -3  -2  -1
```

| 보기 | 식                                                    | 결과                          |
| ---- | ----------------------------------------------------- | ----------------------------- |
| ①    | `SUBSTR('SQL_PROGRAM', 5, 2)`                         | `PR`                          |
| ②    | `SUBSTR('SQL_PROGRAM', -7, 2)`                        | `PR`                          |
| ③    | `SUBSTR('SQL_PROGRAM', INSTR('SQL_PROGRAM', 'P'), 2)` | `INSTR`가 5를 반환하므로 `PR` |
| ④    | `SUBSTR('SQL_PROGRAM', 6, 2)`                         | `RO`                          |

> [!tip] SUBSTR 위치 세기
> **첫 글자는 1 / 마지막 글자는 -1 / 세 번째 인자는 추출할 글자 수**
> 음수는 시작 위치를 찾는 기준이다. 문자열을 거꾸로 추출하는 뜻이 아니다.
> 이번 문제에서 결과가 다른 것은 **④ (`RO`)**이다.

참고 : [Oracle SUBSTR](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/SUBSTR.html), [Oracle INSTR](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/INSTR.html)

NULL 관련 함수
NVL(값, NULL일 경우 대체할 값) : NULL일 경우 대체할 값으로 대체
COALESCE(값1, 값2, 값3..) : 여러 개의 값을 순서대로 검사하여 가장 먼저 나오는 NULL이 아닌 값 반환
NVL2(검사할 값, NULL이 아닐 경우 값, NULL일 경우 값) : 검사할 값이 NULL인지에 따라 2가지 케이스로 값 반환
NULLIF(값1, 값2) : 입력한 두 개의 값이 서로 같으면 NULL, 같지 않으면 첫 번째 값 반환

CASE
`CASE WHEN`은 **조건에 따라 반환할 값을 정하는 표현식**이다. 프로그래밍의 if / else처럼 읽는다.

- WHEN : 검사할 조건
- THEN : 조건이 TRUE일 때 반환할 값
- ELSE : 어느 WHEN 조건도 TRUE가 아닐 때 반환할 값. **생략하면 NULL**이다.
- END : CASE 표현식의 끝. 여러 WHEN이 있으면 위에서부터 검사하여 처음 TRUE인 조건의 값을 반환한다.

단순 CASE

```sql
CASE [기준 컬럼]
	WHEN [비교값 1] THEN [결과값1]
	WHEN [비교값 2] THEN [결과값2]
	ELSE [위 조건에 해당하지 않을 경우 값]
END
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

CASE를 이용한 조건부 집계 : 특정 등급의 개수 세기

```sql
CASE WHEN GRADE = 'A' THEN 1 ELSE 0 END
```

‘이 행의 등급이 A이면 1, 그렇지 않으면 0’이라는 뜻이다. **CASE가 행마다 값을 만든 다음, SUM이나 COUNT가 그 값을 집계한다.**

| GRADE | `CASE WHEN GRADE = 'A' THEN 1 ELSE 0 END` | `CASE WHEN GRADE = 'A' THEN 1 END` |
| ----- | ----------------------------------------- | ---------------------------------- |
| A     | 1                                         | 1                                  |
| B     | 0                                         | NULL                               |
| C     | 0                                         | NULL                               |

| 집계식                                    | 조건을 만족하지 않는 행                              | 무엇을 세는가?                    |
| ----------------------------------------- | ---------------------------------------------------- | --------------------------------- |
| `SUM(CASE WHEN 조건 THEN 1 ELSE 0 END)`   | 0을 더함                                             | 조건이 TRUE인 행 수               |
| `NVL(SUM(CASE WHEN 조건 THEN 1 END), 0)`  | NULL은 합계에서 제외. 모두 NULL이면 NVL이 0으로 바꿈 | 조건이 TRUE인 행 수               |
| `COUNT(CASE WHEN 조건 THEN 1 END)`        | NULL이므로 세지 않음                                 | 조건이 TRUE인 행 수               |
| `COUNT(CASE WHEN 조건 THEN 1 ELSE 0 END)` | **0도 NULL이 아니므로 셈**                           | 조건과 관계없이 그룹의 모든 행 수 |

이번 문제의 ①~③은 상품별 A·B 등급 개수를 구하지만, **④는 A_CNT와 B_CNT 모두 상품별 전체 행 수**를 구한다.

| 상품 | 등급 구성 | ①~③ A_CNT | ①~③ B_CNT | ④ A_CNT | ④ B_CNT |
| ---- | --------- | --------- | --------- | ------- | ------- |
| 사과 | A, B      | 1         | 1         | 2       | 2       |
| 배   | A, A      | 2         | 0         | 2       | 2       |
| 포도 | C         | 0         | 0         | 1       | 1       |

> [!tip] SUM과 COUNT에 CASE를 넣을 때
> **SUM은 숫자를 더하고, COUNT(표현식)는 NULL이 아닌 값을 센다.**
> 0도 COUNT의 대상이다. COUNT로 조건을 만족하는 행만 세려면 조건이 맞지 않을 때 **NULL을 반환**하도록 한다.
> 따라서 이번 문제에서 나머지와 결과가 다른 것은 **④**이다.

참고 : [Oracle CASE](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/CASE-Expressions.html), [Oracle COUNT](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/COUNT.html)

LIKE

- % : 0개 이상의 모든 문자
- \_ : 정확히 1개의 모든 문자

서브쿼리의 종류
서브쿼리는 SQL문 안에 포함된 SELECT문이다. **행 수 / 컬럼 수 / 메인 쿼리 참조 여부**로 분류한다.

| 분류 기준           | 종류                        | 구분하는 핵심                                                      |
| ------------------- | --------------------------- | ------------------------------------------------------------------ |
| 반환 행 수          | 단일 행 서브쿼리            | 결과가 최대 1행. 한 컬럼의 값 하나를 반환하면 `=` 등으로 비교 가능 |
| 반환 행 수          | 다중 행 서브쿼리            | 결과가 여러 행. `IN`, `ANY`, `ALL` 등의 연산자로 비교              |
| 반환 컬럼 수        | 다중 컬럼(다중 열) 서브쿼리 | SELECT절에 2개 이상의 컬럼을 반환하여 여러 컬럼을 함께 비교        |
| 메인 쿼리 참조 여부 | 연관(상관) 서브쿼리         | 서브쿼리 내부에서 메인 쿼리의 컬럼을 참조                          |

1. 단일 행 서브쿼리 예시 : 전체 최고 급여와 같은 급여를 받는 직원

```sql
SELECT EMP_NAME, SALARY
FROM EMPLOYEES
WHERE SALARY = (SELECT MAX(SALARY) FROM EMPLOYEES);
```

GROUP BY 없는 MAX 집계는 결과가 1행이므로 값 하나와 비교할 수 있다. 메인 쿼리에서는 최고 급여를 받는 직원이 여러 명 조회될 수 있다.

2. 다중 행 서브쿼리 예시 : 10번 부서 직원들의 급여 중 하나와 같은 급여를 받는 직원

```sql
SELECT EMP_NAME, SALARY
FROM EMPLOYEES
WHERE SALARY IN (SELECT SALARY FROM EMPLOYEES WHERE DEPT_ID = 10);
```

10번 부서에 직원이 여러 명이면 서브쿼리가 여러 행을 반환할 수 있으므로 IN으로 비교한다.

3. 다중 컬럼 서브쿼리 예시 : 각 부서에서 최고 급여를 받는 직원

```sql
SELECT EMP_NAME, DEPT_ID, SALARY
FROM EMPLOYEES
WHERE (DEPT_ID, SALARY) IN (
    SELECT DEPT_ID, MAX(SALARY)
    FROM EMPLOYEES
    GROUP BY DEPT_ID
);
```

서브쿼리가 **부서번호와 최고 급여라는 두 컬럼**을 반환한다. 메인 쿼리도 `(부서번호, 급여)`를 **한 쌍으로** 비교한다. 이 예시는 여러 부서가 있으면 다중 행이면서 다중 컬럼인 서브쿼리이다.

4. 연관 서브쿼리 예시 : 자신이 속한 부서의 평균 급여보다 많이 받는 직원

```sql
SELECT E.EMP_NAME, E.SALARY
FROM EMPLOYEES E
WHERE E.SALARY > (
    SELECT AVG(S.SALARY)
    FROM EMPLOYEES S
    WHERE S.DEPT_ID = E.DEPT_ID
);
```

서브쿼리 안의 **E.DEPT_ID**가 메인 쿼리의 컬럼이다. 메인 쿼리에서 비교하는 직원의 부서에 따라 평균 급여 계산 대상이 달라진다. 이 예시는 연관 서브쿼리이면서 단일 행 서브쿼리이다.

> [!tip] ‘행’과 ‘컬럼’을 구분하기
> **여러 행(세로) = 다중 행 / 여러 컬럼(가로) = 다중 컬럼 / 바깥 쿼리의 컬럼 참조 = 연관**
> 분류 기준이 다르므로 한 서브쿼리에 여러 분류를 함께 적용할 수 있다.
> 이번 문제의 “SELECT절에 두 개 이상의 컬럼을 기술” → **② 다중 컬럼 서브쿼리**

서브쿼리와 비교 연산자 (`=` / `IN`)

- `컬럼 = (서브쿼리)` : 컬럼의 값을 서브쿼리가 반환한 **하나의 값**과 비교한다.
  - 이처럼 값 하나가 필요한 위치에 쓰는 서브쿼리를 스칼라 서브쿼리라고 한다. **1개 열, 최대 1개 행**을 반환해야 한다.
  - 1행 반환 : 해당 값과 비교한다.
  - 0행 반환 : 서브쿼리의 값을 NULL로 취급한다. `컬럼 = NULL`은 UNKNOWN이므로 WHERE 조건을 만족하는 행이 없다.
  - 2행 이상 반환 : 오류가 발생한다. 같은 값이 중복되어 여러 행으로 반환되어도 오류이다.
  - `>`, `<`, `>=`, `<=`, `<>`로 서브쿼리의 값 하나를 직접 비교할 때도 같은 행 수 제한이 적용된다.
- `컬럼 IN (서브쿼리)` : 서브쿼리 결과의 **여러 값 중 하나와 일치하는지** 검사한다.
  - 단일 컬럼을 비교하는 이 형태에서는 서브쿼리도 1개 열을 반환해야 하며, 여러 행을 반환할 수 있다.
  - 여러 행이 나올 수 있고 그중 하나와 일치하는 행을 찾으려면 IN을 사용한다.
- `컬럼 NOT IN (서브쿼리)` : 서브쿼리 결과 목록에 포함되지 않는 값을 검사한다. 여러 행을 반환하는 서브쿼리에 사용할 수 있다.

이번 문제의 예시
TBL1에 `(10, 'A'), (20, 'A'), (30, 'B')`가 있을 때:

```sql
SELECT *
FROM TBL1
WHERE C1 = (SELECT C1 FROM TBL1 WHERE C2 = 'A');
```

서브쿼리는 10과 20을 **두 행**으로 반환한다. `=` 오른쪽에는 값 하나가 필요하므로 **④ 오류가 발생한다.**

여러 값 중 하나와 일치하는 행을 조회하려면:

```sql
SELECT *
FROM TBL1
WHERE C1 IN (SELECT C1 FROM TBL1 WHERE C2 = 'A');
```

`C1 IN (10, 20)`과 같은 의미이므로 `(10, 'A')`, `(20, 'A')` 두 행이 조회된다.

> [!tip] 서브쿼리 결과의 행 수부터 확인
> **값 하나와 비교 = `=` / 여러 값 중 하나와 일치 = `IN`**
> `= (서브쿼리)`의 행 수 제한은 **오른쪽 서브쿼리 결과**에 대한 것이다. 메인 SELECT문의 조회 결과는 여러 행일 수 있다.

NOT IN의 결과 목록에 NULL이 있을 때
이번 문제는 EMP의 부서번호가 10, 20이고, DEPT의 부서번호가 30, 40, NULL이다.

```sql
SELECT DEPTNO
FROM EMP
WHERE DEPTNO NOT IN (SELECT DEPTNO FROM DEPT);
```

서브쿼리 결과는 `(30, 40, NULL)`이다. 단일 컬럼의 NOT IN은 **목록의 모든 값과 같지 않아야 TRUE**가 되므로 다음처럼 해석한다.

```sql
WHERE DEPTNO <> 30
  AND DEPTNO <> 40
  AND DEPTNO <> NULL
```

| EMP.DEPTNO | <> 30 | <> 40 | <> NULL | AND 최종 결과 | 조회 여부 |
| ---------- | ----- | ----- | ------- | ------------- | --------- |
| 10         | TRUE  | TRUE  | UNKNOWN | UNKNOWN       | 제외      |
| 20         | TRUE  | TRUE  | UNKNOWN | UNKNOWN       | 제외      |

- **NULL은 비교 목록에서 자동으로 빠지지 않는다.** `10 <> NULL`과 `20 <> NULL`은 TRUE가 아닌 UNKNOWN이다.
- `TRUE AND TRUE AND UNKNOWN`은 UNKNOWN이다. WHERE는 **TRUE인 행만** 통과시키므로 두 행 모두 제외된다.
- 따라서 결과는 **0행**, 정답은 **①**이다. ③의 2행은 목록에서 NULL을 제외했을 때의 결과이다.

목록의 NULL을 제외하고 비교하려면:

```sql
SELECT DEPTNO
FROM EMP
WHERE DEPTNO NOT IN (
    SELECT DEPTNO
    FROM DEPT
    WHERE DEPTNO IS NOT NULL
);
```

이제 `(30, 40)`만 비교하므로 EMP의 10, 20이 모두 조회된다.

> [!tip] 단일 컬럼 NOT IN을 만나면 NULL부터 확인
> **비교 목록에 NULL이 하나라도 있으면 NOT IN 조건은 TRUE가 될 수 없어 조회 결과가 0행이다.** 일치하는 값이 있으면 FALSE, 일치하는 값이 없어도 NULL 비교 때문에 UNKNOWN이다.
> NULL은 ‘30, 40과는 다른 제3의 일반 값’으로 취급할 수 없다. NULL과 일반 비교 연산을 하면 UNKNOWN이 된다.

참고 : [Oracle IN·NOT IN의 NULL 처리](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/IN-Condition.html), [Oracle 논리 조건과 UNKNOWN](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Logical-Conditions.html)

SQL의 논리적 처리 순서와 SELECT 별칭 (이번 문제의 Oracle 19c 등 기준)
기본 SELECT문의 의미를 해석할 때는 아래 순서로 이해한다. DBMS가 실제로 수행하는 물리적 실행 순서와는 구분한다.

```text
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
```

| 단계     | 역할                                                |
| -------- | --------------------------------------------------- |
| FROM     | 조회할 테이블과 조인 결과를 정함                    |
| WHERE    | 그룹화 전 개별 행을 필터링                          |
| GROUP BY | 행을 그룹으로 묶음                                  |
| HAVING   | 집계 결과 등을 기준으로 그룹을 필터링               |
| SELECT   | 출력할 컬럼·표현식을 정하고 결과 컬럼에 별칭을 붙임 |
| ORDER BY | 최종 결과를 정렬                                    |

- GROUP BY는 SELECT보다 앞 단계이므로 **같은 쿼리 블록의 SELECT절에서 붙인 컬럼 별칭을 사용할 수 없다** (Oracle 19c 등).
- ORDER BY는 SELECT보다 뒤 단계이므로 **SELECT 컬럼 별칭을 사용할 수 있다.**
- 여기서 제한되는 것은 SELECT 컬럼 별칭이다. `FROM EMP E`처럼 FROM에서 정한 테이블 별칭을 이용한 `GROUP BY E.DEPTNO`는 가능하다.

이번 문제의 ③ : SELECT 별칭 DNO를 GROUP BY에서 사용하여 오류

```sql
SELECT DEPTNO AS DNO, AVG(SAL)
FROM EMP
GROUP BY DNO;
```

올바르게 고치려면 원래 컬럼명으로 묶는다.

```sql
SELECT DEPTNO AS DNO, AVG(SAL)
FROM EMP
GROUP BY DEPTNO;
```

ORDER BY 2의 의미
**일반 SELECT문의 ORDER BY에 쓴 정수는 SELECT 목록에서의 위치(1부터 시작)**를 뜻한다.

```sql
SELECT DEPTNO, AVG(SAL) AS AVG_SAL
FROM EMP
GROUP BY DEPTNO
ORDER BY 2;
```

- 1번째 항목 : DEPTNO
- 2번째 항목 : AVG(SAL), 별칭은 AVG_SAL
- 따라서 이 쿼리의 `ORDER BY 2`는 **AVG(SAL)을 기준으로 오름차순 정렬**한다. 값이 2인 부서나 두 번째 행을 지정하는 뜻이 아니다.
- `ORDER BY AVG_SAL`, `ORDER BY 2`, `ORDER BY AVG(SAL)`은 이 예시에서 같은 정렬을 한다. DESC가 없으므로 모두 ASC이다.
- 문제 데이터의 결과 순서는 부서 30(평균 1600) → 부서 20(평균 3000) → 부서 10(평균 5000)이다.

> [!tip] 이번 문제의 판별 기준
> **GROUP BY는 원래 컬럼명, ORDER BY는 SELECT 별칭·위치·집계식을 사용할 수 있다.**
> 따라서 ①·②·④는 정상이며, 오류가 발생하는 것은 **③**이다.

> [!note] Oracle 버전 차이
> 이 문항은 GROUP BY에서 SELECT 별칭을 지원하지 않는 Oracle 19c 등의 문법을 전제한다. **Oracle Release 23부터는 GROUP BY에서 SELECT 별칭 사용을 지원**하므로, 모든 버전에 동일하게 적용되는 제한은 아니다.

참고 : [Oracle 19c SELECT](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/SELECT.html), [Oracle SELECT — Release 23부터 GROUP BY 별칭 지원](https://docs.oracle.com/en/database/oracle/oracle-database/26/sqlrf/SELECT.html)

ORDER BY 생략 시 조회 순서와 출력하지 않는 컬럼으로 정렬

- **ORDER BY를 생략하면 결과 행의 순서는 보장되지 않는다.** 입력 순서, 기본키 순서, 이전 조회 순서가 유지된다고 가정하면 안 된다.
- 같은 데이터라도 인덱스 사용 여부, 실행 계획, 병렬 처리 등에 따라 읽는 방식과 반환 순서가 달라질 수 있다.
- 어떤 조회에서 우연히 입력한 순서대로 나왔더라도, 이후 실행에서도 그 순서가 보장되는 것은 아니다. 원하는 순서는 ORDER BY로 명시한다.

```sql
SELECT ENAME FROM EMP;
-- 조회 순서 보장 없음

SELECT ENAME FROM EMP ORDER BY ENAME;
-- 이름 오름차순 정렬

SELECT ENAME FROM EMP ORDER BY SAL DESC;
-- 이름만 출력하되 급여 내림차순으로 정렬
```

일반 SELECT에서는 **출력할 컬럼과 정렬할 컬럼이 달라도 된다.** 위 세 번째 쿼리는 SELECT에 SAL이 없어도 SAL로 정렬할 수 있다. 단, DISTINCT나 GROUP BY를 사용한 쿼리는 ORDER BY 표현식에 별도 제한이 있다.

> [!tip] ORDER BY 설명 문제
> **별칭 정렬 가능 / 방향 생략 시 ASC / ORDER BY 생략 시 순서 보장 없음 / 일반 SELECT는 출력하지 않는 컬럼으로도 정렬 가능**
> 이번 문제에서 틀린 설명은 **③ ‘ORDER BY가 없으면 입력 순서대로 조회된다’**이다.

참고 : [Oracle SELECT — ORDER BY와 조회 순서](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/SELECT.html)

ROWNUM과 ORDER BY : 급여 상위 N명 조회 (Oracle)

- ROWNUM은 쿼리가 행을 선택하면서 **1부터 부여하는 번호**이다. 테이블에 저장된 고정 번호나 급여 순위가 아니다.
- **같은 쿼리 블록에서는 WHERE의 ROWNUM 조건으로 행을 제한한 뒤 ORDER BY로 정렬한다.**

① : 먼저 선택된 3행만 급여순으로 정렬 → 급여 상위 3명 보장 불가

```sql
SELECT ENAME, SAL
FROM EMP
WHERE ROWNUM <= 3
ORDER BY SAL DESC;
```

예를 들어 CLARK(2450), BLAKE(2850), JONES(2975)가 먼저 읽혔다면 이 세 명만 뽑힌다. 이후 급여 내림차순 정렬을 해도 JONES → BLAKE → CLARK가 되며, KING(5000)은 결과에 들어오지 않는다.

② : 전체를 급여순으로 정렬한 결과에서 3행 선택 → 올바른 상위 3명

```sql
SELECT ENAME, SAL
FROM (
    SELECT ENAME, SAL
    FROM EMP
    ORDER BY SAL DESC
)
WHERE ROWNUM <= 3;
```

FROM절의 서브쿼리를 **인라인 뷰**라고 한다. 안쪽 쿼리에서 정렬하고, 바깥 쿼리에서 그 결과에 ROWNUM 조건을 적용하므로 KING(5000), SCOTT(3000), FORD(3000)가 선택된다.

| 쿼리 구조                                 | 행을 선택하는 기준                     |
| ----------------------------------------- | -------------------------------------- |
| ① 같은 블록에 ROWNUM과 ORDER BY           | 먼저 선택된 3행 → 그 3행만 정렬        |
| ② 인라인 뷰에서 ORDER BY, 바깥에서 ROWNUM | 전체 급여 정렬 → 그 결과의 앞 3행 선택 |

> [!tip] 표에 그려진 순서로 읽힌다고 가정하지 않기
> 문제의 표가 이미 급여순으로 보이더라도, 테이블에서 행을 읽는 순서는 보장되지 않는다. ①이 우연히 같은 결과를 낼 수 있어도 상위 3명 조회를 보장하는 구조는 **②**이다.
> ④도 ①과 같은 쿼리 구조이므로 같은 문제가 있다.

③의 `WHERE ROWNUM > 1 AND ROWNUM <= 4`가 결과를 반환하지 않는 이유
같은 쿼리 블록에서 ROWNUM을 직접 검사하면, 첫 후보 행의 ROWNUM은 1이므로 `> 1`을 통과하지 못한다. 반환된 첫 행이 없으므로 다음 후보도 여전히 1로 검사되어 탈락한다. 따라서 2~4번째 행을 선택하는 방법으로 쓸 수 없다.

참고 : [Oracle ROWNUM](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ROWNUM-Pseudocolumn.html)

행 수 제한 (`OFFSET` / `FETCH`)
정렬된 조회 결과의 일부만 가져올 때 사용한다. **ORDER BY로 정렬 → OFFSET으로 건너뛰기 → FETCH로 가져오기** 순서로 이해한다.

```sql
SELECT C1, C2
FROM TBL1
ORDER BY C1 DESC
OFFSET 3 ROWS
FETCH FIRST 5 ROWS ONLY;
```

| 구문                      | 의미                                                                      |
| ------------------------- | ------------------------------------------------------------------------- |
| `OFFSET 3 ROWS`           | 앞의 3행을 건너뛴다. 따라서 4번째 행부터 조회한다.                        |
| `FETCH FIRST 5 ROWS ONLY` | 건너뛴 뒤 남은 결과의 앞에서 최대 5행만 가져온다.                         |
| `ROW` / `ROWS`            | ‘행’이라는 단위를 나타낸다. 둘은 같은 의미이며 별도 연산이 아니다.        |
| `FIRST` / `NEXT`          | 이번 Oracle 구문에서는 같은 의미이다. OFFSET 이후 남은 결과에서 가져온다. |
| `ONLY`                    | 지정한 행 수까지만 가져오도록 제한한다.                                   |

- `FETCH FIRST 1 ROW ONLY` : 최대 1행을 가져온다.
- `FETCH FIRST ROW ONLY` : 개수를 생략하면 1행을 뜻한다.
- `FIRST`가 있어도 OFFSET으로 건너뛴 행을 다시 가져오지 않는다.

이번 문제의 처리 과정

1. `ORDER BY C1 DESC` : C1이 `80, 70, 60, 50, 40, 30, 20, 10` 순서가 된다.
2. `OFFSET 3 ROWS` : 앞의 `80, 70, 60`을 건너뛴다.
3. `FETCH FIRST 5 ROWS ONLY` : 남은 `50, 40, 30, 20, 10`에 해당하는 5행을 반환한다. C2는 순서대로 `E, D, C, B, A`이다.

> [!warning] OFFSET이 전체 행 수 이상이면?
> **오류 없이 0행(빈 결과)**을 반환한다. 숫자 0을 담은 행을 반환하는 뜻이 아니다.
> 전체가 8행이면 `OFFSET 8 ROWS`나 `OFFSET 10 ROWS` 모두 빈 결과가 된다.
> FETCH 개수보다 남은 행이 적으면 남은 행만 반환한다. 예를 들어 8행에서 `OFFSET 6 ROWS FETCH FIRST 5 ROWS ONLY`는 2행만 반환한다.
> 따라서 이번 문제에서 적절하지 않은 설명은 **② ‘건너뛸 행이 부족하면 오류가 발생한다’**이다.

ORDER BY 없이도 Oracle에서 이 구문을 쓸 수 있지만, 어느 행을 건너뛰고 가져올지 일관되게 정하려면 ORDER BY를 사용한다.
참고 : [Oracle SELECT — 행 수 제한](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/SELECT.html#GUID-CFA006CA-6FF1-4972-821E-6996142A51C6)

집계 함수
COUNT

- `COUNT(*)` : NULL 포함 여부와 관계없이 전체 행 수
- `COUNT(1)` : 모든 행에서 NULL이 아닌 상수 1을 세므로 전체 행 수
- `COUNT(컬럼)` : 해당 컬럼이 NULL이 아닌 행의 수 (중복 포함)
- `COUNT(DISTINCT 컬럼)` : 해당 컬럼에서 NULL을 제외한 고유 값의 수. 중복된 값은 하나로 센다.

예 : 컬럼 값이 `10, 10, 20, NULL`이면 COUNT(\*)와 COUNT(1)은 **4**, COUNT(컬럼)은 **3**, COUNT(DISTINCT 컬럼)은 **2**이다.

GROUP BY·HAVING과 ‘결과 없음’ / ‘COUNT = 0’ 구분

- `WHERE` : 그룹을 만들기 전에 개별 행을 필터링한다.
- `GROUP BY` : 남은 행을 기준 컬럼별로 묶는다.
- `HAVING` : 집계 결과 등을 조건으로 **그룹을 필터링**한다.
- GROUP BY를 사용한 COUNT는 **각 그룹 안의 행 수**를 계산한다. HAVING을 통과한 그룹의 개수를 세는 것이 아니다.
- HAVING을 통과한 그룹이 없으면 **출력할 결과 행 자체가 없다(0행)**. `RESULT = 0`인 행을 만드는 것이 아니다.

이번 문제의 SQL A

```sql
SELECT COUNT(*) AS RESULT
FROM T_SALES
WHERE CUST IS NOT NULL
GROUP BY CUST
HAVING SUM(AMT) > 500;
```

1. WHERE에서 CUST가 NULL인 행을 제외한다.
2. CUST별로 X, Y 두 그룹을 만든다. SUM은 AMT의 NULL을 제외한다.

| 그룹 | AMT 값    | SUM(AMT) | COUNT(\*) | COUNT(CUST) | HAVING SUM(AMT) > 500 |
| ---- | --------- | -------- | --------- | ----------- | --------------------- |
| X    | NULL, 200 | 200      | 2         | 2           | 불만족 → 그룹 제외    |
| Y    | 100, 300  | 400      | 2         | 2           | 불만족 → 그룹 제외    |

3. 두 그룹 모두 HAVING을 통과하지 못하므로 최종 결과는 **빈 결과(0행)**이다.
   SQL B의 COUNT(CUST)도 같다. WHERE에서 CUST의 NULL을 이미 제외했으므로 각 그룹의 COUNT(CUST)는 COUNT(\*)와 같으며, 최종적으로 두 그룹 모두 제외된다.
   따라서 **① A·B 모두 결과 없음**이다. ④의 `RESULT = 0`은 숫자 0을 담은 결과 행이 하나 있다는 뜻이므로 다르다.

비교 : GROUP BY와 HAVING 없이 전체 행 수만 세는 경우

```sql
-- 이번 데이터에는 AMT > 500인 행이 없다.
SELECT COUNT(*) AS RESULT
FROM T_SALES
WHERE AMT > 500;
```

이 쿼리는 필터링 후 남은 전체 행 수를 세므로 **RESULT = 0인 행 하나**를 반환한다.

> [!tip] 결과의 행 수와 행 안의 값을 구분
> **결과 없음 = 출력 행 0개 / RESULT = 0 = 출력 행 1개, 그 안의 값이 0**
> COUNT가 있어도 GROUP BY·HAVING이 있다면 최종 결과가 0행일 수 있다.

join의 종류

- EQUI JOIN
  조인 조건에서 등호(=)를 사용하여 두 테이블의 컬럼값이 정확히 일치하는 경우에만 데이터 연결하는 가장 기본적인 조인
- NON EQUI JOIN (비등가 조인)
  등호 대신 BETWEEN, >, < 등의 범위·비교 조건으로 두 테이블의 행을 연결하는 조인
- INNER JOIN
  조인 조건에 맞는 데이터만을 반환
- OUTER JOIN
  - LEFT OUTER JOIN : 왼쪽의 모든 행을 유지하고, 오른쪽에 일치하는 행이 없으면 오른쪽 컬럼을 NULL로 채움
  - RIGHT OUTER JOIN : 오른쪽의 모든 행을 유지하고, 왼쪽에 일치하는 행이 없으면 왼쪽 컬럼을 NULL로 채움
  - FULL OUTER JOIN : 양쪽의 모든 행을 유지하고, 일치하지 않는 쪽의 컬럼을 NULL로 채움
- NATURAL JOIN
  ON 없이 두 테이블의 이름과 유형이 모두 동일한 컬럼끼리 EQUI JOIN
- CROSS JOIN (카테시안 곱)
  한 쪽 테이블의 모든 행이 다른쪽 테이블의 모든 행과 한 번씩 연결되는 조인

FROM의 인라인 뷰·별칭과 WHERE의 범위 조인 해석

```sql
SELECT A.DEPTNO, B.GRADE
FROM (
    SELECT DEPTNO, AVG(SAL) AS AVG_SAL
    FROM EMP
    GROUP BY DEPTNO
) A, SALGRADE B
WHERE A.AVG_SAL BETWEEN B.LOSAL AND B.HISAL;
```

1. FROM절의 괄호 안 SELECT를 먼저 하나의 결과 표로 이해한다.
   부서별 평균 급여를 구한 **인라인 뷰**이며, 그 결과에 **A라는 별칭**을 붙인 것이다. A는 원래 있던 테이블 이름이 아니다.

| A.DEPTNO | 평균 계산         | A.AVG_SAL |
| -------- | ----------------- | --------- |
| 10       | (1300 + 5000) / 2 | 3150      |
| 20       | (800 + 3000) / 2  | 1900      |

2. `SALGRADE B`는 실제 SALGRADE 테이블을 B라는 별칭으로 부른다는 뜻이다.
   따라서 `A.AVG_SAL`은 A 결과 표의 평균 급여, `B.LOSAL`·`B.HISAL`은 B 등급표의 급여 하한·상한이다.

3. `FROM A, B`처럼 쉼표로 나열한 대상들을 WHERE의 조인 조건으로 연결한다.
   이 문제는 평균 급여가 등급표의 어느 구간에 들어가는지 검사하는 **비등가 조인**이다.

```sql
A.AVG_SAL BETWEEN B.LOSAL AND B.HISAL
-- 아래와 같은 의미 (양 끝 포함)
A.AVG_SAL >= B.LOSAL AND A.AVG_SAL <= B.HISAL
```

| 부서 | 평균 급여 | 일치하는 B의 범위 | B.GRADE |
| ---- | --------- | ----------------- | ------- |
| 10   | 3150      | 3001~9999         | 5       |
| 20   | 1900      | 1401~2000         | 3       |

4. SELECT에서 A.DEPTNO와 B.GRADE만 출력하므로 최종 결과에 포함되는 행은 **(10, 5), (20, 3)**이며 정답은 **②**이다.

같은 조인을 JOIN ... ON으로 표현하면:

```sql
SELECT A.DEPTNO, B.GRADE
FROM (
    SELECT DEPTNO, AVG(SAL) AS AVG_SAL
    FROM EMP
    GROUP BY DEPTNO
) A
JOIN SALGRADE B
  ON A.AVG_SAL BETWEEN B.LOSAL AND B.HISAL;
```

> [!tip] FROM에 괄호와 별칭이 보이면
> **괄호 안 SELECT가 만든 표 = A / 등급표 SALGRADE = B / WHERE = A와 B를 연결하는 조건**으로 나누어 읽는다.
> 안쪽 SELECT에서 만든 AVG_SAL은 A의 결과 컬럼이므로 바깥 WHERE에서 사용할 수 있다. 같은 쿼리 블록의 SELECT 별칭을 WHERE에서 사용하는 경우와는 다르다.
> 쉼표는 두 조회 대상을 나열하는 문법이며, 결과를 위아래로 합치는 UNION이라는 뜻이 아니다.

셀프 조인
같은 테이블을 서로 다른 별칭으로 두 번 참조하여 조인한다. 부모·자식, 직원·관리자 관계를 연결할 때 사용한다.

```SQL
SELECT 별칭1.컬럼, 별칭2.컬럼
FROM 테이블 별칭1, 테이블 별칭 2
WHERE 조인 조건;
```

Oracle 외부 조인 기호 `(+)`
**조인 조건에서 `(+)`가 붙은 쪽에 일치하는 행이 없어도, 반대쪽의 행은 유지**한다. 일치하지 않는 쪽의 컬럼 값은 NULL로 채운다.

| 예전 Oracle 표기   | JOIN ... ON으로 표현              | 유지하는 쪽 |
| ------------------ | --------------------------------- | ----------- |
| `A.KEY = B.KEY(+)` | `A LEFT JOIN B ON A.KEY = B.KEY`  | A           |
| `A.KEY(+) = B.KEY` | `A RIGHT JOIN B ON A.KEY = B.KEY` | B           |

이번 문제의 ① : 부품과 그 부모를 연결하는 내부 셀프 조인

```sql
SELECT A.PART_NAME, B.PART_NAME AS PARENT_PART_NAME
FROM PARTS A, PARTS B
WHERE A.PARENT_PART_ID = B.PART_ID;
```

- A는 부품, B는 그 부품의 부모 역할이다. 같은 PARTS를 두 역할로 나누어 참조한다.
- PISTON·SPARKPLUG의 부모 ID 100은 ENGINE의 PART_ID와 일치한다.
- TIRE의 부모 ID 200은 WHEEL의 PART_ID와 일치한다.
- ENGINE·WHEEL은 부모 ID가 NULL이어서 일치하는 부모 행이 없으므로 결과에서 제외된다. 따라서 원하는 3행을 반환하는 정답은 **①**이다.

③의 차이

```sql
WHERE A.PARENT_PART_ID = B.PART_ID(+)
```

**부모 B가 없어도 부품 A는 출력**한다는 뜻이다. 아래 LEFT JOIN과 같다.

```sql
SELECT A.PART_NAME, B.PART_NAME AS PARENT_PART_NAME
FROM PARTS A
LEFT JOIN PARTS B
  ON A.PARENT_PART_ID = B.PART_ID;
```

따라서 PISTON·SPARKPLUG·TIRE 외에 **(ENGINE, NULL), (WHEEL, NULL)**도 포함되어 총 5행을 반환한다.

> [!tip] `(+)`의 위치로 유지할 쪽 찾기
> **B에 `(+)` → B의 짝이 없어도 A 유지 / A에 `(+)` → A의 짝이 없어도 B 유지**
> 이번 문제는 부모가 연결된 부품만 원하므로 외부 조인 기호가 없는 ①을 사용한다.

참고 : [Oracle 외부 조인과 (+) 표기](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Joins.html)

UNION ALL / UNION
UNION : 두 SELECT문의 결과를 합친다. 중복된 결과는 하나로 합친다.
UNION ALL : UNION과 동일하되 중복된 행 제거 없이 그대로 모두 보여줌

INTERSECT
두 SELECT 결과에 **공통으로 존재하는 행(교집합)**만 반환하며, **중복된 행은 하나만 남긴다.**
양쪽 결과에 같은 행이 여러 번 있어도 최종 결과에는 한 번만 나온다. 중복된 행을 아예 제외하는 것이 아니다.

예 : 한 컬럼을 조회한 두 결과가 다음과 같다면

```text
첫 번째 결과 : 10, 10, 20, 30
두 번째 결과 : 10, 10, 20, 40
INTERSECT 결과에 포함되는 값 : 10, 20 (각각 한 번)
```

```sql
SELECT C1 FROM TBL_A
INTERSECT
SELECT C1 FROM TBL_B;
```

> [!tip] 공통 행 선택 + 중복 제거
> **INTERSECT = 양쪽 공통 행만 선택 → 같은 행은 한 번만 출력**
> 여러 컬럼을 SELECT했다면 **선택한 모든 컬럼의 값이 같은 행**을 기준으로 공통 여부와 중복을 판단한다.
> 이번 문제의 정답은 **② ‘교집합을 출력하며 중복은 제거한다’**이다.

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

UNPIVOT : 여러 컬럼을 행으로 펼치기
가로로 나뉜 컬럼의 값을 **하나의 값 컬럼과, 그 값의 종류를 표시하는 컬럼**으로 바꾸어 세로 행으로 펼친다.

이번 문제의 원본
| DEPT_NAME | Q1_SALES | Q2_SALES |
| --- | --- | --- |
| 영업부 | 100 | 120 |
| 개발부 | 200 | 210 |

```sql
SELECT DEPT_NAME, QUARTER, SALES
FROM SALES_WIDE
UNPIVOT (
    SALES FOR QUARTER IN (
        Q1_SALES AS '1분기',
        Q2_SALES AS '2분기'
    )
);
```

| 구문 요소                 | 역할                                                                  |
| ------------------------- | --------------------------------------------------------------------- |
| `UNPIVOT`                 | 여러 컬럼을 세로 행으로 변환하는 연산                                 |
| `SALES`                   | 100, 120 같은 기존 컬럼의 **값**을 담을 새 컬럼 이름                  |
| `FOR QUARTER`             | 그 값이 어느 분기에서 왔는지 나타내는 **분류 값**을 담을 새 컬럼 이름 |
| `IN (Q1_SALES, Q2_SALES)` | 행으로 펼칠 원본 컬럼들을 지정                                        |
| `Q1_SALES AS '1분기'`     | Q1_SALES에서 가져온 행의 QUARTER 값에 **'1분기'**를 넣음              |
| `Q2_SALES AS '2분기'`     | Q2_SALES에서 가져온 행의 QUARTER 값에 **'2분기'**를 넣음              |

**QUARTER는 ‘분기’라는 뜻으로 작성자가 붙인 컬럼 이름**이다. 날짜에서 분기를 계산하는 함수나 UNPIVOT의 필수 키워드가 아니다. SALES도 새로 붙인 값 컬럼 이름이다.

변환 후 결과에 포함되는 행
| DEPT_NAME | QUARTER | SALES |
| --- | --- | --- |
| 영업부 | 1분기 | 100 |
| 영업부 | 2분기 | 120 |
| 개발부 | 1분기 | 200 |
| 개발부 | 2분기 | 210 |

부서마다 Q1_SALES와 Q2_SALES가 각각 별도의 행이 되므로 이번 데이터는 **2행 × 2개 분기 = 4행**이다. 정답은 **①**이다.
③처럼 QUARTER에 Q1_SALES·Q2_SALES라는 원래 컬럼명이 나오는 것은 IN 안의 AS 매핑을 생략했을 때이다.

> [!tip] 값과 분류 이름을 나누어 읽기
> **SALES = 매출액 / QUARTER = 그 매출액의 분기 이름 / IN = 펼칠 원본 컬럼과 분기 이름 매핑**
> `UNPIVOT (값을 담을 컬럼 FOR 분류를 담을 컬럼 IN (...))` 형태로 기억한다.
> Oracle UNPIVOT은 기본적으로 값이 NULL인 행을 제외한다. NULL도 포함하려면 `UNPIVOT INCLUDE NULLS (...)`를 사용한다.

참고 : [Oracle SELECT — UNPIVOT](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/SELECT.html)

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

3. 계산 범위(윈도우 프레임) : ROWS / RANGE BETWEEN
   SUM, AVG 등의 윈도우 함수에서 **현재 행의 계산에 주변의 어느 행까지 포함할지** 정한다.

```sql
SUM(SCORE) OVER (
    PARTITION BY TEAM_ID
    ORDER BY SCORE
    ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
)
```

- PARTITION BY : 팀별로 계산 영역을 나눈다.
- ORDER BY : 각 팀 안에서 점수 순으로 행을 정렬한다.
- ROWS BETWEEN : 정렬된 행의 위치를 기준으로 계산 범위를 정한다. 시작과 끝을 모두 포함한다.

| 키워드 (ROWS 기준)    | 뜻                         |
| --------------------- | -------------------------- |
| `BETWEEN 시작 AND 끝` | 계산 범위의 시작과 끝 지정 |
| `n PRECEDING`         | 현재 행보다 n행 앞         |
| `CURRENT ROW`         | 현재 행                    |
| `n FOLLOWING`         | 현재 행보다 n행 뒤         |
| `UNBOUNDED PRECEDING` | 현재 파티션의 맨 처음      |
| `UNBOUNDED FOLLOWING` | 현재 파티션의 맨 끝        |

PRECEDING은 ‘앞선’, FOLLOWING은 ‘뒤따르는’, UNBOUNDED는 ‘경계 제한이 없는’이라는 뜻이다. 앞·뒤는 **ORDER BY로 정렬된 순서**를 기준으로 판단한다.

자주 쓰는 범위

```sql
-- 앞 1행 + 현재 행 + 뒤 1행 : 최대 3행의 이동 합계
ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING

-- 파티션 처음부터 현재 행까지 : 누적 합계
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW

-- 현재 행부터 파티션 끝까지
ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING

-- 파티션 전체
ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
```

범위가 파티션의 처음이나 끝을 넘어가면 **해당 파티션 안에 실제로 존재하는 행만** 계산한다. 다른 팀의 행까지 포함하지 않는다.

이번 문제 : 앞 1행 + 현재 행 + 뒤 1행의 이동 합계 → **①**
문제에서 제시한 P1 → P2 → P3, P4 → P5 순서로 읽으면:

| 현재 행  | 팀  | 계산에 포함되는 선수 | 합계                  |
| -------- | --- | -------------------- | --------------------- |
| P1 (100) | 10  | P1 + P2              | 100 + 150 = 250       |
| P2 (150) | 10  | P1 + P2 + P3         | 100 + 150 + 150 = 400 |
| P3 (150) | 10  | P2 + P3              | 150 + 150 = 300       |
| P4 (200) | 20  | P4 + P5              | 200 + 250 = 450       |
| P5 (250) | 20  | P4 + P5              | 200 + 250 = 450       |

ROWS와 RANGE의 차이

- `ROWS` : 정렬된 **행 위치의 차이**를 기준으로 한다. `1 PRECEDING`은 바로 앞 1행이다.
- `RANGE` : **정렬 기준 값의 차이**를 기준으로 한다. `ORDER BY SCORE`가 오름차순일 때 `RANGE BETWEEN 1 PRECEDING AND 1 FOLLOWING`은 현재 점수 -1부터 +1까지의 모든 행이다.
  예 : 점수가 150이면 149~151 범위이므로 P2·P3만 포함되어 합계는 300이다. 앞뒤 1행씩이라는 뜻이 아니다. 따라서 ②는 원하는 결과와 다르다.
- ③은 처음부터 현재 행까지의 **누적 합계**, ④는 ORDER BY 없이 **팀 전체 합계**를 구하므로 이동 합계와 다르다.

> [!note] 같은 점수인 행의 순서
> 보기의 `ORDER BY SCORE`만으로는 동점인 P2·P3의 순서가 보장되지 않는다. 문제는 P2가 먼저라고 가정한 것이다.
> 실제 SQL에서 제시된 선수별 결과를 확실히 재현하려면, 이 데이터에서는 `ORDER BY SCORE, PLAYER_NAME`처럼 동점 순서를 정할 기준을 추가한다.

참고 : [Oracle 윈도우 함수의 계산 범위](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Analytic-Functions.html)

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
   현재 행의 **계산 범위(프레임) 안에서** ORDER BY 순서상 마지막에 위치한 행의 값 반환

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
> Oracle에서 LAST_VALUE처럼 프레임을 지원하는 함수에 ORDER BY를 쓰고 범위를 생략하면 기본값은 `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`이다. 현재 정렬 값과 같은 동점 행까지 포함한다.
> LAST_VALUE는 **그 계산 범위 안의 마지막 값**을 반환한다. 정렬 값이 유일하면 현재 행의 값이므로 파티션 전체의 마지막 값과 다를 수 있다.
> 파티션 전체의 마지막 값을 구하려면 위 예시처럼 `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`으로 전체 범위를 지정한다.

FIRST_VALUE·LAST_VALUE의 기본 범위 예제 : 왜 부서 마지막 직원이 계속 나오지 않는가?
이번 문제의 `OVER (PARTITION BY DEPTNO ORDER BY SAL)`은 범위를 생략했으므로 다음과 같이 해석한다.

```sql
OVER (
    PARTITION BY DEPTNO
    ORDER BY SAL ASC
    RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)
```

부서별 급여 오름차순으로 정렬하고, **부서 처음부터 현재 급여 값까지**의 행을 대상으로 FIRST_VALUE와 LAST_VALUE를 구한다. 이번 데이터는 부서 안에서 동점 급여가 없으므로 범위의 마지막 행이 현재 행이다.

| 부서 | 현재 직원 (급여) | 현재 행의 계산 범위   | FIRST_VALUE | LAST_VALUE |
| ---- | ---------------- | --------------------- | ----------- | ---------- |
| 10   | MILLER (1300)    | MILLER                | MILLER      | MILLER     |
| 10   | CLARK (2450)     | MILLER → CLARK        | MILLER      | CLARK      |
| 10   | KING (5000)      | MILLER → CLARK → KING | MILLER      | KING       |
| 20   | JONES (2975)     | JONES                 | JONES       | JONES      |
| 20   | SCOTT (3000)     | JONES → SCOTT         | JONES       | SCOTT      |

따라서 정답은 **③**이다. FIRST_VALUE는 계산 범위의 시작이 항상 부서의 처음이어서 같고, LAST_VALUE는 현재 행까지 확장되는 범위의 마지막이므로 행마다 달라진다.

①처럼 모든 행에 부서 전체의 마지막 직원(KING 또는 SCOTT)을 표시하려면:

```sql
LAST_VALUE(ENAME) OVER (
    PARTITION BY DEPTNO
    ORDER BY SAL
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
) AS LAST_EMP
```

> [!tip] PARTITION과 프레임을 함께 확인
> PARTITION BY는 부서를 나누고, 프레임은 **그 부서 안에서 현재 행의 계산에 포함할 범위**를 정한다.
> **FIRST/LAST는 그 계산 범위 안의 처음/마지막**이다. ORDER BY와 함께 범위를 생략하면 기본 끝은 CURRENT ROW이며, 부서 맨 끝까지 포함하려면 UNBOUNDED FOLLOWING을 지정한다.

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

| 메타 문자 | 설명                                      | 예시                                            |
| --------- | ----------------------------------------- | ----------------------------------------------- |
| ^         | 문자열 시작                               | ^A : 'A'로 시작하는 문자열                      |
| $         | 문자열 끝                                 | A$ : 'A'로 끝나는 문자열                        |
| .         | 임의의 한 글자                            | A.B : 'A'와 'B' 사이에 한 글자가 있는 문자열    |
| \*        | 바로 앞 문자나 그룹이 0번 이상 나타남     | A\*B : 'B'앞에 'A'가 없거나 여러 개 있는 문자열 |
| +         | 바로 앞 문자나 그룹이 1번 이상 나타남     | A+B : 'B' 앞에 A가 최소 1개 이상 있는 문자열    |
| ?         | 바로 앞 문자나 그룹이 0번 또는 1번 나타남 | A?B : 'B' 앞에 'A'가 없거나 1개 있는 문자열     |
| [ ]       | 괄호 안 문자 중 하나 의미                 | \[ABC] : 'A', 'B', 'C' 중 한 글자               |
| `\d`      | 숫자 한 글자                              | `\d+` : 숫자 1개 이상                           |
| `( )`     | 여러 문자를 하나의 그룹으로 묶음          | `(-Y)?` : `-Y` 전체가 없거나 한 번 있음         |
| \|        | 여러 단어나 패턴 중 하나                  | A\|B : 'A' 또는 'B'                             |
| {m, n}    | 바로 앞 문자가 m번 이상 n번 이하 나타남   | A{2,3} : 'A'가 2번 또는 3번 반복되는 문자열     |

정규 표현식 해석 예제 : `^[AB]-\d+(-Y)?$`
**A 또는 B로 시작 → 하이픈 → 숫자 1개 이상 → 선택적으로 -Y → 문자열 끝**으로 읽는다.

| 부분    | 해석                                                     |
| ------- | -------------------------------------------------------- |
| `^`     | 문자열 시작                                              |
| `[AB]`  | A 또는 B 중 한 글자. AB 두 글자가 아니다.                |
| `-`     | 하이픈 문자 그대로                                       |
| `\d+`   | 숫자가 1개 이상 이어짐. 정확히 3자리라는 뜻은 아니다.    |
| `(-Y)?` | `-Y` 전체가 0번 또는 1번 나타남 → 접미사 생략 가능       |
| `$`     | 문자열 끝. 지정한 패턴 뒤에 다른 문자가 더 붙으면 불일치 |

```sql
SELECT COUNT(*)
FROM PRODUCT
WHERE REGEXP_LIKE(PROD_CODE, '^[AB]-\d+(-Y)?$');
```

| 상품 코드 | 일치 여부 | 이유                                                   |
| --------- | --------- | ------------------------------------------------------ |
| A-001     | 일치      | A + 하이픈 + 숫자, 접미사 생략                         |
| B-002     | 일치      | B + 하이픈 + 숫자, 접미사 생략                         |
| A-003-X   | 불일치    | 선택적으로 허용하는 접미사는 -Y이며 -X는 허용하지 않음 |
| C-004     | 불일치    | 첫 글자가 A나 B가 아님                                 |
| B-005-Y   | 일치      | B + 하이픈 + 숫자 + -Y                                 |
| A-006-Z   | 불일치    | -Z는 허용하지 않음                                     |

따라서 조건에 맞는 행은 **3개**, 정답은 **③**이다.

> [!tip] 괄호와 물음표를 함께 읽기
> `(-Y)?`에서 물음표는 **괄호로 묶인 -Y 전체**에 적용된다. 숫자 뒤에 아무 접미사나 올 수 있다는 뜻이 아니다.
> `^`와 `$`를 함께 사용하면 문자열 전체가 지정한 형식에 맞아야 한다.

참고 : [Oracle 정규 표현식 문법](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Multilingual-Regular-Expression-Syntax.html), [Oracle 숫자 문자 표현](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Perl-influenced-Extensions-in-Oracle-Regular-Expressions.html)

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

중복 행 삭제 : 이름별 최소 LOG_ID만 남기기
이번 문제에서 남겨야 하는 로그 ID는 다음 서브쿼리로 구한다.

```sql
SELECT MIN(B.LOG_ID)
FROM EMP_LOG B
GROUP BY B.ENAME;
```

| 이름  | 해당 LOG_ID   | 남길 최소 LOG_ID |
| ----- | ------------- | ---------------- |
| SMITH | 101, 103, 105 | 101              |
| ALLEN | 102           | 102              |
| WARD  | 104           | 104              |

**MIN을 썼어도 GROUP BY가 있으므로 이름별 한 행씩, 총 3행(101, 102, 104)**을 반환한다.

③ : 보존 목록에 없는 로그를 삭제 → 올바름

```sql
DELETE FROM EMP_LOG A
WHERE A.LOG_ID NOT IN (
    SELECT MIN(B.LOG_ID)
    FROM EMP_LOG B
    GROUP BY B.ENAME
);
```

`NOT IN (101, 102, 104)`와 같은 의미이므로 **103, 105만 삭제**하고 이름별 최소 ID를 남긴다.

④ : 여러 행을 반환하는 서브쿼리를 >로 직접 비교 → 오류

```sql
DELETE FROM EMP_LOG A
WHERE A.LOG_ID > (
    SELECT MIN(B.LOG_ID)
    FROM EMP_LOG B
    GROUP BY B.ENAME
);
```

`>` 오른쪽에는 비교할 값 하나가 필요하지만, 서브쿼리는 3행을 반환한다. 자동으로 각 행의 이름에 해당하는 최소 ID를 골라 비교하지 않는다. 따라서 ④는 정답이 아니다.

‘자신의 이름에 해당하는 최소 ID보다 크면 삭제’라는 방식으로 작성하려면 **메인 쿼리의 이름과 연결하는 연관 조건**이 필요하다.

```sql
DELETE FROM EMP_LOG A
WHERE A.LOG_ID > (
    SELECT MIN(B.LOG_ID)
    FROM EMP_LOG B
    WHERE B.ENAME = A.ENAME
);
```

이 쿼리는 현재 A행과 이름이 같은 로그만 모아 최소 ID 하나를 구하므로 정상적으로 103, 105를 삭제한다.

> [!tip] 집계 함수가 있다고 반드시 단일 행은 아니다
> **GROUP BY 없는 MIN = 전체에 대해 한 행 / GROUP BY 있는 MIN = 그룹마다 한 행**
> 이번 ④에는 `B.ENAME = A.ENAME` 같은 연관 조건이 없다. 전체 이름별 최소 ID 목록을 반환하므로 단일 값 비교 연산자 `>`로 직접 비교할 수 없다.

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

- 트랜잭션 : 함께 확정하거나 취소하는 하나의 논리적 작업 단위. 하나 이상의 SQL문으로 구성될 수 있다.
- 세션 : 사용자가 DB에 접속하여 SQL을 실행하는 연결 단위. 같은 계정으로 접속해도 서로 다른 연결이면 별도 세션이다.
- `COMMIT` : 현재 트랜잭션의 변경 내용을 영구적으로 확정하고 트랜잭션을 끝낸다.
- `ROLLBACK` : 현재 트랜잭션에서 아직 확정하지 않은 변경을 취소한다. 이미 COMMIT한 변경은 이 명령으로 되돌릴 수 없다.
- `SAVEPOINT 이름` : 트랜잭션 안에 중간 지점을 만든다. `ROLLBACK TO 이름`으로 그 지점 이후의 변경만 취소할 수 있다.

COMMIT 전후에 보이는 데이터 (Oracle의 기본 READ COMMITTED 기준)

- UPDATE 등을 실행한 세션에서는 **자신이 변경한 미확정 데이터도 SELECT로 확인**할 수 있다.
- 다른 세션에서는 그 변경을 COMMIT하기 전까지 볼 수 없다. 기존 행을 수정한 경우에는 **변경 전의 확정된 값**을 조회한다.
- COMMIT이 완료된 뒤 다른 세션에서 새 SELECT를 실행하면 확정된 변경을 볼 수 있다.

예시 : 잔액이 100으로 확정된 계좌를 세션 A에서 80으로 변경 (자동 커밋은 꺼져 있다고 가정)

```sql
-- 세션 A
UPDATE ACCOUNT SET BALANCE = 80 WHERE ACCOUNT_ID = 1;

-- 아직 COMMIT하지 않았어도, 같은 세션에서 조회하면 80이 나온다.
SELECT BALANCE FROM ACCOUNT WHERE ACCOUNT_ID = 1;
```

| 시점                       | 세션 A에서 SELECT | 별도 세션 B에서 새 SELECT |
| -------------------------- | ----------------- | ------------------------- |
| 변경 전                    | 100               | 100                       |
| A가 UPDATE 실행, COMMIT 전 | **80**            | **100**                   |
| A가 COMMIT 완료 후         | 80                | 80                        |

A가 COMMIT 대신 ROLLBACK했다면 변경을 취소하므로 A에서 다시 조회해도 100이다. B는 미확정 값인 80을 본 적이 없다.

> [!tip] 변경 실행과 확정 구분하기
> **변경 실행 → 본인은 변경 결과 확인 가능 → COMMIT으로 확정 → 다른 세션의 새 조회에서도 확인 가능**
> SELECT로 자신의 변경을 확인해도 자동으로 COMMIT되는 것은 아니다.
> 다른 트랜잭션의 미확정 변경을 읽지 못하게 하는 것은 **격리성(Isolation)**, COMMIT된 변경을 영구적으로 보장하는 것은 **지속성(Durability)**과 관련된다.
> 이번 문제에서 적절하지 않은 설명은 **④ ‘다른 사용자가 COMMIT 이전의 변경 내용을 조회할 수 있다’**이다.

참고 : [Oracle 데이터 동시성과 일관성](https://docs.oracle.com/en/database/oracle/oracle-database/19/cncpt/data-concurrency-and-consistency.html), [Oracle 트랜잭션](https://docs.oracle.com/en/database/oracle/oracle-database/19/cncpt/transactions.html)

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
    - PRIMARY KEY : 중복과 NULL 모두 불가. 유일성 + NOT NULL을 함께 보장한다.
    - FOREIGN KEY
    - NOT NULL : NULL 입력 불가. 중복 여부는 제한하지 않는다.
    - UNIQUE : NULL이 아닌 값의 중복을 막는다. **NULL은 허용**한다 (별도 NOT NULL 제약이 없는 경우).
    	- Oracle의 단일 컬럼 UNIQUE에서는 **NULL을 여러 행에 입력할 수 있다.** 예 : `10, 20, NULL, NULL` 허용. 여기에 10을 다시 입력하면 중복 오류.
    	- 한 테이블에 서로 다른 컬럼 또는 컬럼 조합을 대상으로 UNIQUE 제약을 여러 개 만들 수 있다.
    	- NULL도 금지하려면 해당 컬럼에 NOT NULL을 함께 지정한다.
    	- 이번 문제의 틀린 설명은 **② ‘PRIMARY KEY처럼 NULL을 허용하지 않는다’**이다.
    	- 참고 : [Oracle 제약조건](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/constraint.html)
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
   테이블의 **모든 행을 한 번에 비우는 DDL**이다. 테이블 구조(컬럼, 제약조건 등)는 유지되므로 이후 다시 INSERT할 수 있다.
   WHERE 조건으로 일부 행만 지울 수 없다. Oracle에서는 ROLLBACK으로 취소할 수 없다.

DELETE / TRUNCATE / DROP 비교 (일반 테이블, Oracle 기준)

| 명령어                    | 분류 | 삭제 대상                             | WHERE 사용 | 테이블 구조 유지 | ROLLBACK으로 취소 |
| ------------------------- | ---- | ------------------------------------- | ---------- | ---------------- | ----------------- |
| `DELETE FROM 테이블명`    | DML  | 조건에 맞는 행. WHERE 생략 시 모든 행 | 가능       | 유지             | COMMIT 전 가능    |
| `TRUNCATE TABLE 테이블명` | DDL  | 모든 행                               | 불가       | 유지             | 불가              |
| `DROP TABLE 테이블명`     | DDL  | 테이블 자체 (구조와 데이터)           | 불가       | 삭제             | 불가              |

> [!tip] 삭제 범위로 기억하기
> **DELETE = 행 삭제 / TRUNCATE = 테이블 비우기 / DROP = 테이블 자체 삭제**
> TRUNCATE는 데이터를 지우는 명령이지만 분류는 **DDL**이다. ‘데이터를 지운다’는 이유만으로 DML로 판단하면 안 된다.
> 이번 문제에서 잘못 연결된 것은 **② DML: DROP**이다. DROP은 **DDL**에 속한다.

참고 : [Oracle TRUNCATE TABLE](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/TRUNCATE-TABLE.html)

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

해시 조인
