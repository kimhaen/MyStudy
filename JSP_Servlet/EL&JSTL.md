
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190107-lightgray.svg?style=flat-square)

# EL / JSTL 한방정리!

참조: [hunit 블로그](https://hunit.tistory.com/203)

## EL (Expression Language)
> <code>&lt;%= abc %&gt;</code> 의 표현문법을 더욱 간결한 <code>${abc}</code> 방식으로 사용한다

- page 디렉티브에서 isELIgnored="false" 값을 주면 해당 페이지에서 EL 사용이 가능하다
- Attribute 형식에 대해서는 ${cnt + 1} 로 쓰고 (cnt 는 임의의 변수)
- Parameter 형식에 대해서는 ${param.abc} 로 쓴다
- 속성값을 찾을 때는 작은 scope 에서 큰 scope 로 찾는다 (page - request - session - application)

## JSTL (Jsp Standard Tag Library)

- JSTL 은 자신만의 Core, Formatting, DataBase, XML, Function 등의 태그를 추가할 수 있다

### JSTL Core Library

- <c:set>
<c:set var="num" value="100">
<c:set var="num" value="100" scope="page">








