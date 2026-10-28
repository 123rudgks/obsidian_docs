>[!note] 문자열 매칭 알고리즘은 실제 코테에서는 흔히 나오지 않으니 로직만 이해하고 차후 필요시 상세히 공부하는 것으로 하자.
## 개념

**라빈-카프(Rabin-Karp)**는 문자열에서 특정 패턴을 찾기 위한 **문자열 검색 알고리즘**이다.

문자열을 직접 한 글자씩 계속 비교하는 대신, 문자열 구간을 **해시(Hash) 값으로 변환하여 비교**하는 것이 핵심이다.

## 롤링 해시 (Rolling Hash)

라빈-카프에서 중요한 개념은 롤링 해시이다.

예를 들어 현재 확인 중인 문자열 구간이
```
ABC
```
이고 다음 구간이
```
BCD
```
라고 하자.
매번 `BCD`의 해시 값을 처음부터 다시 계산하면 효율성이 떨어진다.

따라서 기존 `ABC`의 해시 값을 이용해서
```
A 제거
D 추가
```
만 수행하여 `BCD`의 해시 값을 계산한다.
즉
```
ABC → BCD → CDE → DEF
```
처럼 검색 구간을 한 칸씩 이동할 때 기존 해시 값을 재사용하여 새로운 해시 값을 빠르게 계산한다.
이 방식을 **롤링 해시(Rolling Hash)**라고 한다.
## 동작 과정

1. 찾으려는 패턴 문자열의 해시 값을 계산한다.
2. 원본 문자열에서 패턴과 같은 길이의 첫 구간의 해시 값을 계산한다.
3. 현재 구간의 해시 값과 패턴의 해시 값을 비교한다.
4. 해시 값이 다르면 해당 위치는 패턴이 아니므로 바로 다음 구간으로 이동한다.
5. 해시 값이 같다면 실제 문자열도 같은지 확인한다.
6. 검색 구간을 한 칸 이동하면서 롤링 해시를 이용해 다음 구간의 해시 값을 계산한다.
7. 문자열 끝까지 같은 과정을 반복한다.

>[!note] 해시 충돌
> 서로 다른 문자열이 같은 해시 값을 가질 수 있다.
> 따라서 해시 값이 같더라도 실제 문자열이 같은지 한 번 더 확인해야 한다.

## 시간 복잡도

- `N` : 원본 문자열의 길이
- `M` : 패턴 문자열의 길이
### 최악의 경우
해시 충돌이 매우 많이 발생하면 해시 값이 같을 때마다 실제 문자열을 다시 비교해야 한다.
이 경우 최악의 시간복잡도는
```
O(N × M)
```
까지 증가할 수 있다.

### 평균적인 경우
롤링 해시를 사용하면 각 위치에서 새로운 해시 값을 빠르게 계산할 수 있으므로 평균적으로
```
O(N + M)
```

수준으로 동작할 수 있다.


## 대표 문제

### SWEA 4038. 단어가 등장하는 횟수

#### 문제 핵심

책의 내용 `B`에서 특정 단어 `S`가 몇 번 등장하는지 구한다.
문자열이 등장하는 위치는 서로 중첩될 수 있다.

```
B = ABABA
S = ABA

0~2 : ABA
2~4 : ABA

정답 = 2
```

단순 문자열 검색으로 모든 위치에서 `S` 전체를 비교하면 비효율적이므로 [[Rabin-Karp]]의 [[롤링 해시]]를 이용한다.

#### 핵심 로직

1. 패턴 `S`의 해시 값을 계산한다.
2. `B`에서 `S`와 같은 길이의 첫 구간에 대한 해시 값을 계산한다.
3. 두 해시 값이 같으면 실제 문자열도 같은지 확인한다.
4. 검색 구간을 한 칸 오른쪽으로 이동한다.
5. 기존 해시에서 맨 앞 문자의 영향을 제거하고 새로운 문자를 추가하여 다음 해시를 계산한다.
6. 문자열 끝까지 반복하면서 일치 횟수를 센다.

#### Java 구현

```java
import java.io.*;

public class Solution {

    static final long BASE = 257;
    static final long MOD = 1_000_000_007L;

    public static void main(String[] args) throws Exception {

        BufferedReader br =
                new BufferedReader(new InputStreamReader(System.in));

        StringBuilder sb = new StringBuilder();

        int T = Integer.parseInt(br.readLine());

        for (int tc = 1; tc <= T; tc++) {

            String book = br.readLine();
            String pattern = br.readLine();

            int answer = rabinKarp(book, pattern);

            sb.append("#")
              .append(tc)
              .append(" ")
              .append(answer)
              .append("\n");
        }

        System.out.print(sb);
    }

    static int rabinKarp(String book, String pattern) {

        int N = book.length();
        int M = pattern.length();

        if (M > N) {
            return 0;
        }

        long patternHash = 0;
        long windowHash = 0;

        // BASE^(M-1)
        long highestPower = 1;

        for (int i = 0; i < M - 1; i++) {
            highestPower = (highestPower * BASE) % MOD;
        }

        // 패턴과 첫 번째 구간의 해시 계산
        for (int i = 0; i < M; i++) {

            patternHash =
                    (patternHash * BASE + pattern.charAt(i)) % MOD;

            windowHash =
                    (windowHash * BASE + book.charAt(i)) % MOD;
        }

        int count = 0;

        for (int start = 0; start <= N - M; start++) {

            // 해시가 같다면 실제 문자열 비교
            if (patternHash == windowHash) {

                if (book.regionMatches(start, pattern, 0, M)) {
                    count++;
                }
            }

            // 마지막 구간이면 다음 해시를 만들 필요 없음
            if (start == N - M) {
                break;
            }

            // 현재 구간의 첫 문자 제거
            long remove =
                    (book.charAt(start) * highestPower) % MOD;

            windowHash =
                    (windowHash - remove + MOD) % MOD;

            // 한 자리 이동 후 새로운 문자 추가
            windowHash =
                    (windowHash * BASE
                    + book.charAt(start + M)) % MOD;
        }

        return count;
    }
}
```

#### 핵심 코드

```
long remove =
        (book.charAt(start) * highestPower) % MOD;

windowHash =
        (windowHash - remove + MOD) % MOD;

windowHash =
        (windowHash * BASE
        + book.charAt(start + M)) % MOD;
```

현재 구간의 해시를 처음부터 다시 계산하지 않고,

```
기존 해시
→ 맨 앞 문자 제거
→ 한 자리 이동
→ 새로운 문자 추가
```

방식으로 다음 구간의 해시를 구한다.

이것이 [[롤링 해시]]의 핵심이다.

> [!note] 해시 충돌  
> 서로 다른 문자열의 해시 값이 같을 수 있으므로, 해시가 같은 경우 `regionMatches()`로 실제 문자열도 같은지 확인한다.

#### 시간복잡도

롤링 해시를 이용하면 각 구간의 다음 해시 값을 `O(1)`에 갱신할 수 있으므로 평균적인 탐색 비용은

```
O(N + M)
```

이다.

단, 해시 충돌이 반복되어 실제 문자열 비교가 많이 발생하는 최악의 경우에는 `O(N × M)`까지 증가할 수 있다.