너비 우선 탐색
[[DFS]]와 달리 너비 우선 탐색은 재귀를 쓰지 않는다. 대신 [[큐]]로 문제를 해결한다.

### BFS 개념 코드로 구현 (인접리스트 버전)
```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Queue;
import java.util.Set;

public class Solution{
	static class Vertex{
		int value;
		List<Vertex> adjacentVertices = new ArrayList<>();
		
		Vertex(int value){
			this.value = value;
		}
		
		void addAdjacentVertex(Vertex vertex){
			adjacentVertices.add(vertex);
		}
	}
	
	static void bfsTraverse(Vertex vertex){
		// BFS에서 사용할 큐
		Queue<Vertex> queue = new ArrayDeque<>();
		// 방문한 정점 기록
		Set<Integer> visitedVertices = new HashSet<>();
		// 시작 정점 방문 처리
		visitedVertices.add(startingVertex.value);
		// 시작 정점을 큐에 삽입
		queue.offer(startingVertex);
		// 큐가 빌 때까지 반복
		while(!queue.isEmpty()){
			// 큐의 가장 앞 정점을 꺼냄
			Vertex currentVertex = queue.poll();
			// 현재 정점 출력
			System.out.println(currentVertex.value);
			// 현재 정점과 연결된 모든 인접 정점 확인
			for(Vertex adjacentVertex : currentVertex.adjacentVertices){
				// 아직 방문하지 않은 정점이라면
				if(!visitedVertices.contains(adjacentVertex.value)){
					// 방문 처리
					visitedVertices.add(adjacentVertex.value);
					// 큐에 추가
					queue.offer(adjacentVertex);
				}
			}
		}
	}
	public static void main(String[] args){
		Vertex v1 = new Vertex(1);
		Vertex v2 = new Vertex(2); 
		Vertex v3 = new Vertex(3); 
		Vertex v4 = new Vertex(4); 
		Vertex v5 = new Vertex(5); 
		Vertex v6 = new Vertex(6);
		
		v1.addAdjacentVertex(v2); 
		v1.addAdjacentVertex(v3); 
		v2.addAdjacentVertex(v4); 
		v2.addAdjacentVertex(v5); 
		v3.addAdjacentVertex(v6);
		bfsTraverse(v1);
	}
}
```

> BFS와 DFS 중 어느 것을 사용해야 할 지 결정할 때 그래프를 탐색하는 동안 시작 정점에 가까이 있고 싶은지 아니면 무조건 멀리 떨어지고 싶은지로 구분 할 수 있다.


### BFS 개념 코드로 구현 (인접행렬 버전)
```java
import java.util.*;

public class Main{
	static inv V = 5;
	static int[][] graph = new int[V][V];
	static boolean[] visited = new boolean[V];
	
	static void bfs(int start){
		Queue<Integer> queue = new ArrayDeque<>();
		visited[start] = true;
		queue.offer(start);
		
		while(!queue.isEmpty()){
			int current = queue.poll();
			System.out.println(current + " ");
			
			// current와 연결된 모든 정점 확인
			for(int next = 0; next < V; next++){
				// 연결 되어있지만 방문 전
				if(graph[current][next] == 1 && !visited[next]){
					visited[next] = true;
					queue.offer(next);
				}
			}
		}
	}
	
	static void addEdge(int a , int b){
		graph[a][b] = 1;
		graph[b][a] = 1;
	}
	public static void main(String[] args){
		// 무방향 그래프 예시 
		addEdge(0, 1); 
		addEdge(0, 2); 
		addEdge(1, 3);
		addEdge(1, 4); 
		addEdge(2, 4); 
		bfs(0);
	}
}
```