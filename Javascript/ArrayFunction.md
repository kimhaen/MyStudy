
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190110-lightgray.svg?style=flat-square)



실무에서 중요한 것은 타이밍.. 적용 스킬이 좋은 것보다 기한을 지키는 것이 우선이다

# Javascript 로 다차원 배열 만들기

JS 에서는 문법적으로 다차원 배열을 지원하지 않는다<br/>
그러나 일차원 배열만으로 다차원 배열을 만들어볼 수 있다

```JAVASCRIPT
a = new Array(3);
a[0] = new Array(4);
a[1] = new Array(5);
a[0][2] = new Array(6);

// 출력
```

- 코드를 보면 알다시피 일차원 배열을 중첩하는 방식으로 다차원 배열을 만들 수는 있으나,
- 각 중첩 수준마다 행 또는 열의 사이즈가 제각각이다
- JAVA 에서처럼 행과 열이 정방향이 되도록 지원하지는 않는다

### 배열에 관한 편리한 메서드 (함수)

- concat
	* 두 배열을 하나의 배열로 합친다
	* 배열 a 와 b 라면, a 뒤에 b 를 붙여 변수 r 에 저장하는 방식

```JAVASCRIPT
<script>
	var a = new Array(1, 2, 3, 4, 5);
	var b = new Array(6, 7, 8, 9, 0);
	var r = a.concat(b);

	document.write(a + <br/>);
	document.write(b + <br/>);
	document.write(r + <br/>);
</script>
```

출력 결과는 a 배열 b 배열 합쳐진 r 배열 순이다

- join
	* 배열의 항목들을 출력할 때 구분자 기호를 지정할 수 있다. 기본값은 콤마(,)이다

```JAVASCRIPT
...
	document.write(a.join('-') + <br/>);
...
```

출력 결과는 - 기호를 구분자로 출력된 a 배열의 데이터들이다

- reverse
	* 같은 방식으로 지정된 배열에 대해 역순으로 출력한다

- sort
	* **문자열 오름차순**으로 정렬됨. 그러므로 숫자 데이터 정렬시 원하는 결과가 나오지 않을 수 있음
	* 이 때는 임의로 정의된 function 을 인자로 넘겨준다

```JAVASCRIPT
<script>
	function num (a, b) {
		return a - b;
	}

	var arr = new Array(6, 5, 4, 3, 12, 11, 2);
	a.sort(num);
	document.write('arr: ' + arr + '<br/>);
</script>
```

	* 이 코드의 결과는 오름차순 정렬된 arr 의 숫자들이 출력된다
	* 내부 로직은 알수 없으나, num 함수에서 전달된 a - b 의 결과가 양수라면 둘의 자리를 바꾸기 때문 > 오름차순 정렬됨

# 함수

### 수학 함수

- eval('계산식');
	* 숫자 계산식이 문자열 형식으로 전달되도 계산결과가 출력된다 (와우!)

- parseInt(str, radix);
	* 입력된 숫자나 문자를 숫자로 변경
	* 입력정보, 진수

- parseFloat('문자열');
	* 문자열을 실수로 변환하여 리턴

- isFinite(number);
	* number 가 유한의 수인가를 체크
	* 실제로는 유/무한의 수를 판별하지 못하지만,
	* 숫자로만 이루어져 있는가를 체크할때 사용

- isNaN(value);
	* value 가 문자인가를 체크한다 (Not a Number)
	* 'value 가 숫자가 아닌 것이 참인가?' >> 'value 가 문자인가?'

### 문자열 함수

- substr(시작위치, [길이]);
	* 문자열의 시작위치부터 지정한 길이까지 잘라내서 반환
	* 길이가 생략되면 시작위치부터 문자열 끝까지 잘라냄

- toLowerCase();
	* 문자열을 모두 소문자로 변경

- toUpperCase();
	* 문자열을 모두 대문자로 변경

- indexOf('문자열'); / lastIndexOf('문자열');
	* 해당 문자열이 존재하는 위치를 반환. 찾지 못하면 -1 을 반환한다
	* 전자는 앞에서부터, 후자는 뒤에서부터 찾는다. 그러나 위치값은 무조건 앞에서부터 계산한다

- charAt(index);
	* 지정된 index 위치의 값을 가져온다

- split('나눌 문자열');
	* 지정된 문자열을 나눌 문자열로 분리하여 배열형으로 반환
	* HTML 에서 split 활용 - textarea 에 입력된 데이터는 개행을 '\n' 으로 표현한다. 그러므로 split 함수로 분리해줘야 한다

```JAVASCRIPT
...
var b = '111111\n222222\n333333\n444444\n';
var array = b.split('\n');
for (var i = 0; i < array.length; i++) {
	document.write('<li>' + array[i]);
}
...
```

- replace(정규식, 바꿀 문자);
	* 지정된 문자열의 선택한 위치의 문자를 특정 문자열 변환
	* 정규식을 사용할 수도 있다
	* 활용 - 개행 문자열 \n 을 br 태그로 대치한다

```JAVASCRIPT
...
var b = '111111\n222222\n333333\n444444\n';
var c = b.replace(/\n/g, '<br/>');
document.write('<li>replace ' + c);
```

- 자바스크립트에서의 정규식의 표현 방식 > /정규식/g > 지정 문자열 전체에서 정규식 부분을 적용함

- search(정규식 또는 찾을 문자열);
	* 매개변수로 전달된 문자를 찾아 그 위치값을 반환한다

- match(정규식 및 찾을 문자열);
	* 못찾으면 null 반환

### 사용자 정의 함수

- 키워드 '**function**' 을 사용한다. 구조는 반환형 지정 없이 JAVA 의 그것과 거의 같다 (var, 반환값은 형식 상관 없이 그냥 반환하면 됨)
- 사용자 정의 함수의 매개변수 특성
	* 만약 매개변수를 6 개 정의하고, 실제 사용시에 인자를 한 개만 전달해도 큰 문제가 없다. 나머지 다섯 개는 null 로 처리된다
	* 앞 매개변수에서부터 개수만큼 채워지는 것인데, 이런 특성 때문에 경우에 따른 함수로직 처리가 까다롭다 (null 처리가 추가적으로 필요하게 됨)
	* 선언된 매개변수의 개수보다 많은 개수의 인자가 투입되면 초과한 만큼의 데이터는 그냥 버려진다
- 사용자 정의 함수 예제

funcUser.jsp
```JAVASCRIPT
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>funcUser</title>
</head>
<body>

	<div id='funcUser'>
		<h1>사용자 정의 함수</h1>
		
		<script>
			function sum (a, b) {
				if (a == null) a = 0;
				if (b == null) b = 0;
				return a + b;
			}
			
			function main () {
				document.write('<ul>');
				
				document.write('<li>sum() : ' + sum());
				document.write('<li>sum(10) : ' + sum(10));
				document.write('<li>sum(10, 20) : ' + sum(10, 20));
				document.write('<li>sum(30, 40, 50) : ' + sum(30, 40, 50));
				
				document.write('</ul>');
			}
			
			main();
		</script>
	</div>

</body>
</html>
```











