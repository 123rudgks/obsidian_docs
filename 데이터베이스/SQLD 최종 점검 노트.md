# SQLD 최종 점검 노트

퀴즈를 풀면서 다시 틀리거나 헷갈린 개념을 모아 최종 복습한다.
관련 개념 정리: [[SQLD 오답 노트]]

## 1. NOT IN의 비교 목록에 NULL이 있을 때

- 기록일: 2026-11-14
- 점검 상태: 원문과 NULL을 제외한 변형 문제 모두 힌트 후 정답 도출. 이후 힌트 없이 독립 재확인 필요.
- 처음 답한 내용: 10, 30이 조회된다고 생각했다.
- 보완할 개념: **NULL은 비교 목록에서 자동으로 제외되지 않는다.** NULL과의 비교는 UNKNOWN이다.

### 틀린 문제

EMP 테이블의 DEPTNO가 10, 20, 30일 때:

```sql
SELECT DEPTNO
FROM EMP
WHERE DEPTNO NOT IN (20, NULL);
```

정답은 **조회 결과 없음(0행)**이다.

### 왜 10, 30도 조회되지 않는가?

단일 컬럼의 NOT IN은 목록의 모든 값과 같지 않아야 TRUE가 된다.

```sql
DEPTNO NOT IN (20, NULL)
-- 다음 조건과 같은 의미
DEPTNO <> 20 AND DEPTNO <> NULL
```

| DEPTNO | <> 20 | <> NULL | AND 결과 | 조회 여부 |
| --- | --- | --- | --- | --- |
| 10 | TRUE | UNKNOWN | UNKNOWN | 제외 |
| 20 | FALSE | UNKNOWN | FALSE | 제외 |
| 30 | TRUE | UNKNOWN | UNKNOWN | 제외 |

WHERE는 **TRUE인 행만** 통과시킨다. FALSE와 UNKNOWN은 모두 제외된다.

### 최종 암기 문장

> 단일 컬럼 NOT IN의 비교 목록에 NULL이 하나라도 있으면 조건이 TRUE가 될 수 없으므로 조회 결과는 0행이다.

### 재확인 문제

EMP의 DEPTNO는 10, 20, 30이고, DEPT의 DEPTNO는 20, NULL이다.

```sql
SELECT DEPTNO
FROM EMP
WHERE DEPTNO NOT IN (
    SELECT DEPTNO
    FROM DEPT
    WHERE DEPTNO IS NOT NULL
);
```

- [ ] 서브쿼리가 반환하는 값을 먼저 말할 수 있다.
- [ ] 최종 조회 결과와 그 이유를 힌트 없이 설명할 수 있다.

### 재확인에서 추가로 헷갈린 부분

- 변형 문제에서 답한 내용: 10, 20, 30 모두 조회된다고 답했다.
- 서브쿼리의 `WHERE DEPTNO IS NOT NULL`은 **DEPT의 NULL만 제외**한다. 서브쿼리 결과에는 20이 남는다.
- 바깥 조건은 `EMP.DEPTNO NOT IN (20)`, 즉 `EMP.DEPTNO <> 20`이 된다. **목록에 남은 20은 여전히 제외 대상**이다.
- 올바른 결과: 10, 30.
- 핵심: **안쪽 WHERE로 비교 목록을 만든 뒤, 바깥 NOT IN으로 그 목록에 없는 행을 선택한다.**
- 피드백 후 답변: 10, 30으로 정정하여 정답 도출. 아직 힌트 없이 해결한 것으로 처리하지 않는다.

## 2. GROUP BY의 COUNT는 그룹 안의 행 수

- 기록일: 2026-11-14
- 점검 상태: 설명 후 개념을 다시 표현했고, 새 데이터의 유사 문제를 힌트 없이 정답 도출.
- 처음 답한 내용: Y, 1.
- 보완할 개념: **COUNT(*)는 각 그룹 안의 행 수를 센다. HAVING을 통과한 그룹의 개수를 세는 것이 아니다.**

### 틀린 문제

| CUST | AMT |
| --- | --- |
| X | 100 |
| X | 200 |
| Y | 300 |
| Y | 400 |

```sql
SELECT CUST, COUNT(*) AS CNT
FROM SALES
GROUP BY CUST
HAVING SUM(AMT) > 500;
```

