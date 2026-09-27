# DFS

DFS(Depth-First Search)는 **깊이 우선 탐색**이다.

한 경로를 가능한 깊게 탐색한 뒤 더 이상 갈 곳이 없으면 이전 정점으로 돌아와 다른 경로를 탐색한다.

핵심 자료구조는 **Stack**이다.

재귀로 DFS를 구현하면 함수 호출 자체가 호출 스택에 쌓이므로 별도의 Stack을 만들지 않아도 된다.

BFS와의 전체 비교는 [[BFS#BFS vs DFS 비교]] 참고.

## 재귀 DFS — 인접 리스트

```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
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

    static Vertex dfs(Vertex vertex, int searchValue, Set<Integer> visitedVertices) {
        if (vertex.value == searchValue) {
            return vertex;
        }

        visitedVertices.add(vertex.value);

        for (Vertex adjacentVertex : vertex.adjacentVertices) {
            if (visitedVertices.contains(adjacentVertex.value)) {
                continue;
            }

            Vertex result = dfs(adjacentVertex, searchValue, visitedVertices);

            if (result != null) {
                return result;
            }
        }

        return null;
    }
}
```

## 재귀 DFS — 인접 행렬

```java
public class Main {
    static int V = 5;
    static int[][] graph = new int[V][V];
    static boolean[] visited = new boolean[V];

    static void dfs(int current) {
        visited[current] = true;
        System.out.print(current + " ");

        for (int next = 0; next < V; next++) {
            if (graph[current][next] == 1 && !visited[next]) {
                dfs(next);
            }
        }
    }

    static void addEdge(int a, int b) {
        graph[a][b] = 1;
        graph[b][a] = 1;
    }
}
```

## 반복문 + Stack으로 DFS

```java
import java.util.ArrayDeque;
import java.util.Deque;

static void dfs(int start) {
    Deque<Integer> stack = new ArrayDeque<>();
    boolean[] visited = new boolean[V];

    stack.push(start);

    while (!stack.isEmpty()) {
        int current = stack.pop();

        if (visited[current]) {
            continue;
        }

        visited[current] = true;
        System.out.print(current + " ");

        for (int next : graph[current]) {
            if (!visited[next]) {
                stack.push(next);
            }
        }
    }
}
```

## 시간 복잡도

- 인접 리스트: `O(V + E)`
- 인접 행렬: `O(V²)`

## 대표 문제 — 연결된 영역의 개수

`N × N` 지도에서 `1`은 땅, `0`은 빈 공간이고 상하좌우로 붙어 있는 `1`을 하나의 영역으로 본다고 하자.

모든 칸을 확인하면서 아직 방문하지 않은 땅을 발견할 때마다 DFS를 시작하면 연결 영역의 개수를 구할 수 있다.

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Solution {
    static int N;
    static int[][] map;
    static boolean[][] visited;

    static int[] dx = {-1, 1, 0, 0};
    static int[] dy = {0, 0, -1, 1};

    public static void main(String[] args) throws Exception {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

        N = Integer.parseInt(br.readLine());
        map = new int[N][N];
        visited = new boolean[N][N];

        for (int i = 0; i < N; i++) {
            StringTokenizer st = new StringTokenizer(br.readLine());

            for (int j = 0; j < N; j++) {
                map[i][j] = Integer.parseInt(st.nextToken());
            }
        }

        int count = 0;

        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                if (map[i][j] == 1 && !visited[i][j]) {
                    count++;
                    dfs(i, j);
                }
            }
        }

        System.out.println(count);
    }

    static void dfs(int x, int y) {
        visited[x][y] = true;

        for (int d = 0; d < 4; d++) {
            int nx = x + dx[d];
            int ny = y + dy[d];

            if (nx < 0 || nx >= N || ny < 0 || ny >= N) {
                continue;
            }

            if (map[nx][ny] == 1 && !visited[nx][ny]) {
                dfs(nx, ny);
            }
        }
    }
}
```

## 핵심

> DFS = 한 경로를 깊게 탐색 → 막히면 되돌아옴 → Stack 또는 재귀를 사용한다.
