Prim 알고리즘은
> 하나의 정점에서 시작하여 현재 만들어진 트리와 연결할 수 있는 가장 싼 간선을 반복해서 선택해 [[MST]]를 만드는 알고리즘

알고리즘 이름은 만든 사람의 이름을 따온 것

## 핵심 아이디어
Prim은 하나의 정점에서 시작해서 하나의 트리를 계속 확장한다.

```text
아무 정점 하나에서 시작
        ↓
현재 MST에 포함되지 않은 정점 중
가장 싸게 연결할 수 있는 정점 선택
        ↓
선택한 정점을 MST에 포함
        ↓
새롭게 들어온 정점을 기준으로
다른 정점들의 최소 연결 비용 갱신
        ↓
모든 정점이 포함되면 종료
```

[[Kruskal]]이 전체 간선을 대상으로 가장 싼 간선을 선택한다면

```text
Kruskal
→ 전체 그래프의 간선 중 가장 싼 간선을 선택

Prim
→ 현재 만들어진 트리에서 바깥으로 나가는 가장 싼 간선을 선택
```

이라고 볼 수 있다.

Prim에서는 현재 MST에 포함된 정점과 아직 포함되지 않은 정점을 구분하기 위해 보통 `visited`를 사용한다.

또 각 정점이 현재 MST와 연결되기 위해 필요한 최소 비용을 `minEdge`에 저장한다.

예를 들어

```text
minEdge[i]
```

는

> 현재 MST에 포함된 정점들 중 하나와 `i`번 정점을 연결할 수 있는 최소 비용

을 의미한다.

현재 MST가

```text
{0, 2, 4}
```

라면

```text
minEdge[3]
=
min(
    0 → 3 비용,
    2 → 3 비용,
    4 → 3 비용
)
```

이다.

## 구현 순서
1. 모든 정점의 `minEdge` 값을 무한대로 초기화한다.
2. 시작 정점의 `minEdge`를 `0`으로 설정한다.
3. 아직 MST에 포함되지 않은 정점 중 `minEdge` 값이 가장 작은 정점을 찾는다.
4. 해당 정점을 MST에 포함하고 비용을 누적한다.
5. 새로 들어온 정점을 기준으로 다른 정점들의 `minEdge`를 갱신한다.
6. 모든 정점이 MST에 포함될 때까지 반복한다.

## Cut Property
Prim 역시 [[그리디]] 알고리즘이므로

> 현재 가장 싼 간선을 선택하는 것이 왜 최적해를 보장하는가?

를 생각해야 한다.

현재까지 MST에 포함된 정점과 아직 포함되지 않은 정점을 두 그룹으로 나누면 하나의 Cut이 만들어진다.

```text
MST에 포함된 정점 | 아직 포함되지 않은 정점
```

Prim은 이 두 그룹 사이를 가로지르는 간선 중 가장 싼 간선을 선택한다.

Cut Property는 다음을 보장한다.

> 어떤 방식으로 정점을 두 그룹으로 나누더라도, 그 경계를 가로지르는 가장 싼 간선은 어떤 MST에 포함시킬 수 있다.

따라서 Prim은 현재 트리에서 바깥으로 나가는 가장 싼 간선을 반복해서 선택해도 최적해를 놓치지 않는다.

# 구현

아래는 인접 행렬을 이용한 `O(V²)` Prim 구현이다.

```java
import java.util.Arrays;

public class Main {

    static final long INF = Long.MAX_VALUE;

    public static void main(String[] args) {

        long[][] graph = {
            {0, 2, 3, 6},
            {2, 0, 1, 4},
            {3, 1, 0, 5},
            {6, 4, 5, 0}
        };

        int V = graph.length;

        boolean[] visited = new boolean[V];
        long[] minEdge = new long[V];

        Arrays.fill(minEdge, INF);

        // 0번 정점부터 시작
        minEdge[0] = 0;

        long totalCost = 0;

        for (int count = 0; count < V; count++) {

            int minVertex = -1;
            long minCost = INF;

            // 아직 MST에 포함되지 않은 정점 중
            // 가장 싸게 연결 가능한 정점 선택
            for (int i = 0; i < V; i++) {
                if (!visited[i] && minEdge[i] < minCost) {
                    minCost = minEdge[i];
                    minVertex = i;
                }
            }

            visited[minVertex] = true;
            totalCost += minCost;

            // 새로 들어온 정점을 이용해
            // 나머지 정점의 최소 연결 비용 갱신
            for (int next = 0; next < V; next++) {

                if (visited[next]) {
                    continue;
                }

                if (graph[minVertex][next] < minEdge[next]) {
                    minEdge[next] = graph[minVertex][next];
                }
            }
        }

        System.out.println(totalCost);
    }
}
```