### 계산 과정

| 그룹 | 포함된 AMT | SUM(AMT) | COUNT(*) | HAVING 통과 여부 |
| --- | --- | --- | --- | --- |
| X | 100, 200 | 300 | 2 | 제외 |
| Y | 300, 400 | 700 | 2 | 통과 |

최종 결과는 **(Y, 2)** 한 행이다. 출력 결과가 한 행이라는 사실과, 그 안의 CNT 값이 2라는 사실을 구분한다.

### 최종 암기 문장

> GROUP BY의 COUNT(*)는 그룹 안의 행 수, HAVING은 남길 그룹을 고르는 조건이다.

- [x] 출력 결과 행 수와 COUNT(*) 값을 구분할 수 있다.
- [x] 다른 데이터로도 그룹별 집계 후 HAVING 적용 결과를 힌트 없이 구할 수 있다.

### 이해한 내용

- GROUP BY와 함께 쓴 COUNT(*)는 **각 그룹에 속한 원본 행 수**를 구한다.
- HAVING은 집계 조건을 만족하는 그룹을 남기거나 제외한다. 남은 그룹의 COUNT(*)를 1로 바꾸지 않는다.
- 예를 들어 최종 결과가 2행이어도 각 행의 CNT는 각각 3, 2일 수 있다.
- 최종 결과 자체의 행 수를 세려면 그룹별 조회 결과를 인라인 뷰로 감싸고, 바깥 SELECT에서 COUNT(*)를 사용한다.

### 유사 문제 정답 기록

| CUSTOMER | AMOUNT |
| --- | --- |
| A | 100 |
| A | 200 |
| A | 300 |
| B | 400 |
| B | 500 |
| C | 100 |

```sql
SELECT CUSTOMER, COUNT(*) AS CNT
FROM ORDERS
GROUP BY CUSTOMER
HAVING SUM(AMOUNT) >= 600;
```

- 답변과 정답: **(A, 3), (B, 2)**.
- A는 합계 600, 원본 3행이고 B는 합계 900, 원본 2행이므로 통과한다. C는 합계 100이므로 제외된다.
- ‘HAVING을 통과하는 그룹이 없을 때’의 추가 문제는 아직 답변하지 않았으므로 별도 확인이 필요하다.

## 3. 값 하나를 비교하는 서브쿼리의 행 수 제한

- 기록일: 2026-11-14
- 점검 상태: 오류가 발생한다는 답은 맞혔으나 이유를 설명하지 못함. 설명 후 핵심을 확인했으며, 변형 문제는 미응답.
- 핵심: **`SAL = (서브쿼리)`의 오른쪽에는 값 하나가 필요하다. 서브쿼리가 두 행 이상을 반환하면 오류가 발생한다.**

### 확인한 문제

| NAME | DEPTNO | SAL |
| --- | --- | --- |
| A | 10 | 100 |
| B | 10 | 200 |
| C | 20 | 300 |

```sql
SELECT NAME
FROM EMP
WHERE SAL = (
    SELECT MAX(SAL)
    FROM EMP
    GROUP BY DEPTNO
);
```

서브쿼리는 부서별 최댓값을 구하므로 10번 부서의 200, 20번 부서의 300을 **두 행으로 반환**한다. `=`로 직접 비교할 값이 하나가 아니므로 오류가 발생한다.

### 최종 암기 문장

> 집계 함수를 사용해도 GROUP BY가 있으면 그룹마다 한 행이 나온다. 서브쿼리의 반환 행 수를 먼저 확인한다.

- `=`, `>`, `<` 등으로 서브쿼리의 단일 값을 직접 비교할 때는 **1개 컬럼, 최대 1행**이어야 한다. 0행은 NULL로 취급한다.
- 여러 결과 값 중 하나와 일치하는지 검사하려면 `IN (서브쿼리)`를 사용한다.
- 위 쿼리에서 GROUP BY를 제거하면 전체 최대 급여 300 하나를 반환하므로 C가 조회된다.
- 위 쿼리의 `=`를 `IN`으로 바꾸면 200 또는 300과 일치하는 B, C가 조회된다.

- [ ] 새로운 서브쿼리가 반환하는 행 수를 판단하고 오류 원인을 힌트 없이 설명할 수 있다.

