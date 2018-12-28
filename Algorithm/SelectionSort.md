
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-181228-lightgray.svg?style=flat-square)

# 선택정렬 예제
> 선택정렬은, 배열의 첫 번째 인덱스에서부터 마지막 이전 인덱스까지 순차적으로 선택하여,
각 기준 인덱스와 자신의 우측에 존재하는 모든 인덱스를 차례로 비교하여 정렬하는 알고리즘이다.

## 수 정렬하기 ([백준2750])
>Q. N 개의 수가 주어졌을 때, 이를 오름차순으로 정렬하는 프로그램을 작성하시오.

첫째 줄에 수의 개수 N(1 ≤ N ≤ 1,000)이 주어진다. 둘째 줄부터 N개의 줄에는 숫자가 주어진다. 이 수는 절댓값이 1,000보다 작거나 같은 정수이다. 수는 중복되지 않는다.

```JAVA
import java.util.Scanner;

public class SelectSort {

	static Scanner scn = new Scanner(System.in);
	
	public static void main(String[] args) {
		int[] arr;
		
		// 첫째 줄에 개수 N (1 <= N <= 1000), 둘째 줄부터 N 개의 줄에 숫자가 주어진다 (중복 ㄴㄴ)
		int n = scn.nextInt();
		
		if (n >= 1 && n <= 1000) {
			arr = new int[n];
		} else {
			return;
		}
		
		// n 개만큼 초기화
		for (int i = 0; i < n; i++) {
			arr[i] = scn.nextInt();
		}
		
		// asc sort
		for (int i = 0; i < arr.length - 1; i++) {
			for (int j = i + 1; j < arr.length; j++) {
				if (arr[i] > arr[j]) {
					int temp = arr[i];
					arr[i] = arr[j];
					arr[j] = temp;
				}
			}
		}
		
		for (int i = 0; i < arr.length; i++) {
			System.out.println(arr[i]);
		}

	}

}
```

## 같은 문제를 버블정렬로 풀어보기
> 버블정렬은, 첫 번째 인덱스에서부터 인접한 두 인덱스끼리 배열의 크기만큼 비교정렬을 하는 알고리즘이다.

```JAVA
import java.util.Scanner;

public class BubbleSort {

	static Scanner scn = new Scanner(System.in);
	
	public static void main(String[] args) {
		int[] arr;
		
		// 첫째 줄에 개수 N (1 <= N <= 1000), 둘째 줄부터 N 개의 줄에 숫자가 주어진다 (중복 ㄴㄴ)
		int n = scn.nextInt();
		
		if (n >= 1 && n <= 1000) {
			arr = new int[n];
		} else {
			return;
		}
		
		// n 개만큼 초기화
		for (int i = 0; i < n; i++) {
			arr[i] = scn.nextInt();
		}
		
		// asc sort
		for (int i = 0; i < arr.length - 1; i++) {
			for (int j = 0; j < arr.length - (i + 1); j++) {
				if (arr[j] > arr[j + 1]) {
					int temp = arr[j];
					arr[j] = arr[j + 1];
					arr[j + 1] = temp;
				}
			}
		}
		
		for (int i = 0; i < arr.length; i++) {
			System.out.println(arr[i]);
		}

	}

}
```
[백준2750]: https://www.acmicpc.net/problem/2750


