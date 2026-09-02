[[정렬]]

버블 정렬은 **서로 인접한 두 값을 비교해서 순서가 잘못되어 있으면 교환**하는 방식. 한 번의 반복(패스스루)이 끝날 때마다 가장 큰 값이 배열의 뒤쪽으로 이동한다.

시간 복잡도는 일반적인 구현 기준으로 **O($n^2$)** 이며, 모두 정렬이 되어있는 최선의 경우 **O(n)** 이다. 구현이 단순해서 정렬 알고리즘의 원리를 학습할 때 자주 사용한다.

1. 배열 내의 연속된 두 항목을 두 포인터로 각각 가리킨다.
2. 첫 번째 항목과 두 번째 항목을 비교
3. 두 항목의 순서가 뒤바뀌어 있다면 swap
4. 포인터들을 오른쪽으로 한칸 움직임
5. 마지막 항목까지 2~4 단계 반복 (패스스루, 배열의 맨 뒤에서부터 차곡차곡 올바른 위치의 원소들로 채워짐)
6. 더 이상 swap이 일어나지 않는 패스스루가 발생할때 까지 반복


예시 코드
```java
public static void bubbleSort(int[] arr){
	int n = arr.length;
	
	for(int i=0; i<n-1; i++){
		for(int j=0; k < n-1-i; j++){
			if(arr[j] > arr[j + 1]){
				int temp = arr[j];
				arr[j] = arr[j+1];
				arr[j+1] = temp;
			}
		}
	}
}

public static void main(String[] args){
	int[] numbers = {5,3,8,4,2};
	bubbleSort(numbers);
	
	for(int number : numbers){
		System.out.print(number + " ");
	}

}
```