## 4. LEFT JOIN과 LEFT OUTER JOIN은 같은 문법

- 기록일: 2026-11-14
- 핵심: **LEFT JOIN = LEFT OUTER JOIN**. OUTER는 생략할 수 있다.
- 두 표현 모두 왼쪽 테이블의 모든 행을 유지하며, 오른쪽에 일치하는 행이 없으면 오른쪽 컬럼을 NULL로 채운다.

```sql
FROM EMP E
LEFT JOIN DEPT D ON E.DEPTNO = D.DEPTNO

-- 같은 의미
FROM EMP E
LEFT OUTER JOIN DEPT D ON E.DEPTNO = D.DEPTNO
```

## 5. ROWS BETWEEN으로 계산 범위 지정하기

- 기록일: 2026-11-14
- 핵심: 현재 행의 계산에 포함할 **행의 범위**를 정한다. 앞뒤는 ORDER BY로 정렬된 순서를 기준으로 판단한다.

```sql
ROWS BETWEEN 시작점 AND 끝점
```

| 표현 | 의미 |
| --- | --- |
| `n PRECEDING` | 현재 행보다 n행 앞 |
| `CURRENT ROW` | 현재 행 |
| `n FOLLOWING` | 현재 행보다 n행 뒤 |
| `UNBOUNDED PRECEDING` | 파티션의 맨 처음 |
| `UNBOUNDED FOLLOWING` | 파티션의 맨 끝 |

### 자주 쓰는 조합

```sql
-- 바로 앞 1행 + 현재 행
ROWS BETWEEN 1 PRECEDING AND CURRENT ROW

-- 처음부터 현재까지: 누적 합계
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW

-- 앞 1행 + 현재 행 + 뒤 1행
ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING

-- 현재부터 맨 끝까지
ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING

-- 파티션 전체
ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
```

- **시작점과 끝점 모두 포함**한다.
- 앞뒤 행이 없으면 실제로 존재하는 행만 계산한다.
- PARTITION BY가 있으면 해당 파티션의 경계를 넘지 않는다. 없으면 전체 입력 행을 하나의 파티션으로 본다.

### 앞 1행과 누적 범위 구분

점수가 A=10, B=20, C=30, D=40 순서일 때 SUM 결과:

| 현재 행 | 앞 1행부터 현재까지 | 처음부터 현재까지 |
| --- | --- | --- |
| A | 10 | 10 |
| B | 30 | 30 |
| C | 50 | 60 |
| D | 70 | 100 |

> `1 PRECEDING`은 바로 앞 1행, `UNBOUNDED PRECEDING`은 파티션 맨 처음이다.

## 6. GROUPING SETS와 전체 총계 ()

- 기록일: 2026-11-14
- 핵심: **원하는 집계 기준을 괄호별로 나열**한다. 나열한 기준만 계산한다.

```sql
GROUP BY GROUPING SETS (
    (DEPT),      -- 부서별 합계
    (JOB),       -- 직무별 합계
    (DEPT, JOB), -- 부서와 직무를 함께 구분한 합계
    ()           -- 구분 없이 원본 전체 합계
)
```

- `(DEPT)`와 `(JOB)`는 각각 독립적인 집계 기준이다.
- `(DEPT, JOB)`는 **두 컬럼을 한 쌍의 기준으로** 묶는다. 부서와 직무가 모두 같은 행끼리 합산한다.
- `()`는 그룹을 나눌 컬럼 없이 원본 전체를 하나로 묶어 집계한다. SUM을 썼다면 전체 합계, COUNT를 썼다면 전체 행 수를 구한다.
- **전체 총계는 자동으로 추가되지 않는다.** 필요할 때 `()`를 직접 지정한다.
- 전체 총계는 원본 전체를 한 번 집계한 값이다. 부서별 소계와 직무별 소계를 다시 모두 더하는 것이 아니다.

### (DEPT, JOB) 예시

| DEPT | JOB | AMT |
| --- | --- | --- |
| A | 개발 | 100 |
| A | 개발 | 50 |
| A | 영업 | 200 |
| B | 개발 | 300 |

```sql
SELECT DEPT, JOB, SUM(AMT) AS TOTAL
FROM SALES
GROUP BY GROUPING SETS ((DEPT, JOB))
ORDER BY DEPT, JOB;
```

