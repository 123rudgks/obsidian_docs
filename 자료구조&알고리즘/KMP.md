>[!note] 문자열 매칭 알고리즘은 실제 코테에서는 흔히 나오지 않으니 로직만 이해하고 차후 필요시 상세히 공부하는 것으로 하자.

## 개념

**KMP(Knuth-Morris-Pratt)**는 문자열에서 특정 패턴을 찾기 위한 **문자열 검색 알고리즘**이다.

문자열 비교 도중 불일치가 발생했을 때, 지금까지 일치했던 정보를 버리지 않고 재사용하여 불필요한 비교를 건너뛰는 것이 핵심이다.

## LPS 배열

KMP에서 중요한 개념은 **LPS(Longest Prefix Suffix) 배열**이다.

LPS 배열은 패턴의 각 위치까지 봤을 때, **접두사이면서 동시에 접미사인 문자열 중 가장 긴 길이를 저장한다.**

예를 들어 패턴이

```text
ABABAC
```

이라면

```text
Pattern : A B A B A C
LPS     : 0 0 1 2 3 0
```

가 된다.

`ABABA`까지 보면

```text
ABABA
^^^
ABA   ← 접두사

ABABA
  ^^^
  ABA ← 접미사
```

이므로 길이 `3`의 문자열을 재사용할 수 있다.

즉 비교 도중 실패하더라도 처음부터 다시 비교하지 않고,

```text
현재까지 일치한 구간
→ 접두사 = 접미사인 부분 확인
→ 이미 일치가 보장된 부분은 다시 비교하지 않음
```

방식으로 다음 비교 위치를 정할 수 있다.

## 동작 과정

1. 패턴 문자열을 이용해 LPS 배열을 만든다.
2. Text와 Pattern을 앞에서부터 비교한다.
3. 문자가 같으면 Text와 Pattern의 인덱스를 모두 한 칸 이동한다.
4. 문자가 다르면 지금까지 일치한 패턴 구간의 LPS 값을 확인한다.
5. 재사용 가능한 접두사 = 접미사 구간이 있다면 Pattern의 비교 위치를 해당 길이만큼 되돌린다.
6. 재사용 가능한 부분이 없다면 Pattern을 처음부터 비교한다.
7. 이때 Text의 인덱스는 뒤로 돌아가지 않는다.
8. 패턴 전체가 일치하면 패턴이 등장한 것으로 판단하고, 중첩된 패턴을 찾기 위해 LPS 값을 이용해 다음 비교를 이어간다.

> [!note] KMP의 핵심
> 비교 실패 시 Text를 뒤로 돌리지 않고, LPS 배열을 이용해 Pattern에서 다시 비교할 위치를 결정한다.

## 시간 복잡도

- `N` : 원본 문자열의 길이
- `M` : 패턴 문자열의 길이

### LPS 배열 생성

패턴을 한 번 순회하므로

```text
O(M)
```

### 문자열 검색

Text의 인덱스가 뒤로 돌아가지 않으므로

```text
O(N)
```

### 전체

```text
O(N + M)
```

이다.

라빈-카프와 달리 해시 충돌이 없기 때문에 최악의 경우에도 `O(N + M)`을 보장한다.

## 대표 문제

### SWEA 4038. 단어가 등장하는 횟수

#### 문제 핵심

책의 내용 `B`에서 특정 단어 `S`가 몇 번 등장하는지 구한다.

문자열이 등장하는 위치는 서로 중첩될 수 있다.

```text
B = ABABA
S = ABA

0~2 : ABA
2~4 : ABA

정답 = 2
```

단순 문자열 검색으로 모든 위치에서 `S` 전체를 다시 비교하면 비효율적이므로 [[KMP]]의 [[LPS 배열]]을 이용한다.

#### 핵심 로직

1. 패턴 `S`의 LPS 배열을 만든다.
2. `B`와 `S`를 앞에서부터 비교한다.
3. 문자가 같으면 두 인덱스를 모두 증가시킨다.
4. 문자가 다르면 LPS 배열을 이용해 Pattern의 비교 위치를 이동한다.
5. Pattern 전체가 일치하면 등장 횟수를 증가시킨다.
6. 중첩된 문자열도 찾기 위해 Pattern 인덱스를 `lps[M - 1]`로 이동시킨다.
7. Text 끝까지 반복한다.

#### Java 구현

```java
import java.io.*;

public class Solution {

    public static void main(String[] args) throws Exception {

        BufferedReader br =
                new BufferedReader(new InputStreamReader(System.in));

        StringBuilder sb = new StringBuilder();

        int T = Integer.parseInt(br.readLine());

        for (int tc = 1; tc <= T; tc++) {

            String book = br.readLine();
            String pattern = br.readLine();

            int answer = kmp(book, pattern);

            sb.append("#")
              .append(tc)
              .append(" ")
              .append(answer)
              .append("\n");
        }

        System.out.print(sb);
    }

    static int kmp(String text, String pattern) {

        int N = text.length();
        int M = pattern.length();

        int[] lps = makeLps(pattern);

        int i = 0;
        int j = 0;
        int count = 0;

        while (i < N) {

            if (text.charAt(i) == pattern.charAt(j)) {

                i++;
                j++;

                if (j == M) {
                    count++;
                    j = lps[j - 1];
                }

            } else {

                if (j > 0) {
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }

        return count;
    }

    static int[] makeLps(String pattern) {

        int M = pattern.length();
        int[] lps = new int[M];

        int len = 0;
        int i = 1;

        while (i < M) {

            if (pattern.charAt(i) == pattern.charAt(len)) {

                len++;
                lps[i] = len;
                i++;

            } else {

                if (len > 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }

        return lps;
    }
}
```

#### 핵심 코드

```java
if (text.charAt(i) == pattern.charAt(j)) {
    i++;
    j++;
} else {
    if (j > 0) {
        j = lps[j - 1];
    } else {
        i++;
    }
}
```

문자가 일치하면 Text와 Pattern을 함께 이동한다.

문자가 일치하지 않을 경우,

```text
이전에 일치한 부분이 있음
→ LPS를 이용해 Pattern만 이동

이전에 일치한 부분이 없음
→ Text만 한 칸 이동
```

한다.

즉 Text의 인덱스는 뒤로 돌아가지 않는다.

패턴 전체가 일치한 경우에는

```java
j = lps[j - 1];
```

로 이동하여 중첩된 패턴도 계속 찾는다.

예를 들어

```text
Text    = ABABA
Pattern = ABA
```

에서 첫 번째 `ABA`를 찾은 뒤 `j = 1`로 이동하여 뒤쪽 `A`를 재사용하므로 두 번째 `ABA`도 찾을 수 있다.

> [!note] LPS의 의미
> `lps[i]`는 Pattern의 `0 ~ i` 구간에서 접두사이면서 접미사인 문자열 중 가장 긴 길이를 의미한다.
> 불일치가 발생했을 때 이 값을 이용해 이미 일치가 보장된 부분의 재비교를 건너뛴다.

#### 시간복잡도

LPS 배열 생성에

```text
O(M)
```

문자열 검색에

```text
O(N)
```

이 필요하므로 전체 시간복잡도는

```text
O(N + M)
```

이다.
