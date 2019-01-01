
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190101-lightgray.svg?style=flat-square)

# 규칙 찾기
> 규칙을 찾아 문제를 해결하기

## 벌집  ([백준2292])

![BeeHouse](https://github.com/daesungRa/MyStudy/blob/master/imgs/BeeHouse.png)

>Q. 위의 그림과 같이 육각형으로 이루어진 벌집이 있다. 그림에서 보는 바와 같이 중앙의 방 1부터 시작해서 이웃하는 방에 돌아가면서 1씩 증가하는 번호를 주소로 매길 수 있다. 숫자 N이 주어졌을 때, 벌집의 중앙 1에서 N번 방까지 최소 개수의 방을 지나서 갈 때 몇 개의 방을 지나가는지(시작과 끝을 포함하여)를 계산하는 프로그램을 작성하시오. 예를 들면, 13까지는 3개, 58까지는 5개를 지난다.

### 입력

첫째 줄에 N(1 ≤ N ≤ 1,000,000,000)이 주어진다.

### 출력

입력으로 주어진 방까지 최소 개수의 방을 지나서 갈 때 몇 개의 방을 지나는지 출력한다.

### 테스트 케이스

- 13 >> 3
- 58 >> 5

### 코드

```JAVA
package findRule;

import java.util.Scanner;

public class BeeHouse {

	static Scanner scn = new Scanner(System.in);
	
	public static void main(String[] args) {
		// 각 그룹의 첫째 숫자: 1, 2, 8, 20, 38, 62, 92
		// 각 첫째 숫자 간의 관계: 1, 6, 12, 18, 24, 30
		// 관계 설명: 첫 그룹은 1 이고, 그 다음 그룹마다 그 구성원이 6 개씩 증가한다
		// 1 에서부터 각 그룹의 방까지의 최소거리: 1, 2, 3, 4, 5, ...
		
		// 결국 주어진 숫자의 그룹을 구하고 그 그룹의 번호만큼이 최소거리라고 보면 된다
		// 
		
		int n = scn.nextInt();
		
		// 그룹 1 부터 시작
		int group = 1;
		// 현재 그룹까지의 총 갯수
		int sum = 1;
		// 다음 그룹 증가 수
		int incre = 6;
		
		while (n > sum) {
			sum += incre * group; // 그룹이 올라갈수록 6의 배수만큼 멤버개수 증가
			group ++; // 다음 그룹
		}
		
		// 그룹 번호가 곧 최소거리
		System.out.println(group);
	}
	
}
```

### 해결방법 요약

1. 거리가 1 증가할 때마다 일정한 규칙으로 그룹화가 가능함을 캐치
2. 각 그룹의 그룹번호를 매기되, 그것이 곧 1 번 방으로부터의 거리가 되도록 맞춘다
3. 규칙에 따라 주어진 숫자의 소속그룹을 구하여 그대로 출력하면 완료

[백준2292]: https://www.acmicpc.net/problem/2292