결과:

| DEPT | JOB | TOTAL |
| --- | --- | --- |
| A | 개발 | 150 |
| A | 영업 | 200 |
| B | 개발 | 300 |

- A·개발 두 행은 부서와 직무가 모두 같으므로 100 + 50 = 150으로 합쳐진다.
- A·영업은 부서는 같지만 직무가 달라 별도 그룹이다.
- B·개발은 직무는 같지만 부서가 달라 별도 그룹이다.
- 이 예시처럼 집계 기준이 `(DEPT, JOB)` 하나뿐이면 **`GROUP BY DEPT, JOB`과 같은 결과**이다. 부서 소계·직무 소계·전체 총계는 포함하지 않는다.

## 7. GROUPING 함수로 집계용 NULL 구분하기

- 기록일: 2026-11-14
- 점검 상태: 기본 문제의 0, 0, 1을 정답 도출. 원본 NULL 행과 총계 NULL 행의 변형 문제도 각각 0, 1로 정답 도출.
- 핵심: **GROUPING(컬럼)은 해당 행의 집계 기준에 그 컬럼이 포함되면 0, 제외되면 1**을 반환한다.

| 반환값 | 의미 |
| --- | --- |
| 0 | 해당 컬럼을 기준으로 나눈 그룹. 원본 데이터의 NULL 그룹도 포함 |
| 1 | 해당 컬럼을 집계 기준에서 제외한 소계·총계 행. 그 컬럼은 집계 표시용 NULL로 나타남 |

### 푼 문제

SALES 데이터가 (A, 100), (B, 200)일 때:

```sql
SELECT DEPT,
       SUM(AMT) AS TOTAL,
       GROUPING(DEPT) AS G
FROM SALES
GROUP BY ROLLUP(DEPT);
```

결과에 포함되는 행 (표시 순서는 별도로 보장되지 않음):

| DEPT | TOTAL | G |
| --- | --- | --- |
| A | 100 | 0 |
| B | 200 | 0 |
| NULL | 300 | 1 |

- A, B는 DEPT를 기준으로 집계한 그룹이므로 0이다.
- 전체 총계 300은 DEPT를 구분하지 않고 집계했으므로 1이다. 마지막 행의 DEPT는 새 부서명이 아니라 NULL이다.

### 최종 암기 문장

> NULL이라고 무조건 GROUPING 값이 1인 것은 아니다. 원본의 NULL 그룹은 0, 해당 컬럼을 집계 기준에서 제외하여 생긴 NULL은 1이다.

- [x] 기본 ROLLUP 결과에서 부서별 행과 총계 행의 GROUPING 값을 구분할 수 있다.
- [x] 원본 NULL과 집계용 NULL이 함께 있는 결과를 구분할 수 있다.

### GROUP BY에서 NULL 행들은 어떻게 묶이는가?

**GROUP BY DEPT는 DEPT 값이 NULL인 행들을 하나의 그룹으로 묶는다.** 일반 비교의 `NULL = NULL`이 UNKNOWN이라는 규칙과, 그룹을 나누는 규칙은 구분한다.

| DEPT | AMT |
| --- | --- |
| A | 100 |
| NULL | 50 |
| NULL | 70 |

```sql
SELECT DEPT, SUM(AMT) AS TOTAL
FROM SALES
GROUP BY DEPT;
```

결과에 포함되는 행:

| DEPT | TOTAL |
| --- | --- |
| A | 100 |
| NULL | 120 |

NULL인 두 행은 같은 그룹이므로 50 + 70 = 120이 된다. 그룹 기준인 DEPT의 NULL 때문에 해당 행 자체가 집계에서 빠지는 것은 아니다.

같은 데이터에 `GROUP BY ROLLUP(DEPT)`와 `GROUPING(DEPT)`를 적용하면:

| DEPT | TOTAL | GROUPING(DEPT) |
| --- | --- | --- |
| A | 100 | 0 |
| NULL | 120 | 0 |
| NULL | 220 | 1 |

- 120은 원본 DEPT가 NULL인 그룹의 합계이므로 GROUPING=0이다.
- 220은 DEPT 구분 없이 원본 전체를 더한 총계이므로 GROUPING=1이다.

## 8. CASE WHEN END와 조건부 집계

