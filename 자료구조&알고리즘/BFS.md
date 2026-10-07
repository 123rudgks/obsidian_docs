# BFS

BFS(Breadth-First Search)는 **너비 우선 탐색**이다.

시작 정점에서 가까운 정점부터 레벨 단위로 탐색한다.

```text
시작점
→ 거리 1인 정점들
→ 거리 2인 정점들
→ 거리 3인 정점들
→ ...
```

핵심 자료구조는 **Queue**이다.

## 기본 흐름

1. 시작 정점을 방문 처리하고 Queue에 넣는다.
2. Queue에서 정점 하나를 꺼낸다.
3. 해당 정점의 인접 정점 중 방문하지 않은 정점을 방문 처리하고 Queue에 넣는다.
4. Queue가 빌 때까지 반복한다.

방문 처리는 보통 **Queue에 넣는 시점**에 한다.

## 인접 리스트 버전

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Queue;
import java.util.Set;

public class Solution {
    static class Vertex {
        int value;
        List<Vertex> adjacentVertices = new ArrayList<>();

        Vertex(int value) {
            this.value = value;
        }

        void addAdjacentVertex(Vertex vertex) {
            adjacentVertices.add(vertex);
        }
    }

    static void bfsTraverse(Vertex startingVertex) {
        Queue<Vertex> queue = new ArrayDeque<>();
        Set<Integer> visitedVertices = new HashSet<>();

        visitedVertices.add(startingVertex.value);
        queue.offer(startingVertex);

        while (!queue.isEmpty()) {
            Vertex currentVertex = queue.poll();
            System.out.println(currentVertex.value);

            for (Vertex adjacentVertex : currentVertex.adjacentVertices) {
                if (!visitedVertices.contains(adjacentVertex.value)) {
                    visitedVertices.add(adjacentVertex.value);
                    queue.offer(adjacentVertex);
                }
            }
        }
    }
}
```

## 인접 행렬 버전

```java
import java.util.ArrayDeque;
import java.util.Queue;

public class Main {
    static int V = 5;
    static int[][] graph = new int[V][V];
    static boolean[] visited = new boolean[V];

    static void bfs(int start) {
        Queue<Integer> queue = new ArrayDeque<>();

        visited[start] = true;
        queue.offer(start);

        while (!queue.isEmpty()) {
            int current = queue.poll();
            System.out.print(current + " ");

            for (int next = 0; next < V; next++) {
                if (graph[current][next] == 1 && !visited[next]) {
                    visited[next] = true;
                    queue.offer(next);
                }
            }
        }
    }

    static void addEdge(int a, int b) {
        graph[a][b] = 1;
        graph[b][a] = 1;
    }
}
```

## 시간 복잡도

- 인접 리스트: `O(V + E)`
- 인접 행렬: `O(V²)`

자세한 이유는 [[그래프#DFS / BFS 탐색 시간 복잡도]] 참고.

## 무가중치 그래프의 최단 거리

BFS는 시작점에서 거리 1, 거리 2, 거리 3 순서로 탐색한다.

따라서 모든 간선의 비용이 동일한 **무가중치 그래프**에서는 어떤 정점에 처음 도달했을 때의 거리가 최단 거리가 된다.

---

# BFS vs DFS 비교

| 구분 | BFS | [[DFS]] |
|---|---|---|
| 탐색 방식 | 가까운 정점부터 넓게 탐색 | 한 경로를 최대한 깊게 탐색 |
| 핵심 자료구조 | Queue | Stack / 재귀 호출 스택 |
| 방문 기록 | 필요 | 필요 |
| 무가중치 최단 거리 | 적합 | 일반적으로 보장하지 않음 |
| 대표 활용 | 최단 거리, 레벨 탐색, 확산 | 경로 탐색, 연결 요소, 백트래킹 |
| 인접 리스트 복잡도 | `O(V+E)` | `O(V+E)` |
| 인접 행렬 복잡도 | `O(V²)` | `O(V²)` |

## 서술형 핵심

> BFS는 Queue를 사용하여 시작 정점에서 가까운 정점부터 너비 방향으로 탐색한다. DFS는 Stack 또는 재귀 호출 스택을 사용하여 한 경로를 가능한 깊게 탐색한 뒤 되돌아온다. 두 방식 모두 이미 방문한 정점을 다시 탐색하지 않도록 visited 상태를 관리해야 한다. 인접 리스트를 사용하면 두 탐색 모두 `O(V+E)`, 인접 행렬을 사용하면 `O(V²)`의 시간이 걸린다. 무가중치 그래프의 최단 거리에는 BFS가 적합하다.





# 0-1 BFS

## 문제 설명

N × N 격자 형태의 서버실에서 `(1,1)`부터 `(N,N)`까지 이동한다.

- `1` : 통로, 비용 없이 이동 가능
- `0` : 보안 문, 통과하려면 해킹 1회 필요
- 상하좌우 이동 가능

목표는 목적지까지 이동하면서 필요한 **보안 문 해킹 횟수의 최솟값**을 구하는 것이다.

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Deque;

public class Main {
	static int N;
	static int[][] map;
	static int[][] dist;
	
	static int[] dr = {-1,1,0,0};
	static int[] dc = {0,0,-1,1};
	
	static class Node{
		int r;
		int c;
		
		Node(int r, int c){
			this.r = r;
			this.c = c;
		}
	}
	
	public static void main(String[] args) throws Exception{
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		N = Integer.parseInt(br.readLine());
		
		map = new int[N][N];
		dist = new int[N][N];
		
		// 지도 입력
		for(int r = 0; r < N; r++){
			String line = br.readLine();
			
			for(int c = 0; c < N; c++){
				map[r][c] = line.charAt(c) - '0';
			}
		}
		
		// 최소 해킹 횟수 초기화
		for(int r = 0; r < N; r++){
			Arrays.fill(dist[r], Integer.MAX_VALUE);
		}
		
		Deque<Node> deque = new ArrayDeque<>();
		
		// 시작점은 해킹 비용 없음
		dist[0][0] = 0;
		deque.offerFirst(new Node(0,0));
		
		while(!deque.isEmpty()){
			Node.current = deque.pollFirst();
			for(int d = 0; d < 4; d++){
				int nr = current.r + dr[d];
				int nc = current.c + dc[d];
				
				// 범위 밖
				if(nr < 0 || nr >= N || nc < 0 || nc >= N){
					continue;
				}
                /*
                 * 통로(1) → 비용 0
                 * 보안 문(0) → 비용 1
                 */
                 int weight;
                 
                 if(map[nr][nc] == 1){
	                 weight = 0;
                 }else{
	                 weight = 1;
                 }
                 
                 int newCost = dist[current.r][current.c] + weight;
                 
                 // 기존보다 더 적은 해킹 횟수로 갈 수 있는 경우
                 if(newCost < dist[nr][nc]){
	                 dist[nr][nc] = newCost;
	                 
	                 if(weight == 0){
		                 // 비용이 증가하지 않으므로 먼저 탐색
		                 deque.offerFirst(new Node(nr,nc));
	                 }else{
		                 deque.offerLast(new Node(nr,nc));
	                 }
                 }
			}
		}
		System.out.println(dist[N-1][N-1]);
	}
}
```