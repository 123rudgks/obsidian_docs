[[정렬]]
선택 정렬은 **아직 정렬되지 않은 영역에서 가장 작은 값을 찾아 앞쪽으로 보내는 방식**
시간 복잡도는 최선, 평균과 최악 모두 **O(n²)** 이고, 별도의 배열을 만들지 않기 때문에 추가 공간 복잡도는 **O(1)** 이다.

1. 배열 내의 원소를 순회하면서 최소값을 찾는다. (패스스루)
2. 패스스루 내의 최소값과 패스스루의 첫번째 원소와 swap (맨 앞으로 간 원소는 정렬된 자리 확정)
3. 패스스루를 반복하며 배열의 끝에서 시작하는 패스스루에 도달하면 종료 (정렬 완성)


코드 예시
```java
public static void selectionSort(int[] arr){
	int n = arr.length;
	
	for(int i = 0; i< n-1; i++){
		int minIndex = i;
		
		// 현재 위치 이후의 가장 작은 값의 인덱스 찾기
		for (int j = i+1; k<n; k++){
			if(arr[j] < arr[minIndex]){
				minIndex = j;
			}
		}
		
		// 가장 작은 값을 현재 위치와 교환
		int temp = arr[i];
		arr[i] = arr[minIndex];
		arr[minIndex] = temp;
	}
}

public static void main(String[] args){
	int[] numbers = {64, 25, 12, 22, 11};
	
	selectionSort(numbers);
	
	for(int number : numbers){
		System.out.print(number + " " );
	}
}
```