- 기록일: 2026-11-14
- 점검 상태: ELSE 0이 있는 문제와 ELSE를 생략한 변형 문제 모두 정답 도출.
- 핵심: **CASE가 행마다 값을 만들고, SUM이나 COUNT가 그 값을 집계한다.**

```sql
CASE
    WHEN 조건 THEN 조건이_TRUE일_때의_값
    ELSE 그_외의_값
END
```

- WHEN은 조건, THEN은 해당 조건이 TRUE일 때의 반환값, ELSE는 어느 조건도 TRUE가 아닐 때의 반환값이다.
- END는 CASE 표현식을 끝낸다.
- **ELSE를 생략하면 조건에 맞지 않을 때 NULL을 반환**한다.

### 푼 문제

| PRODUCT | GRADE |
| --- | --- |
| 사과 | A |
| 사과 | B |
| 사과 | A |
| 배 | B |

```sql
SELECT PRODUCT,
       SUM(CASE WHEN GRADE = 'A' THEN 1 ELSE 0 END) AS X,
       COUNT(CASE WHEN GRADE = 'A' THEN 1 ELSE 0 END) AS Y
FROM SALES
GROUP BY PRODUCT;
```

| 상품 | CASE가 반환한 값 | X: SUM | Y: COUNT |
| --- | --- | --- | --- |
| 사과 | 1, 0, 1 | 2 | 3 |
| 배 | 0 | 0 | 1 |

SUM은 숫자를 더하지만, **COUNT(표현식)는 NULL이 아닌 값의 개수를 세므로 0도 센다.**

### ELSE를 생략하면?

```sql
COUNT(CASE WHEN GRADE = 'A' THEN 1 END) AS Y
```

| 상품 | CASE가 반환한 값 | Y: COUNT |
| --- | --- | --- |
| 사과 | 1, NULL, 1 | 2 |
| 배 | NULL | 0 |

> 조건에 맞는 행만 세려면 `SUM(CASE WHEN 조건 THEN 1 ELSE 0 END)` 또는 `COUNT(CASE WHEN 조건 THEN 1 END)`를 사용한다. COUNT에 ELSE 0을 넣으면 조건에 맞지 않는 행도 세게 된다.

## 9. 날짜의 TRUNC·ROUND와 반올림 경계 (Oracle)

- 기록일: 2026-11-14
- 점검 상태: TRUNC는 정답 도출했으나 YEAR의 ROUND 결과를 틀림. YEAR·MONTH의 처리 규칙을 설명받았으며, 새로운 문제로 독립 확인은 아직 하지 않음.
- `SYSDATE`는 DB 서버의 현재 날짜와 시간을 반환한다.
- `TRUNC(날짜, 단위)`는 지정한 단위보다 작은 부분을 버려 **해당 단위의 시작 시점**으로 만든다.
- `ROUND(날짜, 단위)`는 정해진 경계를 기준으로 **현재 단위 또는 다음 단위의 시작 시점**으로 반올림한다.
- 아래 YEAR·MONTH 처리 결과의 시간은 모두 00:00:00이다. 반환값은 날짜(DATE)이다.

| 단위 | TRUNC | ROUND |
| --- | --- | --- |
| `'YEAR'` | 올해 1월 1일 | 7월 1일 전이면 올해 1월 1일, 7월 1일부터 다음 해 1월 1일 |
| `'MONTH'` | 이번 달 1일 | 1~15일이면 이번 달 1일, 16일부터 다음 달 1일 |

### 푼 문제와 정정

```sql
SELECT TRUNC(DATE '2025-08-20', 'YEAR') AS A,
       ROUND(DATE '2025-08-20', 'YEAR') AS B
FROM DUAL;
```

- 처음 답변: A, B 모두 2025-01-01.
- 올바른 결과: **A=2025-01-01, B=2026-01-01**.
- 8월 20일은 7월 1일 이후이므로 ROUND는 다음 해 시작으로 올린다.

### MONTH로 바꾸면?

```sql
SELECT TRUNC(DATE '2025-08-20', 'MONTH') AS A,
       ROUND(DATE '2025-08-20', 'MONTH') AS B
FROM DUAL;
```

- **A=2025-08-01, B=2025-09-01**.
- 20일은 16일 이후이므로 ROUND는 다음 달 시작으로 올린다.

### 최종 암기 문장

