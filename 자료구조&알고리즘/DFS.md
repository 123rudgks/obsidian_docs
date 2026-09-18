
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