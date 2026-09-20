## DFS 개념 코드로 구현
```java
import java.util.ArrayList; 
import java.util.HashSet; 
import java.util.List; 
import java.util.Set;

public class Solution{
	static class Vertex{
		int value;
		List<Vertext> adjacentVertices = new ArrayList<>();
		Vertex(int value){
			this.value = value;
		}
		
		void addAdjacentVertex(Vertex vertex){
			adjacentVertices.add(vertex);
		}
	}
	
	static Vertex dfs(Vertex vertex, int searchValue, Set<Integer> visitedVertices){
		// 현재 정점이 찾고 있던 정점이면 반환
		if(vertex.value == searchValue){
			return vertex;
		}
		
		// 현재 정점을 방문했다고 표시
		visitedVertices.add(vertex.value);
		
		// 현재 정점의 인접 정점들을 순회
		for(Vertex adjacentVertex : vertex.adjacentVertices){
			// 이미 방문했던 인접 정점은 무시
			if(visitedVertices.contains(adjacentVertex.value)){
				continue;
			}
			
			// 인접 정점이 찾고 있던 정점이면 반환
			if(adjacentVertex.value == searchValue){
				return adjacentVertex;
			}
			
			// 인접 정점에서 DFS를 계속 진행
			Vertex vertexWeAreSearchingFor = dfs(adjacentVertex,searchValue,visitedVertices);
			
			// 재귀 탐색에서 원하는 정점을 찾았다면 반환
			if(vertexWeAreSearchingFor != null){
				return vertexWeAreSearchingFor;
			}
		}
		// 끝까지 찾지 못했다면 null;
		return null;
	}
	
	public static void main(String[] args){
		Vertex v1 = new Vertex(1); 
		Vertex v2 = new Vertex(2); 
		Vertex v3 = new Vertex(3); 
		Vertex v4 = new Vertex(4); 
		Vertex v5 = new Vertex(5);
		v1.addAdjacentVertex(v2); 
		v1.addAdjacentVertex(v3); 
		v2.addAdjacentVertex(v4); 
		v3.addAdjacentVertex(v5);
		
		Set<Integer> visitedVertices = new HashSet<>();
		Vertex result = dfs( v1, 5, visitedVertices );
		
		if (result != null) { 
			System.out.println("찾은 정점: " + result.value); 
		} else { 
			System.out.println("정점을 찾지 못했습니다."); 
		}
	}
}
```

코드의 핵심 부분은 [[재귀]] 호출이다.
```java
Vertex vertexWeAreSearchingFor =
        dfs(adjacentVertex, searchValue, visitedVertices);
```

### 대표 문제 (연결 요소)개수 구하기
### 문제 — 연결된 영역의 개수

`N × N` 크기의 지도에서 `1`은 땅, `0`은 빈 공간이다.
상하좌우로 붙어 있는 `1`들은 하나의 영역으로 본다.  
지도에 존재하는 **서로 연결된 영역의 개수**를 구하시오.
예를 들어:
```
5
1 1 0 0 0
1 1 0 1 1
0 0 0 1 1
0 1 0 0 0
0 1 1 0 0
```

연결 관계를 보면:
```
① 영역

1 1
1 1
② 영역
      1 1
      1 1

③ 영역
  1
  1 1
```

따라서 출력은:
```
3
```

### 조건
- `1 ≤ N ≤ 100`
- 이동은 **상 / 하 / 좌 / 우**만 가능
- 대각선은 연결된 것으로 보지 않음
- 한 번 방문한 위치는 다시 방문하지 않음


```java
public class Solution {

    static int N;
    static int[][] map;
    static boolean[][] visited;

    // 상, 하, 좌, 우
    static int[] dx = { -1, 1, 0, 0 };
    static int[] dy = { 0, 0, -1, 1 };

    public static void main(String[] args) throws Exception {
        BufferedReader br = new BufferedReader(
                new InputStreamReader(System.in)
        );
        N = Integer.parseInt(br.readLine());
        map = new int[N][N];
        visited = new boolean[N][N];

        // 지도 입력
        for (int i = 0; i < N; i++) {
            StringTokenizer st = new StringTokenizer(br.readLine());
            for (int j = 0; i < N; i++) {
                map[i][j] = Integer.parseInt(st.nextToken());
            }
        }

        int count = 0;

        // 모든 칸 확인
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                // 땅이면서 아직 방문하지 않았다면
                if (map[i][j] == 1 && !visited[i][j]) {
                    count++;
                    dfs(i, j);
                }
            }
        }

        System.out.println(count);
    }

    static void dfs(int x, int y) {

        // 현재 위치 방문 처리
        visited[x][y] = true;
        // 상하좌우 탐색
        for (int d = 0; d < 4; d++) {
            int nx = x + dx[d];
            int ny = y + dy[d];
            
            // 지도 밖이면 무시
            if (nx < 0 || nx >= N || ny < 0 || ny >= N) {
                continue;
            }

            // 땅이고 아직 방문하지 않았다면 계속 DFS
            if (map[nx][ny] == 1 && !visited[nx][ny]) {
                dfs(nx, ny);
            }
        }
    }
}
```

