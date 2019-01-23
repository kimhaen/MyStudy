
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190107-lightgray.svg?style=flat-square)

## EL (Expression Language)

- EL 은 JSP 자바 데이터 및 코드를 간단하게 나타내기 위한 JSTL 규약 중 표현 언어를 의미한다
- 각 객체에 대한 게터의 결과를 매우 간결하게 표현한다
- 표현문법 '${}' 에서 중괄호 사이에 표현할 값을 입력하면 된다
- 예를 들어 member 객체의 id 를 표현하고 싶다면, ${member.id} 하면 된다
- 단, JSP 의 스크립트 (scriptlet, expression, declaration) 요소가 아닌 곳에서만 사용 가능하다
- 게터가 아니라 세터를 간략하게 표현하고 싶다면 tag library 언어를 사용한다 > JSTL (JSTL, JSP Standard Tag Library)
- ${expr} 방식은 바로 값을 계산해서 표현하지만(게터), #{expr} 방식은 일단 설정해 뒀다가 실제 값이 사용되는 시점에 계산된다
- 즉, JSTL 로 변수를 ${} 방식으로 세팅했다면 고정값이지만, #{} 방식으로 세팅했다면, 해당 변수가 사용되는 시점마다 가변적으로 계산된다

### EL 기초

- null 처리에 용이하다. 즉 NullPointerException 이 발생하지 않는다 (아무것도 없으면null 아무것도 없는 대로 공백)
- 데이터 타입과 리터럴
	* boolean
	* 정수: JAVA 의 long 타입
	* 실수: JAVA 의 double 타입
	* 문자열, null
- 기본 객체
	* param : requst.getParameter() 를 Map<파라미터 이름, 파라미터 값> 방식으로 표현, 값 타입은 String
	* paramValues : requst.getParameterValues() 를 Map<파라미터 이름, 파라미터 값 배열> 방식으로 표현, 값 타입은 String[]
	* pageContext, pageScope, requestScope, sessionScope, applicationScope, header, headerValues, cookie, initParam
- 객체 탐색
	* pageScope ~ requestScope ~ sessionScope ~ applicationScope 순으로 탐색한다
	* 성능 최적화를 위해 탐색할 스코프를 명시하는 것이 좋다
- 수치 연산자
	* 따옴표로 감싸진 문자열 숫자와 수치 연산을 수행했을 때, 자바는 문자열로 계산되지만, EL 은 그대로 숫자로 계산된다
	* ${"10" + 1} 의 결과는 "101" 이 아닌 11 이다
	* 만약 숫자형 문자열이 아닌데 수치 연산을 시도하면 에러를 발생시킨다
	* null 에 대한 수치 연산의 결과는 long 타입 0 이다
- 비교 연산자와 논리 연산자
	* 기호와 키워드 모두 사용 가능
- empty 연산자
	* empty <값>
	* "" 나 null 이면 true 를 반환함
	* 길이가 0 인 배열도 마찬가지
	* 이외 값이 비어 있는, 혹은 공백인 모든 컬렉션에 대해서도 true 를 반환한다
- 비교 선택 연산자
	* <수식> ? <값1> : <값2>
- 문자열 연결
	* 문자열 연결은 가능하나 호환성의 문제가 있을 수 있으므로 JAVA 나 JSP 로 처리 후 EL 을 사용할 것

### EL 활용

