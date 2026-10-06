Kruskal 알고리즘은
> 간선을 가중치가 작은 순서대로 확인하면서 사이클을 만들지 않는 간선만 선택하여 [[MST]]를 만드는 알고리즘

알고리즘 이름은 만든 사람의 이름을 따온 것

## 핵심 아이디어
Kruskal은 다음과 같은 [[그리디]] 선택을 사용한다.
```
간선을 비용순으로 정렬
        ↓
가장 싼 간선부터 확인
        ↓
두 정점이 이미 같은 집합인가?
        ↓
YES → 선택하지 않음
NO  → 간선 선택 + 두 집합 합치기
        ↓
N - 1개의 간선을 선택하면 종료
```

Kruskal에서는 두 정점이 이미 연결되어 있는지 빠르게 확인하기 위해 보통 [[Union-Find]]를 사용한다.

두 정점 ```a, b```에 대해

```
find(a) == find(b)
```
이면 이미 같은 집합에 속한다.

따라서 두 정점을 연결하면 사이클이 생긴다.

반대로
```
find(a) != find(b)
```
이면 서로 다른 집합이므로 연결할 수 있다.

## 구현 순서
1. 모든 간선을 저장한다.
2. 간선을 가중치 기준 오름차순으로 정렬한다.
3. 각 정점을 자기 자신만 포함하는 집합으로 초기화한다.
4. 가장 싼 간선부터 확인한다.
5. 두 정점의 대표가 다르면 간선을 선택하고 ```union``` 한다.
6. ```N - 1``` 개의 간선을 선택하면 종료한다.

## Cut Property
무작정 싼 간선만 선택하면 최적해가 보장이 안될 수도 있지 않나?

Kruskal의 실제 논리는 다음과 같다.
> 현재 서로 분리된 두 영역을 연결해야 한다면,
> 그 두 영역을 잇는 간선 중 가장 싼 것을 선택해도 최적해를 잃지 않는다.

**Cut** : 그래프의 정점들을 두 그룹으로 나눈다고 생각하면 된다.

예시
```
{A, B} | {C, D}
```
위와 같이 두 그룹으로 나눈다.
이 두 집합 사이를 가로지르는 간선들이 있을 것이다.
```
A ----5---- C
B ----3---- C
B ----7---- D
```
이때 두 그룹을 연결하는 가장 싼 간선은
```
B - C : 3
```
이다.

Cut Property는 다음을 보장한다.
>어떤 방식으로 정점을 둘로 나누더라도, 그 경계를 가로지르는 가장 싼 간선은 어떤 MST에 포함시킬 수 있다.

이게 Kruskal의 그리디 선택이 안전한 이유다.

# 구현
```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	static class Edge implements Comparable<Edge> {
		int from;
		int to;
		int cost;
		
		Edge(int from, int to, int cost){
			this.from = from;
			this.to = to;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Edge other){
			return Integer.compare(this.cost, other.cost);
		}
	}
	
	static int[] parent;
	
	static int find(int x){
		if(parent[x] == x){
			return x;
		}
		return parent[x] = find(parent[x]);
	}
	
	static boolean union(int a, int b){
		int rootA = find(a);
		int rootB = find(b);
		
		if(rootA == rootB){
			return false;
		}
		
		parent[rootB] = rootA;
		return true;
	}
	
	public static void main(String[] args) throws Exception{
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());
		
		int V = Integer.parseInt(st.nextToken());
		int E = Integer.parseInt(st.nextToken());
		
		Edge[] edges = new Edges[E];
		
		for(int i = 0; i < E; i++){
			st = new StringTokenizer(br.readLine());
			
			int from = Integer.parseInt(st.nextToken());
			int to = Integer.parseInt(st.nextToken());
			int cost = Integer.parseInt(st.nextToken());
			
			edges[i] = new Edge(from, to, cost);
		}
		
		Arrays.sort(edges);
		
		parent = new int[V + 1];
		
		for(int i = 1; i<= V; i++){
			parent[i] = i;
		}
		
		long totalCost = 0;
		int selectedEdgeCount = 0;
		
		for(Edge edge : edges){
			if(!union(edge.from, edge.to)){
				continue;
			}
			totalCost += edge.cost;
			selectedEdgeCount++;
			
			if(selectedEdgeCount == V - 1){
				break;
			}
		}
		System.out.println(totalCost);
	}
}
```

## 시간 복잡도
간선의 개수를 ```E``` 라고 하면 가장 큰 비용은 간선 정렬
```
O(E log E)
```
[[[Union-Find]]의 ```find```, ```union``` 은 경로 압축 등을 사용하면 매우 빠르게 동작하므로 전체 시간 복잡도는 보통
```
O(E log E)
```

## 특징
- [[MST]]를 구하는 알고리즘
- [[그리디]] 알고리즘이다.
- 간선을 기준으로 선택한다.
- 사이클 판별을 위해 [[Union-Find]]를 함께 사용한다.
- 간선이 적은 희소 그래프에서 사용하기 좋은 편이다.

## 대표 문제 SWEA 3124
최소 스패닝 트리
```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Solution{
	static class Edge implements Comparable<Edge>{
		int from;
		int to;
		int cost;
		
		Edge(int from, int to, int cost){
			this.from = from;
			this.to = to;
			this.cost = cost;
		}
		
		@Override
		public int compareTo(Edge other){
			return Integer.compare(this.cost, other.cost);
		}
	}
	
	static int[] parent;
	
	// 대표 노드 찾기
	static int find(int x){
		if(parent[x] == x){
			return x;
		}
		
		// 경로 압축
		return parent[x] = find(parent[x]);
	}
	
	// 두 집합 합치기
	static boolean union(int a, int b){
		int rootA = find(a);
		int rootB = find(b);
		
		// 이미 같은 집합이면
		// 이 간선을 추가할 경우 사이클 발생
		if(rootA == rootB){
			return false;
		}
		
		parent[rootB] = rootA;
		
		return true;
	}
	
	public static void main(String[] args) throws Exception{
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		int T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++){
			StringTokenizer st = new StringTokenizer(br.readLine());
			
			int V = Integer.parseInt(st.nextToken());
			int E = Integer.parseInt(st.nextToken());
			
			Edge[] edges = new Edge[E];
			
			// 간선 입력
			for(int i = 0; i<E; i++){
				st = new StringTokenizer(br.readLine());
				
				int from = Integer.parseInt(st.nextToken());
				int to = Integer.parseInt(st.nextToken());
				int cost = Integer.parseInt(st.nextToken());
				
				edges[i] = new Edge(from, to, cost);
			}
			
			// 1. 간선 비용순 정렬
			Arrays.sort(edges);
			// 2. Union-Find 초기화
			parent = new int[V+1];
			
			for(int i =1; i<=V; i++){
				parent[i] = i;
			}
			
			long totalCost = 0;
			int selectedEdgeCost = 0;
			
			// 3. 가장 싼 간선부터 확인
			for(Edge edge : edges){
				// 두 정점이 서로 다른 집합이라면 선택
				if(union(edge.from, edge.to)){
					totalCost += edge.cost;
					selectedEdgeCount++;
					
					// MST는 V-1개의 간선을 가진다.
					if(selectedEdgeCount == V - 1){
						break;
					}
				}
			}
			System.out.println("#" + tc + " " + totalCost);
			
		}
	}
}
```
