![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190107-lightgray.svg?style=flat-square)

# 개요

> 자바스크립트는 전방위적으로 매우 강력하면서도 인식적으로 굉장히 저평가되는 언어이다.
> 자바스크립트는 HTML,CSS 로 구성된 웹 문서를 동적으로 표현하기 위해 네스케이프 사와 선 마이크로시스템즈 사가 공동으로 개발한 웹 언어이다
> CSS 로는 동적 페이지 작성에 많은 한계를 느낄 것이다. 그러나 JS 를 활용한다면 무궁무진한 가능성이 펼쳐진다!

- HTML, CSS 와 함께 사용한다
- 준객체 지향형 언어
- 인터프리터 언어 (컴파일 형식이 아님!) > 속도는 굉장히 느림. 그러나 최신 기술발달로 충분한 속도를 낸다
- 자바와는 전혀 다름
- HTML 문서 내에 존재한다
- 변수 타입을 지정할 필요는 없으나 (var), 지역 및 전역 개념은 구분해야 한다
	- 블록 안에서 var 타입으로 변수를 선언하면 지역형으로 선언됨!! (중요!!)
- 보안성은 없다고 보면 된다 (소스가 그냥 공개됨) > 그래서 외부파일로 작성하고 link 하는 방식으로 많이 사용됨

## (참고) 웹 페이지를 만들 때 가장 중요한 개념

- M(Model)	: date, database
- V(View)	: ui, ux (HTML, CSS, JS, ...)
- C(Controller)	: 제어 프로그램 (개발자)

지금까지는 이런 분류 없이 한 파일에 혼재했는데, 웹 프로젝트에서 규모가 커질수록 mvc 에 따라 철저하게 분리하는 것이 중요하게 된다.
예전 모든 흐름을 개발자가 전부 담당해도 괜찮았다면, 현재에는 그것이 불가능하다.
그러므로 DBA, 백엔트, 프론트엔드, 퍼블리셔 등의 분업이 불가피하게 되었다.

## 자바스크립트 작성 방법

- script 태그 내에 작성
- HTML 태그 안에 작성
- 외부에 파일로 만들고 참조하기
- 특별한 경우가 아니라면 head 태그 내에 script 태그를 만들고 그 안에 작성한다. 이는 해당 페이지 전체에 적용된다
	- 그러나 요즘은 해당 페이지를 최초 로드할 때 용량부하가 커지는 요인이 되므로 지역 내 임시적으로 사용하기도 한다
- 문법 구조
	- 데이터 타입이 없다는 것 외에 배열 및 제어문까지는 C 언어와 거의 동일하다.
	- 주석 방식도 동일하다
	- 출력 메서드 : <code>document.write([var]);</code>
	- document 는 브라우저의 바탕화면을 의미한다
	- 특정 이벤트 외에 스크립트를 태그 내에 작성하는 일은 지양해야 한다. 심지어 이벤트 처리에서도 가급적 지양
	- 왜냐하면 mvc 분리 원칙에 위배되기 때문. HTML 은 view 단이지만, 이벤트 처리는 일종의 control 이므로 상호간에 의존성이 발생함
- 이벤트 핸들러
	- 이벤트 핸들러 이름의 지정 : 동작 이름 앞에 on 접두사를 붙인다
- 외부 스크립트 파일 삽입하기 (공용모듈)
	- 그냥 웹 페이지에 작성하면 소스보기만으로 모든 JS 코드가 노출된다
	- 그나마 보안성을 위해 스크립트 코드를 별도의 파일로 저장하고 웹 페이지에서는 그것을 참조하는 방식으로 한다
	- 방식

```JAVASCRIPT
...
<script language src='스크립트 파일'></script>
...
```

여기서 주의할 점은 script 태그의 마무리를 제대로 지정해야 한다는 것 (/script 명시!)<br/>
안그러면 로드만 되고 적용은 안 된다

## 표기 방식

- ';' 세미콜론 생략이 가능하다. 일반적으로 문장 종결에 무조건 사용한다
- 대소문자 구별함
- 따옴표 구별 없음 > 문자와 문쟈열 구분이 없음
- 명령어들의 집합은 {} 로 묶는다

## 데이터 표기 방법 > JSON

- 

## 상수와 변수, 연산자

- 상수 : 정수형, 실수형, boolean 형, 문자열형, null
- 변수 : var (전역, 지역)
- 연산자 : 마찬가지로 자바와 같다

## 객체지향의 의미

- 객체를 만들어 다른 영역에서 호출을 통해 오브젝트를 생성해서 사용하는 방식

## 반복문, 조건문, 배열 등등 자바와 흡사하다

if 문
```JAVASCRIPT
<script>
	if (a == 1) {
		k = "a"
	} else if (a == 2) {
		k = "b"
	} else if (a == 3) {
		k = "c"
	}
</script>
```

switch 문
```JAVASCRIPT
<script>
	switch (a){
		case 8 : str = "팔";break;
		case 9 : str = "구";break;
		case 10 : str = "십";break;
		default : str = "7이하";
	}
</script>
```

## 실습

> CSS 에서와 마찬가지로 HTML 각 요소들의 id 값을 활용해 동적으로 실행된다

### 인터프리터 방식 확인하기

연습용 jsp 파일을 만들어 활용

jsTest.jsp
```JAVASCRIPT
...
<script>
	alert('OK~');
</script>
</head>
<body>

	<div id='test'>
		<h1>스크립트 선언 방법</h1>
	</div>

</body>
...
```

자바스크립트는 인터프리터 방식이다.
이 코드는 head 내에 alert 를 실행하는 스크립트 코드가 우선 존재하고, 이후 body 에 가서 h1 을 출력하는 순서로 스크립팅되어 있기 때문에
alert 수준에서 블러킹된 상태로 h1 은 페이지에 노출되지 않는다. 확인 버튼을 누르면 그제서야 순차적으로 나타난다.

### 외부 소스(스크립트) 파일 첨부 방식으로 적용하기

먼저 자바스크립트 소스 파일을 만든다
자바스크립트 코드는 자신을 참조하는 페이지(document)의 btn(input button 태그) 버튼 값을 가져와 지정된 함수를 실행하도록 작성

test.js
```JAVASCRIPT
/**
 * refer by test.jsp
 */

 window.onload = function() {
    var btn = document.getElementById('btn');
    btn.onclick = function () {
        alert('button clicked....');
    }
 }
```

그리고 jsp 파일에서 외부참조 한다
id 가 btn 인 버튼태그를 누르면 참조중인 스크립트 코드가 실행된다

jsTest.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>스크립트 선언 방법</title>
<script>
	alert('OK~');
</script>
<script src='../js/test.js'></script>
</head>
<body>

	<div id='test'>
		<h1>스크립트 선언 방법</h1>
		
		<input type='button' value='click' onclick='javascript:alert("good~")' />
		<input type='button' value='func' id='btn' />
	</div>

</body>
</html>
```

### 단순 문법만 보면 굉장히 단순하고 쉬워 보이지만, HTML / CSS 및 다양한 웹 언어들과 구조적으로 맞물려 돌아가는 가운데 로직을 짜려면 상당히 복잡해진다. 결코 만만히 봐서는 안됨.
