> TRUNC는 해당 단위의 시작으로, ROUND는 반올림 경계를 보고 현재 또는 다음 단위의 시작으로 간다. **YEAR의 경계는 7월 1일, MONTH의 경계는 16일**이다.

YEAR의 경계는 12개월을 상반기·하반기로 나누어 기억하면 쉽다. 정확한 동작은 Oracle이 정한 7월 1일 기준이며, 실제 일수의 정중앙을 매번 계산하는 방식은 아니다.

참고: [Oracle 날짜 ROUND·TRUNC의 처리 단위](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ROUND-and-TRUNC-Date-Functions.html)

## 10. NULL 처리 함수: NVL·COALESCE·NVL2·NULLIF

- 기록일: 2026-11-14
- 점검 상태: NVL·NVL2·NULLIF 문제는 정답 도출. COALESCE를 NULL로 답하여 설명 후 유사 문제 재확인 예정.

| 함수 | 의미 |
| --- | --- |
| `NVL(값, 대체값)` | 값이 NULL이면 대체값, 아니면 원래 값 반환 |
| `COALESCE(값1, 값2, ...)` | 왼쪽부터 처음으로 NULL이 아닌 값 반환. 모두 NULL이면 NULL |
| `NVL2(값, 결과1, 결과2)` | 값이 NULL이 아니면 결과1, NULL이면 결과2 반환 |
| `NULLIF(값1, 값2)` | 두 값이 같으면 NULL, 다르면 값1 반환 |

### 푼 문제와 정정

```sql
SELECT
    NVL(NULL, 100) AS A,
    COALESCE(NULL, NULL, 200, 300) AS B,
    NVL2(50, '있음', '없음') AS C,
    NULLIF(10, 10) AS D
FROM DUAL;
```

| 항목 | 처음 답변 | 올바른 결과 | 이유 |
| --- | --- | --- | --- |
| A | 100 | 100 | 첫 값이 NULL이므로 대체값 반환 |
| B | NULL | 200 | NULL 두 개를 지나 처음 만난 NULL 아닌 값은 200 |
| C | 있음 | 있음 | 50은 NULL이 아니므로 두 번째 인자 반환 |
| D | NULL | NULL | 두 값이 같으므로 NULL 반환 |

### 최종 암기 문장

> NVL은 NULL 대체, COALESCE는 처음 만나는 NULL 아닌 값, NVL2는 NULL 여부에 따른 분기, NULLIF는 두 값이 같으면 NULL.

- COALESCE는 앞에 NULL이 있어도 다음 값을 계속 확인한다.
- 숫자 0은 NULL이 아니다. `COALESCE(NULL, 0, 100)`은 0이다.
- NULLIF에서 두 값이 다르면 첫 번째 값을 반환한다. `NULLIF(10, 20)`은 10이다.

## 11. ORDER BY의 숫자는 SELECT 목록의 위치

- 기록일: 2026-11-14
- 점검 상태: ORDER BY 2의 의미를 설명받은 뒤, 확인 문제의 ORDER BY 1을 DEPTNO 기준 정렬로 정답 도출.
- 핵심: **일반 SELECT문의 ORDER BY에서 정수는 SELECT 목록의 몇 번째 항목인지 나타낸다. 1부터 센다.**

```sql
SELECT DEPTNO, AVG(SAL) AS AVG_SAL
--     1번째   2번째
FROM EMP
GROUP BY DEPTNO
ORDER BY 2 DESC;
```

| 표현 | 의미 |
| --- | --- |
| `ORDER BY 2 DESC` | 두 번째 항목인 평균 급여를 내림차순으로 정렬 |
| `ORDER BY 1 ASC` | 첫 번째 항목인 DEPTNO를 오름차순으로 정렬 |

위 쿼리에서 다음 세 표현은 같은 정렬이다.

```sql
ORDER BY 2 DESC
ORDER BY AVG_SAL DESC
ORDER BY AVG(SAL) DESC
```

> ORDER BY의 숫자는 행 번호나 컬럼 값이 아니라 **SELECT 목록의 위치**이다. ASC는 오름차순, DESC는 내림차순이며 방향을 생략하면 ASC이다.

## 12. OFFSET·FETCH로 조회 범위 제한하기

