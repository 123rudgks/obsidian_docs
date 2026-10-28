
Warshall이란 사람이 그래프에서 모든 쌍의 경로 존재 여부를 찾아내는 동적 계획 알고리즘을 제안 Floyd란 사람이 이를 변형하여 모든 쌍 최단 경로를 찾는 알고리즘을 고안했다.

## 개념
**플로이드-워셜(Floyd-Warshall)**은 그래프의 **모든 정점 쌍 사이의 최단 거리**를 구하는 알고리즘이다.

[[다익스트라]]가 하나의 시작 정점에서 다른 모든 정점까지의 최단 거리를 구하는 알고리즘이라면, 플로이드-워셜은 모든 정점을 시작점으로 보아 **모든 정점 → 모든 정점**의 최단 거리를 한 번에 계산한다.

플로이드-워셜은 [[동적 프로그래밍]]의 아이디어를 사용한다.

## 핵심 아이디어
현재 알고 있는 `i → j`의 최단 거리보다,
`i → k → j`
처럼 중간 정점 `k`를 거쳐 가는 경로가 더 짧다면 최단 거리를 갱신한다.

즉 다음 두 경로를 비교한다.
```
기존 경로
i → j

k를 경유하는 경로
i → k → j
```

갱신식은 다음과 같다.
```
dist[i][j]
=
min(
    dist[i][j],
    dist[i][k] + dist[k][j]
)
```

여기서

- `i` : 출발 정점
- `j` : 도착 정점
- `k` : 중간에 거쳐 가는 정점
을 의미한다.
## 로직
1. `dist[i][j]`에 현재 알고 있는 `i → j`의 거리를 저장한다.
2. 자기 자신으로 가는 거리는 `0`으로 초기화한다.
3. 직접 연결된 간선이 있다면 해당 간선의 가중치를 저장한다.
4. 직접 연결되지 않은 정점 사이는 `INF`로 초기화한다.
5. 정점 `k`를 하나씩 중간 경유지로 허용한다.
6. 모든 `i`, `j`에 대해 `i → j`와 `i → k → j`의 비용을 비교한다.
7. `k`를 거쳐 가는 것이 더 짧다면 `dist[i][j]`를 갱신한다.
8. 모든 정점을 경유지로 고려한 후 `dist[i][j]`에는 모든 정점 쌍의 최단 거리가 저장된다.

## 구현
```java
import java.io.*;
import java.util.*;

public class Main {

    static final long INF = Long.MAX_VALUE / 4;

    public static void main(String[] args) throws Exception {

        BufferedReader br =
                new BufferedReader(new InputStreamReader(System.in));

        StringTokenizer st =
                new StringTokenizer(br.readLine());

        int V = Integer.parseInt(st.nextToken());
        int E = Integer.parseInt(st.nextToken());

        long[][] dist = new long[V + 1][V + 1];

        // 거리 배열 초기화
        for (int i = 1; i <= V; i++) {
            Arrays.fill(dist[i], INF);
            dist[i][i] = 0;
        }

        // 간선 입력
        for (int i = 0; i < E; i++) {

            st = new StringTokenizer(br.readLine());

            int from = Integer.parseInt(st.nextToken());
            int to = Integer.parseInt(st.nextToken());
            long cost = Long.parseLong(st.nextToken());

            // 동일한 두 정점 사이에 여러 간선이 존재할 수 있으므로
            // 가장 작은 가중치를 저장한다.
            dist[from][to] = Math.min(dist[from][to], cost);

            // 무향 그래프라면 아래 코드 추가
            // dist[to][from] = Math.min(dist[to][from], cost);
        }

        // Floyd-Warshall
        for (int k = 1; k <= V; k++) {

            for (int i = 1; i <= V; i++) {

                for (int j = 1; j <= V; j++) {

                    // i → k 또는 k → j 경로가 없다면 갱신 불가능
                    if (dist[i][k] == INF || dist[k][j] == INF) {
                        continue;
                    }

                    dist[i][j] = Math.min(
                            dist[i][j],
                            dist[i][k] + dist[k][j]
                    );
                }
            }
        }

        // 결과 출력
        for (int i = 1; i <= V; i++) {

            for (int j = 1; j <= V; j++) {

                if (dist[i][j] == INF) {
                    System.out.print("INF ");
                } else {
                    System.out.print(dist[i][j] + " ");
                }
            }

            System.out.println();
        }
    }
}
```

## 시간 복잡도
정점 `k`, 출발점 `i`, 도착점 `j`를 모두 확인하므로 ```O(V³)```이다.
정점 수가 커질수록 연산량이 빠르게 증가하므로, 정점 수가 매우 큰 그래프에는 적합하지 않다.

### 공간복잡도
모든 정점 쌍의 거리를 저장하기 위해 `V × V` 배열을 사용하므로 ```O(V²)```이다.

## 특징
- 음수 가중치를 가진 간선도 처리할 수 있다. (단, **음수 사이클이 존재하면 정상적인 최단 거리가 정의되지 않는다**)
- 정점 수가 크면 `O(V³)`의 시간복잡도로 인해 사용하기 어렵다.

## 대표 문제