- page, request, session, application 스코프와 헤더에 포함된 파라미터 정보를 EL 로 표현하기 >> 각 객체에 setAttribute 하고 EL 로 찍어보기
- form 태그 post 방식으로 전달된 request 의 파라미터 정보 표현하기 >> param, paramValues
- form post enctype-multipart 방식으로 전달된 request 정보 표현
	* MultipartRequest 객체를 생성해 EL 객체에 세팅한 후 표현 (multipart 의 인자를 request, 경로, 인코딩 3개만 주면 파일 업로드 안해도 됨
- 같은 방식으로 vo 객체를 pageContext 에 세팅하고, 세팅된 vo 객체의 게터 메서드 활용해서 각 데이터 EL 표현하기
- 주의
	* 폼 전송 시 체크박스나 셀렉트 박스는 저장 및 표현에 배열 루핑이 필요함. EL 에서 루핑 자체는 불가능하므로 JSTL 이 필요함

## JSTL (JSP Standard Tag Library)

- JSP 의 표준 태그 외에 커스텀 태그를 만들 수 있다
- 그 중 많이 사용되는 것들을 모아서 표준을 만든 것이 JSTL 이다
- 그냥 JSP 요소들만을 가지고 jsp 페이지를 작성하다 보면 코드가 엄청나게 길어지고 HTML, CSS, Scriptlet, Expr, Declr 등이 뒤섞여서 알아보기가 어려워진다
- JSTL 은 이러한 불편함을 줄이고자 훨씬 간결하고 함축적으로 코드를 작성할 수 있게 한다
- JSTL 은 EL 과 함께 사용되며 변수 세팅, 흐름 제어(조건, 반복), url 처리 및 기타 코어 기능들을 지원한다

### JSTL 제공 태그 및 라이브러리 받기

- 내가 직접 만드는 태그는 아직 할 필요는 없다. 이미 나온 걸로도 충분함
- 코어, XML, 국제화, 데이터베이스, 함수 태그를 제공함
- 알아서 받아서 lib 에 추가하자

### core 태그

- 변수 지원 태그
	* 변수나 객체 프로퍼티 세팅 시 pageContext 에 포함하는 문제나, 스코프를 명확하게 지정해주는 것에 주의할 것
	* c:set 단순변수세팅
		- 속성 : var, value, scope
		- scope 영역에 해당하는 var 에 value 를 세팅
	* c:set 객체 프로퍼티 세팅
		- 속성 : target, property, value
		- target 객체에 property 이름으로 value 를 세팅
	* c:remove 선언된 변수나 객체, 객체의 프로퍼티 삭제
		- var 변수명을 지정하면 된다

## scope 이슈

- EL, JSTL 의 표현범위나 변수 및 객체 프로퍼티 범위는 page, request, session, application 의 네 범주 순으로 탐색된다
- 어차피 찾기는 하겠지만, 스코프를 확실히 명시해주면 더 빠르고 효율적으로 찾을 수 있다

----------------------------

참조: [hunit 블로그](https://hunit.tistory.com/203)

## EL (Expression Language)
> <code>&lt;%= abc %&gt;</code> 의 표현문법을 더욱 간결한 <code>${abc}</code> 방식으로 사용한다

- page 디렉티브에서 <strong>isELIgnored="false"</strong> 값을 주면 해당 페이지에서 EL 사용이 가능하다
- Attribute 형식에 대해서는 <strong>${cnt + 1}</strong> 로 쓰고 (cnt 는 임의의 변수)
- Parameter 형식에 대해서는 **${param.abc}** 로 쓴다
- 속성값을 찾을 때는 **작은 scope 에서 큰 scope** 로 찾는다 (page - request - session - application)

## JSTL (Jsp Standard Tag Library)

> JSTL 은 자신만의 Core, Formatting, DataBase, XML, Function 등의 태그를 추가할 수 있다

### JSTL Core Library

- <c:set><br/>
<c:set var="num" value="100"><br/>
<c:set var="num" value="100" scope="page">

- <c:out><br/>
<c:out value="출력할 값" default="value 가 null 값일 경우 출력할 값"><br/>
출력할 값에서 특수문자를 별도의 조치 없이 그대로 출력한다

- <c:remove><br/>
<c:remove var="변수명" scope="영역"/><br/>
만약 영역 지정을 생략할 경우 모든 영역의 해당 변수가 삭제된다

- <c:if><br/>
<c:if test="조건식" var="조건을 검사하고 return 되는 boolean 값을 저장할 변수" scope="boolean 변수가 사용될 범위"/><br/>
조건식을 검사하고, 그 결과 boolean 값을 var 에 저장하여 반환

- <c:choose><br/>
<c:choose><br/>
<c:when test="조건식"><br/>
<c:otherwise><br/>
choose 문 안에 단계적으로 포함되며, when 절은 중복 사용 가능하다. 각 조건식이 참이면 블록 내용이 실행된다.<br/>otherwise 절은 모두 false 인 경우 실행된다

- <c:foreach><br/>
<c:foreach begin="시작값" end="끝값" step="증가값" var="count 값이 저장될 변수 i">${ i }</c:foreach><br/>
<c:foreach var="변수" items="배열 or 컬렉션" /><br/>
자바에서 for 문에 해당함.

- <c:forTokens><br/>
<c:forTokens items="배열 or 컬렉션" delims="구분자" var="" begin="" end="" step="" /><br/>
자바에서 StringTokenizer 에 해당함.

- <c:catch><br/>
try  문에 해당하고 catch 에 해당하는 코드는 이 태그 밖에 ${ ~ } 방식으로 따로 작성해야 함

- 이 밖에 <c:redirect>, <c:import>, <c:url> 등이 있다








