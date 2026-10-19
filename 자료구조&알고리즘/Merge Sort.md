## 개념

**Merge Sort(병합 정렬)**는 배열을 계속 작은 단위로 나눈 뒤,  
**정렬된 작은 배열들을 다시 합치면서 전체 배열을 정렬하는 알고리즘**이다.

대표적인 [[분할 정복]] 알고리즘이다.

> Merge = 합치다, 병합하다

---

## 로직

Merge Sort의 핵심은 두 단계이다.
1. 배열을 계속 절반으로 나눈다.
2. 작은 배열부터 정렬된 상태로 다시 합친다.

예를 들어 다음 배열이 있다고 하자.
```text
[5, 2, 4, 1]
```

먼저 계속 절반으로 나눈다.
```text
        [5, 2, 4, 1]

        /          \

    [5, 2]        [4, 1]

    /   \          /   \

  [5]   [2]      [4]   [1]
```

원소가 하나인 배열은 이미 정렬되어 있다고 볼 수 있다.

이제 작은 배열부터 다시 합친다.
```text
[5] + [2]
→ [2, 5]

[4] + [1]
→ [1, 4]
```

현재 상태:
```text
[2, 5]    [1, 4]
```

다시 두 배열을 병합한다.
```text
[2, 5] + [1, 4]

→ [1, 2, 4, 5]
```

---

## Merge 과정

Merge Sort에서 실제로 정렬이 이루어지는 핵심 부분이다.

두 배열이 이미 각각 정렬되어 있다고 하자.
```text
A = [1, 4, 8]
B = [2, 3, 7]
```

두 배열의 앞쪽 값부터 비교한다.

```text
1 vs 2 → 1 선택
4 vs 2 → 2 선택
4 vs 3 → 3 선택
4 vs 7 → 4 선택
8 vs 7 → 7 선택
남은 8 선택
```

결과:

```text
[1, 2, 3, 4, 7, 8]
```

즉 이미 정렬된 두 배열은  
**각 배열의 현재 가장 작은 값만 비교하면서 쉽게 하나의 정렬된 배열로 만들 수 있다.**

---

## 왜 배열을 계속 나누는가?

정렬되지 않은 큰 배열을 한 번에 정렬하는 것은 복잡하다.

하지만 원소가 하나인 배열은 이미 정렬되어 있다고 볼 수 있다.

```text
[5]
[2]
[4]
[1]
```

따라서 큰 문제를 작은 문제로 나누고,  
정렬된 작은 결과들을 다시 합치는 방식으로 전체 문제를 해결한다.

이것이 [[분할 정복]]의 구조이다.
```text
Divide
→ 큰 배열을 절반으로 나눈다.

Conquer
→ 각각의 부분 배열을 정렬한다.

Combine
→ 정렬된 두 배열을 Merge한다.
```

---

## Java 구현

```java
import java.util.Arrays;

public class Main {

    static void mergeSort(int[] arr, int left, int right) {

        // 원소가 하나 이하이면 이미 정렬된 상태
        if (left >= right) {
            return;
        }

        int mid = (left + right) / 2;
        
        // 왼쪽 절반 정렬
        mergeSort(arr, left, mid);

        // 오른쪽 절반 정렬
        mergeSort(arr, mid + 1, right);

        // 두 정렬된 구간 병합
        merge(arr, left, mid, right);
    }

    static void merge(
            int[] arr,
            int left,
            int mid,
            int right
    ) {

        int[] temp =
                new int[right - left + 1];

        int i = left;      // 왼쪽 구간 포인터
        int j = mid + 1;   // 오른쪽 구간 포인터
        int k = 0;         // temp 포인터

        // 양쪽 배열을 비교하면서
        // 작은 값을 temp에 저장
        while (i <= mid && j <= right) {

            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                temp[k++] = arr[j++];
            }
        }

        // 왼쪽에 남은 값 처리
        while (i <= mid) {
            temp[k++] = arr[i++];
        }

        // 오른쪽에 남은 값 처리
        while (j <= right) {
            temp[k++] = arr[j++];
        }

        // 정렬 결과를 원래 배열에 복사
        for (int t = 0; t < temp.length; t++) {
            arr[left + t] = temp[t];
        }
    }

    public static void main(String[] args) {

        int[] arr = {5, 2, 4, 1};

        mergeSort(
                arr,
                0,
                arr.length - 1
        );

        System.out.println(
                Arrays.toString(arr)
        );
    }
}
```

출력:

```text
[1, 2, 4, 5]
```

---

## 시간복잡도

배열을 계속 절반으로 나누므로 깊이는 약:
```text
log N
```
이다.

```text
N
↓
N / 2
↓
N / 4
↓
N / 8
↓
...
↓
1
```

각 단계에서는 모든 원소를 한 번씩 병합하므로:
```text
O(N)
```
의 작업이 필요하다.

따라서 전체 시간복잡도는:
```text
O(N log N)
```

이다.

| 경우      |      시간복잡도 |     |
| ------- | ---------: | --- |
| Best    | O(N log N) |     |
| Average | O(N log N) |     |
| Worst   | O(N log N) |     |

---

## 공간복잡도

병합 과정에서 임시 배열을 사용한다.
```java
int[] temp = new int[right - left + 1];
```

따라서 일반적인 구현의 추가 공간복잡도는:
```text
O(N)
```
이다.