## PriorityQueue를 이용한 Prim

기본 Prim에서는 아직 MST에 포함되지 않은 모든 정점을 순회하면서

minEdge 값이 가장 작은 정점을 직접 찾는다.
```
u ← Extract-MIN(...)
```
이 과정에서 매번 모든 정점을 탐색하면 `O(V)`가 필요하다.

최소값을 더 빠르게 찾기 위해 [[우선순위 큐]]를 사용할 수 있다.
>Prim + PriorityQueue는 현재 MST에서 밖으로 나가는 모든 후보 간선을 PQ에 모아두고, 그중 가장 싼 간선을 반복해서 선택하는 방식이다.
## PriorityQueue를 이용한 Prim 구현
```java
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;

public class Main{
	static class Edge implements Comparable<Edge>{
		int to;
		int cost;
		
		Edge(int to, int cost){
			this.to = to;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Edge other){
			return Integer.compare(this.cost, other.cost);
		}
	}
	
	static long prim(List<Edge>[] graph, int V){
		boolean[] visited = new boolean[V];
		
		PriorityQueue<Edge> pq = new PriorityQueue<>();
		
		// 0번 정점부터 시작
		pq.offer(new Edge(0,0));
		
		long totalCost = 0;
		int count = 0;
		
		while (!pq.isEmpty()){
			Edge current = pq.poll();
			
			// 이미 MST에 포함된 정점이면 무시
			if(visited[current.to]){
				continue;
			}
			
			visited[current.to] = true;
			totalCost += current.cost;
			count++;
			
			if(count == V){
				break;
			}
			
			// 새로 MST에 들어온 정점의 인접 간선 확인
			for(Edge next : graph[current.to]){
				if(!visited[next.to]){
					pq.offer(next);
				}
			}
		}
		
		// 그래프가 연결되어 있지 않아서 MST를 만들 수 없는 경우
		if (count != V) {
		    return -1;
		}
		
		return totalCost;
	}
	
	public static void main(String[] args){
		int V = 4;
		
		List<Edge>[] graph = new ArrayList[V];
		
		for(int i = 0; i<V; i++){
			graph[i] = new ArrayList<>();
		}
		
		// 무향 그래프이므로 양쪽에 모두 추가
		addEdge(graph, 0, 1, 2);
		addEdge(graph, 0, 2, 3);
		addEdge(graph, 0, 3, 6);
		addEdge(graph, 1, 2, 1);
		addEdge(graph, 1, 3, 4);
		addEdge(graph, 2, 3, 5);
		
		long result = prim(graph, V);
		
		System.out.println(result);
	}
	
    static void addEdge(
            List<Edge>[] graph,
            int from,
            int to,
            int cost
    ) {

        graph[from].add(new Edge(to, cost));
        graph[to].add(new Edge(from, cost));
    }

}
```


## 시간 복잡도

### 배열을 이용한 Prim

배열과 인접 행렬을 이용하면 매 단계마다

```text
가장 작은 minEdge를 가진 정점 탐색
→ O(V)

다른 정점의 minEdge 갱신
→ O(V)
```

를 수행하고 이를 `V`번 반복하므로

```text
O(V²)
```

이다.

### PriorityQueue를 이용한 Prim

최소 비용 간선을 찾는 `Extract-MIN` 연산을 [[우선순위 큐]]로 처리한다.

각 간선을 PriorityQueue에 삽입하고 꺼내는 과정이 필요하므로 일반적으로
```
O(E log V)
```
로 본다.

간선이 적은 희소 그래프에서는 배열 기반 `O(V²)` Prim보다 효율적일 수 있다.

## Prim 구현 비교

