# 서로소 집합

## 수론에서의 서로소

두 수의 최대공약수가 1인 관계를 서로소라고 한다.

```text
GCD(a, b) = 1
```

이 개념과 아래의 **서로소 집합 자료구조(Disjoint Set)** 는 이름은 같지만 구분해서 생각한다.

---

## 서로소 집합(Disjoint Set)

서로 겹치지 않는 여러 집합을 효율적으로 관리하는 자료구조이다.

보통 **Union-Find**라고도 부른다.

### 핵심 연산

- `Make-Set`: 처음에는 각 원소가 자기 자신만 포함하는 집합을 만든다.
- `Find`: 특정 원소가 속한 집합의 대표 원소(root)를 찾는다.
- `Union`: 두 원소가 속한 집합을 하나로 합친다.

### 활용

- 두 정점이 같은 집합에 속하는지 확인
- 그래프의 사이클 판별
- [[Kruskal]] 알고리즘
- 연결 요소 관리

## 기본 트리 표현

```java
static int[] parent;

static void makeSet(int n) {
    parent = new int[n + 1];

    for (int i = 1; i <= n; i++) {
        parent[i] = i;
    }
}
```

처음에는 모든 원소가 자기 자신을 대표로 가진다.

## Find

```java
static int find(int x) {
    if (parent[x] == x) {
        return x;
    }

    return find(parent[x]);
}
```

부모를 계속 따라가 자기 자신을 부모로 가지는 루트를 찾는다.

## 경로 압축(Path Compression)

Union이 반복되면 트리가 길어질 수 있다.

```text
1
|
2
|
3
|
4
```

이 상태에서 `find(4)`를 할 때마다

```text
4 → 3 → 2 → 1
```

을 계속 따라가야 한다.

Find 과정에서 찾은 루트를 중간 노드의 부모로 바로 저장하면

```java
static int find(int x) {
    if (parent[x] == x) {
        return x;
    }

    return parent[x] = find(parent[x]);
}
```

한 번의 Find 이후 구조가 다음처럼 압축된다.

```text
    1
  / | \
 2  3  4
```

### 경로 압축의 목적과 효과

- Find 과정에서 지나간 노드들을 루트에 직접 연결
- 이후 Find가 확인해야 하는 경로를 짧게 만듦
- 반복되는 Find 연산의 성능을 크게 향상

## Union by Rank

두 트리를 합칠 때 높이가 낮은 트리를 높은 트리 아래에 붙이면 트리가 불필요하게 길어지는 것을 막을 수 있다.

```java
static int[] parent;
static int[] rank;

static void union(int a, int b) {
    int rootA = find(a);
    int rootB = find(b);

    if (rootA == rootB) {
        return;
    }

    if (rank[rootA] < rank[rootB]) {
        parent[rootA] = rootB;
    } else if (rank[rootA] > rank[rootB]) {
        parent[rootB] = rootA;
    } else {
        parent[rootB] = rootA;
        rank[rootA]++;
    }
}
```

경로 압축과 Union by Rank 또는 Union by Size를 함께 사용하면 Union-Find 연산은 평균적으로 거의 상수 시간에 가깝게 동작한다.

## 전체 구현

```java
public class TreeDisjointSet {
    static int[] parent;
    static int[] rank;

    public static void main(String[] args) {
        int n = 5;

        parent = new int[n + 1];
        rank = new int[n + 1];

        for (int i = 1; i <= n; i++) {
            parent[i] = i;
        }

        union(1, 2);
        union(2, 3);

        System.out.println(find(1));
        System.out.println(find(2));
        System.out.println(find(3));
        System.out.println(find(4));
    }

    static int find(int x) {
        if (parent[x] == x) {
            return x;
        }

        return parent[x] = find(parent[x]);
    }

    static void union(int a, int b) {
        int rootA = find(a);
        int rootB = find(b);

        if (rootA == rootB) {
            return;
        }

        if (rank[rootA] < rank[rootB]) {
            parent[rootA] = rootB;
        } else if (rank[rootA] > rank[rootB]) {
            parent[rootB] = rootA;
        } else {
            parent[rootB] = rootA;
            rank[rootA]++;
        }
    }
}
```

## 핵심

> Union-Find는 여러 집합을 합치고, 두 원소가 같은 집합에 속하는지 빠르게 확인하는 자료구조이다. 경로 압축은 Find 과정에서 트리 높이를 줄여 이후 탐색을 빠르게 만든다.

---

## 연결 리스트를 이용한 서로소 집합 구현 (참고)

트리 방식이 코딩 테스트에서는 더 일반적이지만, 서로소 집합을 연결 리스트로도 표현할 수 있다.

각 노드가 자신이 속한 집합의 대표 노드를 직접 가리키도록 하고, 두 집합을 합칠 때 작은 집합의 대표 정보를 큰 집합의 대표로 갱신한다.

```java
public class LinkedListDisjointSet {
    static class Node {
        int value;
        Node next;
        Node representative;

        Node(int value) {
            this.value = value;
            this.representative = this;
        }
    }

    static class SetList {
        Node head;
        Node tail;
        int size;

        SetList(int value) {
            Node node = new Node(value);
            head = node;
            tail = node;
            size = 1;
        }
    }

    static Node find(Node node) {
        return node.representative;
    }

    static void union(SetList set1, SetList set2) {
        if (set1.size < set2.size) {
            SetList temp = set1;
            set1 = set2;
            set2 = temp;
        }

        Node current = set2.head;

        while (current != null) {
            current.representative = set1.head;
            current = current.next;
        }

        set1.tail.next = set2.head;
        set1.tail = set2.tail;
        set1.size += set2.size;
    }
}
```
