
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190103-lightgray.svg?style=flat-square)

# 규칙 찾기
> 규칙을 찾아 문제를 해결하기

## 분수 찾기  ([백준1193])

>Q. 무한히 큰 배열에 다음과 같이 분수들이 적혀있다.

![FindFraction](https://github.com/daesungRa/MyStudy/blob/master/imgs/FindFraction.png)

>이와 같이 나열된 분수들을 1/1 -> 1/2 -> 2/1 -> 3/1 -> 2/2 -> … 과 같은 지그재그 순서로 차례대로 1번, 2번, 3번, 4번, 5번, … 분수라고 하자. X가 주어졌을 때, X번째 분수를 구하는 프로그램을 작성하시오.

### 입력

첫째 줄에 X(1 ≤ X ≤ 10,000,000)가 주어진다.

### 출력

첫째 줄에 분수를 출력한다.

### 테스트 케이스

- 14 >> 2/4
- 54 >> 9/2

### 코드

```JAVA
package findRule;

import java.util.Scanner;

public class FindFraction {

	static Scanner scn = new Scanner(System.in);

	public static void main(String[] args) {
		// 분수라고는 하지만 형태는 [분자/분모] 임. 즉 배열 상으로 [행/열] 이다
		// 각 열(분모)을 기준으로 행을 따라 1, 2, 3, 4, 5, ... 순으로 분자가 증가함
		// 예를 들어 1 열에서는 [1/1], [2/1], [3/1], [4/1], ... 순이고,
		// 2 열에서는 [1/2], [2/2], [3/2], [4/2], ... 순이다
		// 그러므로 주어지는 숫자 x (번째) 칸의 위치가 몇행 몇열인지 알면 해결 가능 [행/열]

		/*
		 * << 숫자 규칙 >> 1 , 2 , 6 , 7 , 15, 16, 28, 29 3 , 5 , 8 , 14, 17, 27, 4 , 9 ,
		 * 13, 18, 26, 10, 12, 19, 25, 11, 20, 24, 21, 23, 22,
		 */

		// 숫자 입력
		int x = scn.nextInt();

		// 숫자의 칸 알기
		int i; // 메인 숫자. 입력받은 숫자까지 1 씩 증가함. 반복을 위함
		int flag = 1; // 분기. 1 혹은 -1 이다. 1: 열감소 행증가, -1: 열증가 행감소
		int col = 2; // 열. 분수의 분모에 해당
		int row = 1; // 행. 분수의 분자에 해당

		if (x <= 1) {
			System.out.println("1/1");
			return;
		}

		for (i = 2; i < x; i++) { // 입력받은 숫자까지 반복. 2 회기의 첫 번째인 [1/2] 부터 시작하는 셈
			if (row == 1 && flag == -1) {
				col ++;
				
				flag *= -1;
			} else if (col == 1 && flag == 1) {
				row ++;
				
				flag *= -1;
			} else {
				if (flag == 1) {
					// 열감소 행증가
					col --;
					row ++;
				} else if (flag == -1) {
					// 열증가 행감소
					col ++;
					row --;
				}
			}
		}

		// 칸 [행/열] 출력
		System.out.println(row + "/" + col);
	}
}
```

### 해결방법 요약

1. 지정된 숫자까지 증가하는 메인 숫자 / 회기 당 방향을 지정하는 플래그 / 컬럼 및 행 변수
	- 메인 숫자가 끝나면 반복 종료
2. 최초 1 번은 그냥 출력하도록 하고, 실질적인 시작은 2 번째인 [1/2] 부터임
3. flag 에 따라 열감소 행증가 혹은 열증가 행감소를 수행한다
4. 지그재그 각 끝단에 가면 (col == 1 or row == 1) 다음 수로 증가하고 flag 를 역전한다
5. 반복이 종료되면 [행/열] 형식으로 출력

[백준1193]: https://www.acmicpc.net/problem/1193