- 기록일: 2026-11-14
- `OFFSET n ROWS`: 정렬된 결과에서 **앞의 n행을 건너뛴다.**
- `FETCH FIRST m ROWS ONLY`: 건너뛴 뒤 남은 결과에서 **최대 m행을 가져온다.**
- 이해 순서: **ORDER BY로 정렬 → OFFSET으로 건너뛰기 → FETCH로 가져오기**.

예를 들어 급여 내림차순 결과가 `5000, 4000, 3000, 2000, 1000`일 때:

```sql
SELECT SAL
FROM EMP
ORDER BY SAL DESC
OFFSET 2 ROWS
FETCH FIRST 2 ROWS ONLY;
```

앞의 5000, 4000을 건너뛰고 **3000, 2000**을 반환한다.

- 건너뛴 뒤 남은 행이 없으면 **오류 없이 빈 결과(0행)**를 반환한다. 숫자 0을 출력하는 뜻이 아니다.
- FETCH 개수보다 남은 행이 적으면 남은 행만 가져온다.

## 13. Oracle 외부 조인 기호 (+)

- 기록일: 2026-11-14
- 핵심: **`(+)`가 붙은 쪽에 일치하는 행이 없어도, 반대쪽 행은 유지한다.** 일치하지 않는 쪽의 컬럼은 NULL로 채운다.

| 조건 | 같은 의미의 JOIN | 유지하는 쪽 |
| --- | --- | --- |
| `E.MANAGER_ID = M.ID(+)` | `E LEFT JOIN M ON E.MANAGER_ID = M.ID` | 직원 E |
| `E.MANAGER_ID(+) = M.ID` | `E RIGHT JOIN M ON E.MANAGER_ID = M.ID` | 관리자 역할의 M |

### 직원·관리자 셀프 조인 예시

| ID | NAME | MANAGER_ID |
| --- | --- | --- |
| 1 | 팀장 | NULL |
| 2 | 철수 | 1 |
| 3 | 영희 | 1 |

```sql
SELECT E.NAME, M.NAME AS MANAGER
FROM EMP E, EMP M
WHERE E.MANAGER_ID = M.ID(+);
```

E는 직원, M은 관리자 역할로 같은 EMP 테이블을 참조한다. 위 쿼리는 다음 LEFT JOIN과 같다.

```sql
SELECT E.NAME, M.NAME AS MANAGER
FROM EMP E
LEFT JOIN EMP M
  ON E.MANAGER_ID = M.ID;
```

결과에 포함되는 행:

| NAME | MANAGER |
| --- | --- |
| 팀장 | NULL |
| 철수 | 팀장 |
| 영희 | 팀장 |

팀장은 일치하는 관리자 M이 없어도 E의 행을 유지하므로 출력된다.

> **M에 `(+)` → M이 없어도 E 유지.** `(+)`가 붙은 쪽의 모든 행을 유지한다는 뜻으로 읽지 않는다.

## 14. BETWEEN과 범위 조인

- 기록일: 2026-11-14
- 점검 상태: 설명 후 급여 등급 문제에서 A=1, B=2, C=3으로 정답 도출.
- 핵심: **`값 BETWEEN 하한 AND 상한`은 하한 이상, 상한 이하이다. 양 끝값을 모두 포함한다.**

```sql
SAL BETWEEN 1000 AND 1999
-- 다음 조건과 같은 의미
SAL >= 1000 AND SAL <= 1999
```

### 급여 등급을 연결한 예제

EMP:

| NAME | SAL |
| --- | --- |
| A | 1500 |
| B | 2500 |
| C | 3000 |

SALGRADE:

| GRADE | LOSAL | HISAL |
| --- | --- | --- |
| 1 | 1000 | 1999 |
| 2 | 2000 | 2999 |
| 3 | 3000 | 3999 |

```sql
SELECT E.NAME, S.GRADE
FROM EMP E
JOIN SALGRADE S
  ON E.SAL BETWEEN S.LOSAL AND S.HISAL;
```

직원의 급여가 등급표의 하한~상한 사이에 들어가는 행을 연결한다. 등호 대신 범위 조건을 사용하는 **비등가 조인**이다.

| NAME | GRADE |
| --- | --- |
| A | 1 |
| B | 2 |
| C | 3 |

> C의 급여 3000은 3등급의 하한값과 같으므로 3등급에 포함된다.

