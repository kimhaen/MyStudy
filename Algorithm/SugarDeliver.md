
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-181231-lightgray.svg?style=flat-square)

# 사칙연산 응용문제
> 몫과 나머지 구하기 연산으로 응용문제 풀기

## 설탕 배달  ([백준2839])
>Q. N 킬로그램의 설탕의 배달하기 위해 3 킬로그램과 5 킬로그램으로 구성된 최소 개수의 설탕봉지를 구한다

첫째 줄에 N이 주어진다. (3 ≤ N ≤ 5000)
상근이가 배달하는 봉지의 최소 개수를 출력한다. 만약, 정확하게 N킬로그램을 만들 수 없다면 -1을 출력한다.

## 테스트 케이스

- 18 >> 4
- 4 >> -1
- 6 >> 2
- 9 >> 3
- 11 >> 3

```JAVA
package arithmetic;

import java.util.Scanner;

public class SugarDeliver {

	static Scanner scn = new Scanner(System.in);

	public static void main(String[] args) {

		/*
		 * 더 적은 개수의 봉지로 배달해야 함
		 * 3 키로 봉지와 5 키로 봉지가 있음
		 */

		int bongThree = 3;
		int bongFive = 5;

		int n = scn.nextInt();

		int tot = n / bongFive;
		int nam = n % bongFive;

		// 5 로 나누어 떨어지면 최고!
		if (nam == 0) {
			System.out.println(tot);
		} else {

			// 아니라면 그 나머지가 3 으로 나누어 떨어지기까지 반복
			while (true) {
				if (nam % bongThree == 0) {
					System.out.println(tot + nam / bongThree);
					break;
				} else {
					tot --;

					// 3, 5 모두로 불가능하면 -1 출력하고 종료
					if (tot < 0) {
						System.out.println(-1);
						break;
					}

					nam += 5;
				}
			}

		}

	}

}

```
[백준2839]: https://www.acmicpc.net/problem/2839


