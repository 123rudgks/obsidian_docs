

[[정렬]]

## 개념
**Insertion Sort(삽입 정렬)**는 배열의 앞쪽 부분이 이미 정렬되어 있다고 보고,  
**현재 값을 정렬된 구간의 알맞은 위치에 끼워 넣으면서 정렬 범위를 한 칸씩 확장하는 알고리즘**이다.

> Insertion = 삽입

예를 들어:
```text
[5, 3, 8, 4, 2]
```

에서 `3`을 처리할 때는 앞쪽의 `[5]`가 이미 정렬되어 있다고 보고,  
`3`이 들어갈 위치를 찾아 삽입한다.
```text
[5, 3, 8, 4, 2]
     ↑

3을 임시로 저장

[5, _, 8, 4, 2]

5 > 3
→ 5를 오른쪽으로 이동

[_, 5, 8, 4, 2]

3 삽입

[3, 5, 8, 4, 2]
```

이후에도 같은 과정을 반복한다.
```text
[3, 5, 8, 4, 2]
         ↑

8을 정렬된 [3, 5] 안에 삽입
→ 이미 알맞은 위치

[3, 5, 8, 4, 2]
```

```text
[3, 5, 8, 4, 2]
            ↑

4를 정렬된 [3, 5, 8] 안에 삽입

8 → 오른쪽 이동
5 → 오른쪽 이동
3 < 4 → 이동 종료

[3, 4, 5, 8, 2]
```

---

## 로직

Insertion Sort는 매 단계마다 배열을 다음 두 영역으로 생각한다.

```text
[ 정렬 완료된 구간 | 아직 정렬하지 않은 구간 ]
```

처음에는 첫 번째 원소 하나만 정렬된 상태라고 본다.
```text
[5 | 3, 8, 4, 2]
```

그 다음 원소 `3`을 정렬된 구간에 삽입한다.
```text
[3, 5 | 8, 4, 2]
```

그 다음 `8`을 삽입한다.
```text
[3, 5, 8 | 4, 2]
```

이 과정을 마지막 원소까지 반복하면 전체 배열이 정렬된다.

---

## 삽입 과정

현재 삽입할 값을 `key`라고 하자.

예를 들어:

```text
[2, 4, 7, 9, 5]
             ↑
            key
```

앞쪽:

```text
[2, 4, 7, 9]
```

는 이미 정렬되어 있다.

`key = 5`를 임시 변수에 저장한 뒤 왼쪽 값과 비교한다.

```text
9 > 5
→ 9를 오른쪽으로 이동

[2, 4, 7, _, 9]
```

```text
7 > 5
→ 7을 오른쪽으로 이동

[2, 4, _, 7, 9]
```

```text
4 < 5
→ 이동 종료
```

빈 위치에 `5`를 삽입한다.

```text
[2, 4, 5, 7, 9]
```

즉 삽입 정렬은 다음 과정을 반복한다.

1. 현재 값을 `key`에 임시 저장한다.
2. 왼쪽의 정렬된 구간을 뒤에서부터 확인한다.
3. `key`보다 큰 값을 오른쪽으로 한 칸씩 이동시킨다.
4. `key`보다 작거나 같은 값을 만나면 이동을 멈춘다.
5. 만들어진 빈 위치에 `key`를 삽입한다.

---

## Java 구현

```java
public class Main {

    public static void insertionSort(int[] arr) {

        for (int i = 1; i < arr.length; i++) {

            int key = arr[i];
            int j = i - 1;

            // key보다 큰 값을 오른쪽으로 한 칸씩 이동
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j--;
            }

            // key가 들어갈 위치에 삽입
            arr[j + 1] = key;
        }
    }

    public static void main(String[] args) {

        int[] numbers = {5, 3, 8, 4, 2};

        insertionSort(numbers);

        for (int number : numbers) {
            System.out.print(number + " ");
        }
    }
}
```

출력:

```text
2 3 4 5 8
```

---

## 왜 거의 정렬된 배열에서 효율적인가?

삽입 정렬의 비용은 `key`를 삽입하기 위해 **얼마나 많은 원소를 오른쪽으로 이동해야 하는가**에 크게 영향을 받는다.

이미 정렬된 배열이라면:

```text
[1, 2, 3, 4, 5]
```

각 단계에서:

```java
arr[j] > key
```

조건이 바로 거짓이 된다.

따라서 원소를 이동시키지 않고 다음 원소로 넘어갈 수 있다.

반면 역순으로 정렬된 배열:

```text
[5, 4, 3, 2, 1]
```

에서는 새로운 원소를 삽입할 때마다 앞쪽의 거의 모든 원소를 오른쪽으로 이동해야 한다.

그래서 Insertion Sort는 **작은 배열이나 이미 어느 정도 정렬된 배열에서 효율적**이다.

---

## 시간복잡도

### Best Case

이미 오름차순으로 정렬된 경우:

```text
[1, 2, 3, 4, 5]
```

각 원소마다 한 번 정도만 비교하면 되므로:

```text
O(N)
```

이다.

### Average Case

평균적으로 삽입 위치를 찾기 위해 앞쪽 원소들의 상당 부분을 확인하고 이동해야 하므로:

```text
O(N²)
```

이다.

### Worst Case

역순으로 정렬된 경우:

```text
[5, 4, 3, 2, 1]
```

매 단계마다 앞쪽 원소를 거의 모두 이동해야 한다.

```text
1 + 2 + 3 + ... + (N - 1)
```

이므로:

```text
O(N²)
```

이다.

| 경우 | 시간복잡도 |
|---|---:|
| Best | O(N) |
| Average | O(N²) |
| Worst | O(N²) |

---

## 공간복잡도

별도의 배열을 만들지 않고 기존 배열 내부에서 값을 이동시킨다.

따라서 추가 공간복잡도는:

```text
O(1)
```

이다.

Insertion Sort는 **In-place Sort**이다.

또한 같은 값의 상대적인 순서를 유지하므로 일반적인 구현은 **Stable Sort**이다.

---

# Binary Insertion Sort

## 개념

**Binary Insertion Sort**는 일반 Insertion Sort에서  
`key`가 들어갈 위치를 찾는 과정을 **[[이진 탐색]]으로 개선한 방식**이다.

일반 Insertion Sort는 정렬된 구간을 뒤에서부터 하나씩 비교한다.

```text
[2, 4, 6, 8, 10]

key = 5

10 > 5
8 > 5
6 > 5
4 < 5

→ 4와 6 사이에 삽입
```

Binary Insertion Sort는 앞쪽 구간이 이미 정렬되어 있다는 점을 이용한다.

```text
[2, 4, 6, 8, 10]

key = 5
```

정렬된 구간에서 [[이진 탐색]]을 사용하여 삽입 위치를 찾는다.

```text
중간값 6 확인
→ 5 < 6
→ 왼쪽 탐색

중간값 4 확인
→ 5 > 4

→ 4와 6 사이에 삽입
```

---

## 일반 Insertion Sort와의 차이

일반 Insertion Sort:

```text
삽입 위치 탐색
→ 순차 탐색
→ O(N)
```

Binary Insertion Sort:

```text
삽입 위치 탐색
→ 이진 탐색
→ O(log N)
```

하지만 삽입 위치를 찾은 뒤에는 여전히 원소들을 오른쪽으로 이동해야 한다.

```text
[2, 4, 6, 8, 10]

key = 5
삽입 위치 = index 2

6, 8, 10을 오른쪽으로 이동

[2, 4, _, 6, 8, 10]

5 삽입
```

따라서 **비교 횟수는 줄일 수 있지만 전체 시간복잡도는 여전히 O(N²)**이다.

---

## Java 구현

```java
public class Main {

    public static void binaryInsertionSort(int[] arr) {

        for (int i = 1; i < arr.length; i++) {

            int key = arr[i];

            // 정렬된 구간 [0, i)에서
            // key가 들어갈 위치 탐색
            int left = 0;
            int right = i;

            while (left < right) {

                int mid = (left + right) / 2;

                // 같은 값 뒤에 삽입하여 안정성 유지
                if (arr[mid] <= key) {
                    left = mid + 1;
                } else {
                    right = mid;
                }
            }

            int insertPos = left;

            // 삽입 위치 뒤의 원소들을 오른쪽으로 이동
            for (int j = i; j > insertPos; j--) {
                arr[j] = arr[j - 1];
            }

            arr[insertPos] = key;
        }
    }

    public static void main(String[] args) {

        int[] numbers = {5, 3, 8, 4, 2};

        binaryInsertionSort(numbers);

        for (int number : numbers) {
            System.out.print(number + " ");
        }
    }
}
```

출력:

```text
2 3 4 5 8
```

---

## Binary Insertion Sort의 시간복잡도

삽입 위치를 찾는 과정은 [[이진 탐색]]을 사용하므로:

```text
O(log N)
```

이다.

하지만 실제 삽입을 위해 원소를 이동하는 작업은 최악의 경우:

```text
O(N)
```

이 필요하다.

이 작업을 `N`개의 원소에 대해 반복하므로 전체 시간복잡도는:

```text
O(N²)
```

이다.

즉 Binary Insertion Sort의 장점은 전체 점근적 시간복잡도를 낮추는 것이 아니라  
**삽입 위치를 찾기 위한 비교 횟수를 줄이는 것**이다.

| 항목 | Insertion Sort | Binary Insertion Sort |
|---|---:|---:|
| 삽입 위치 탐색 | O(N) | O(log N) |
| 원소 이동 | O(N) | O(N) |
| Worst 전체 시간 | O(N²) | O(N²) |
| 추가 공간 | O(1) | O(1) |
