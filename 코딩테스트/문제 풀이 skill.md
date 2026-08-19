**해시 테이블을 인덱스로 활용하기**
```java
// 이런 배열에서 어떤 값이 존재 하는지 확인하려고 할때
// 배열이면 선형 검색으로 O(n) 걸림
int[] arr = [23,36,54,64,75]; 

// 해시테이블 (HashMap)을 사용하면 O(1)로 바로 찾을 수 있음
Map<Integer> map = new HashMap<>();
map.put(23,true);
map.put(36,true);
map.put(54,true);
map.put(64,true);
map.put(75,true);

int val = map.get(23); // 있으면 true 나옴
val = map.get(100); // 없으면 null 나옴
```


```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public static boolean isSubset(int[] array1, int[] array2) {
        int[] largerArray = array1.length > array2.length ? array1 : array2;
        int[] smallerArray = array1.length > array2.length ? array2 : array1;

        Set<Integer> hashSet = new HashSet<>();
        for (int value : largerArray) {
            hashSet.add(value);
        }

        for (int value : smallerArray) {
            if (!hashSet.contains(value)) {
                return false;
            }
        }

        return true;
    }
}
```
