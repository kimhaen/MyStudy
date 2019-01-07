
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190107-lightgray.svg?style=flat-square)

# EL / JSTL 한방정리!

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








