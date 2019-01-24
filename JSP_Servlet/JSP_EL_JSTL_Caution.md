
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190107-lightgray.svg?style=flat-square)

## EL, JSTL 사용상 헷갈려서 발생하는 에러

- JSP 페이지에서 JSP 코드 뿐만 아니라 HTML, EL, JSTL 등의 표현어 및 라이브러리들이 뒤섞이기 때문에 (이것 자체도 경량화 한다고 한거지만..)
- 처음 배우는 입장에서 마구 헷갈린다. 물론 숙련되면 쉽겠지만, 그때까지는 자잘한 문법적 오류나 오타들로 인한 에러들을 기록해 둔다

### 표현어 자체가 따옴표 사이에 위치할 때

- JSP 나 EL 표현어가 태그나 태그 라이브러리의 속성값으로 따옴표에 둘러싸여 코딩되는 경우가 있다
- 이때, 그냥 단순 표현이면 상관없지만 표현어 내부에서도 String 인자값에 따른 결과값을 출력하는 경우, 같은 따옴표로 인해 혼선이 발생한다
- 직접 살펴보면 쉽게 알 수 있다

```HTML
	...
	<c:set var="id" value="<%=(String)session.getAttribute("id") %>" scope="page"></c:set>
	<c:set var="name" value="<%=(String)session.getAttribute("name") %>" scope="page"></c:set>
	...
```

- 위 코드는 JSTL 세팅 태그의 변수값을 세션으로부터 얻어오는 코드다
- 일면 문제 없어 보이지만, 큰따옴표에 둘러싸인 Expression 내부에도 인자값 대입을 위한 큰따옴표가 또 있다. 그럴때는 무엇이 표현어인지 구분이 되지 않아
- Attribute value [(String)session.getAttribute("id") ] is quoted with ["] which must be escaped when used within the value
- 에러를 발생시킨다
- 그럴때는 이스케이프 문자로 처리해주면 된다

```HTML
	...
	<c:set var="id" value="<%=(String)session.getAttribute(\"id\") %>" scope="page"></c:set>
	<c:set var="name" value="<%=(String)session.getAttribute(\"name\") %>" scope="page"></c:set>
	...
```