## 15. FIRST_VALUE·LAST_VALUE의 기본 계산 범위 (Oracle)

- 기록일: 2026-11-14
- 점검 상태: 기본 범위 문제에서 모든 행이 (A, C)라고 답함. 설명 후 ‘현재부터 끝까지’ 범위의 B행은 (B, C)로 정답 도출.
- 핵심: **FIRST_VALUE와 LAST_VALUE는 각 행의 계산 범위 안에서 처음 값과 마지막 값을 반환한다.**
- ORDER BY를 쓰고 범위를 생략하면 기본값은 다음과 같다.

```sql
RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
```

파티션 처음부터 **현재 정렬 값과 같은 동점 행까지** 포함한다. 정렬 값이 유일한 이번 예시에서는 처음부터 현재 행까지이다.

### 기본 범위를 적용한 문제

| NAME | SAL |
| --- | --- |
| A | 100 |
| B | 200 |
| C | 300 |

```sql
SELECT NAME,
       FIRST_VALUE(NAME) OVER (ORDER BY SAL) AS F,
       LAST_VALUE(NAME) OVER (ORDER BY SAL) AS L
FROM EMP
ORDER BY SAL;
```

| 현재 행 | 계산 범위 | F | L |
| --- | --- | --- | --- |
| A | A | A | A |
| B | A → B | A | B |
| C | A → B → C | A | C |

### 전체 범위로 지정하면?

```sql
ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
```

모든 행에서 범위가 A → B → C 전체이므로 F=A, L=C이다.

### 현재부터 끝까지로 지정하면?

```sql
ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
```

| 현재 행 | 계산 범위 | F | L |
| --- | --- | --- | --- |
| A | A → B → C | A | C |
| B | B → C | B | C |
| C | C | C | C |

> LAST_VALUE가 항상 파티션 전체의 마지막 값인 것은 아니다. **OVER 안의 계산 범위부터 확인**한다. 전체의 마지막 값을 원하면 범위의 끝을 UNBOUNDED FOLLOWING으로 지정한다.

## 16. ROWS는 행 위치, RANGE는 정렬 값 기준

- 기록일: 2026-11-14
- 점검 상태: 첫 A행의 ROWS 합계와 RANGE 합계를 모두 100이라고 답함. 설명 후 기준의 차이를 확인했으며, 변형 문제는 아직 미응답.
- 핵심: **ROWS는 정렬된 행의 위치**, **RANGE는 ORDER BY의 정렬 값**을 기준으로 계산 범위를 정한다.

### 동점 행이 있는 누적 합계 예제

| NAME | SCORE |
| --- | --- |
| A | 100 |
| B | 100 |
| C | 200 |

```sql
SELECT NAME,
       SUM(SCORE) OVER (
           ORDER BY SCORE, NAME
           ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS X,
       SUM(SCORE) OVER (
           ORDER BY SCORE
           RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS Y
FROM SCORES
ORDER BY SCORE, NAME;
```

| 현재 행 | X: ROWS | Y: RANGE |
| --- | --- | --- |
| A | 100 | 200 |
| B | 200 | 200 |
| C | 400 | 400 |

- A행의 ROWS는 정렬된 첫 행인 A까지만 포함하므로 100이다. NAME을 추가 정렬 기준으로 두었으므로 동점에서는 A가 B보다 먼저 온다.
- A행의 RANGE는 현재 정렬 값인 **점수 100까지** 포함한다. A와 같은 점수인 B도 포함하므로 100 + 100 = 200이다.
- 여기서 RANGE의 ORDER BY는 SCORE 하나이므로 SCORE가 같으면 동점으로 본다.

### CURRENT ROW를 읽는 기준

| 구문 | 이 누적 범위에서의 의미 |
| --- | --- |
| `ROWS ... AND CURRENT ROW` | 처음부터 현재 행 위치까지 |
| `RANGE ... AND CURRENT ROW` | 처음부터 현재 정렬 값과 같은 동점 행까지 |

> **ROWS = 행 기준 / RANGE = 값 기준.** ‘현재까지’의 뜻이 행 위치인지 정렬 값인지 구분한다. 위 동점 포함 설명은 `UNBOUNDED PRECEDING AND CURRENT ROW` 범위에 대한 것이다.
