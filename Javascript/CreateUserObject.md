
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190114-lightgray.svg?style=flat-square)

## 자바스크립트로 클래스 작성 및 객체 생성하기

- 준 객체 언어? : 컴파일이 아닌 인터프리터 방식으로 코드를 해석한다
- 내부 멤버 변수를 참조할 때는 무조건 'this' 키워드를 사용해야 한다
- 클래스 메서드와 인스턴스 메서드를 만들 수 있다

### 자바스크립트는 동적 결합 언어

- 동적으로 실행되면서 멤버 및 함수가 생성되어 결합된다
- 코드가 순차적으로 진행되면서 (느닷없이)멤버나 함수가 생기기도 한다. 규칙이 그렇다
- 예를 들어, 아무 것도 없는 객체 리터럴을 만들고 이후에 [객체].setName 을 실행하면 name 멤버가 생기는 방식이다

### 객체 생성하기

- 객체 리터럴을 사용하여 Object 객체 생성하기
- new 연산자와 함수를 사용하여 객체 생성하기
- prototype 을 사용하여 객체 생성 (인스턴스 메서드 생성)

object01.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>사용자 지정 객체 만들기</title>
</head>
<body>

	<div id='object01'>
		<h1>사용자 지정 객체 만들기</h1>
		<p id='info'>
			객체 리터럴을 생성하고, 코드 진행에 따라 멤버를 추가하여 결과를 얻는다
		</p>
		
		<script>
			// 객체 리터럴에 멤버 추가
			var obj01 = {};
			obj01.num01 = 100;
			obj01.num02 = 200;
			obj01.sum = obj01.num01 + obj01.num02;
			document.write("obj01.sum : " + obj01.sum);
			document.write('<br/><br/>');
			
			// 객체 리터럴에 함수 추가
			var obj02 = {};
			obj02.num01 = 100;
			obj02.num02 = 200;
			obj02.sum = function () { // 함수명은 이름만, 뒤에 함수 정의에서 제대로 명시
				return this.num01 + this.num02;
			}
			document.write('obj02.sum() : ' + obj02.sum());
			document.write('<br/><br/>');
			
			// 객체 리터럴에 세터와 게터 추가 (멤버, 함수 동시생성)
			var obj03 = {};
			obj03.setName = function (n) {this.name = n}
			obj03.setAge = function (a) {this.age = a}
			obj03.getName = function () {return this.name}
			obj03.getAge = function () {return this.age}
			obj03.setName('hong');
			obj03.setAge(100);
			document.write('obj03.getName() : ' + obj03.getName());
			document.write('<br/>obj03.getAge() : ' + obj03.getAge());
		</script>
	</div>

</body>
</html>
```

