- **배열 기반 Prim `O(V²)`**
    - 모든 정점끼리 연결 가능하거나 간선이 매우 많음
    - 인접 행렬처럼 정점 쌍 비용을 바로 알 수 있음
    - 좌표만 주어지고 두 정점 사이 비용을 즉석 계산할 수 있음
    - 예: **SWEA 1251**
- **PriorityQueue 기반 Prim `O(E log V)`**
    - 간선이 상대적으로 적은 희소 그래프
    - 입력이 `A B C` 형태의 간선 목록으로 주어짐
    - 인접 리스트로 그래프를 만드는 게 자연스러움

## [[Kruskal]]과 비교

```text
Kruskal
→ 간선 중심
→ 전체 간선 중 가장 싼 간선을 선택
→ [[Union-Find]]로 사이클 판별
→ 여러 개의 작은 트리가 점점 합쳐짐

Prim
→ 정점 / 트리 중심
→ 현재 MST에서 바깥으로 나가는 가장 싼 간선을 선택
→ visited로 MST 포함 여부 관리
→ 하나의 트리가 점점 확장됨
```

## 대표 문제 SWEA 1251
하나로

각 섬의 좌표가 주어지고 모든 섬끼리 해저터널을 연결할 수 있다.

두 섬 사이의 환경 부담금은

```text
E × L²
```

이고

```text
L² = (x1 - x2)² + (y1 - y2)²
```

이므로 실제 거리를 구하기 위해 `sqrt`를 사용할 필요가 없다.

또한 `E`는 모든 간선에 동일하게 곱해지는 값이므로

```text
먼저 L²의 합이 최소가 되는 MST를 구한 뒤
마지막에 E를 한 번 곱해도 된다.
```

이 문제는 모든 섬 사이의 간선을 미리 생성하고 저장하지 않아도 된다.

좌표가 있으므로 새로운 섬이 MST에 들어올 때마다

```text
새로 들어온 섬 ↔ 아직 방문하지 않은 섬
```

의 거리를 직접 계산하여 `minEdge`를 갱신할 수 있다.

따라서 `O(N²)` Prim으로 구현하기 좋다.

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Solution {

    static long[] x;
    static long[] y;

    public static void main(String[] args) throws Exception {

        BufferedReader br = new BufferedReader(
                new InputStreamReader(System.in)
        );

        StringBuilder sb = new StringBuilder();

        int T = Integer.parseInt(br.readLine());

        for (int tc = 1; tc <= T; tc++) {

            int N = Integer.parseInt(br.readLine());

            x = new long[N];
            y = new long[N];

            StringTokenizer st = new StringTokenizer(br.readLine());

            for (int i = 0; i < N; i++) {
                x[i] = Long.parseLong(st.nextToken());
            }

            st = new StringTokenizer(br.readLine());

            for (int i = 0; i < N; i++) {
                y[i] = Long.parseLong(st.nextToken());
            }

            double E = Double.parseDouble(br.readLine());

            boolean[] visited = new boolean[N];
            long[] minEdge = new long[N];

            Arrays.fill(minEdge, Long.MAX_VALUE);

            // 0번 섬부터 시작
            minEdge[0] = 0;

            long totalDistanceSquared = 0;

            for (int count = 0; count < N; count++) {

                int minVertex = -1;
                long minCost = Long.MAX_VALUE;

                // 아직 MST에 포함되지 않은 섬 중
                // 가장 싸게 연결할 수 있는 섬 선택
                for (int i = 0; i < N; i++) {
                    if (!visited[i] && minEdge[i] < minCost) {
                        minCost = minEdge[i];
                        minVertex = i;
                    }
                }

                visited[minVertex] = true;
                totalDistanceSquared += minCost;

                // 새로 들어온 섬을 기준으로
                // 다른 섬들의 최소 연결 비용 갱신
                for (int next = 0; next < N; next++) {

                    if (visited[next]) {
                        continue;
                    }

                    long dx = x[minVertex] - x[next];
                    long dy = y[minVertex] - y[next];

                    long distanceSquared = dx * dx + dy * dy;

                    if (distanceSquared < minEdge[next]) {
                        minEdge[next] = distanceSquared;
                    }
                }
            }

            long answer = Math.round(totalDistanceSquared * E);

            sb.append("#")
              .append(tc)
              .append(" ")
              .append(answer)
              .append("\n");
        }

        System.out.print(sb);
    }
}
```
