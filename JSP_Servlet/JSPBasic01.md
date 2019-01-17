
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190116-lightgray.svg?style=flat-square)

참조: 최범균의 JSP 2.3 웹 프로그래밍

## JSP

> MVC 에서 View 단으로 많이 활용되는 JSP 이다

### 전체적인 구조

- JSP 페이지에는 View 를 위한 HTML, CSS, Javascript, JSP 소스가 혼재한다
- 게다가 JSP 문법 내에는 Java 코드도 포함될 수 있다
- 백엔드와 관련된 핵심적인 처리는 웹서버 컨테이너, 즉 서블릿이 수행하지만,
- 프론트 단에서 간단한 데이터 정제작업은 JSP 페이지에서 JS 코드를 활용해 수행한다(요즘은 이것도 굉장히 정교하다)
- 웹앱의 기본 구조
	* ContextPath : 웹앱의 Root. 현재는 WebContent 폴더로 표현됨
		- 톰캣 컨테이너의 server.xml 설정파일과 웹 프로젝트의 Properties 의 Web Project Settings 설정으로 Context Root 를 지정할 수 있다
		- 환경설정 정보를 제외한 HTML, CSS, JS, JSP, 이미지 등의 기타 웹앱 View 요소들을 주제별로 폴더링하여 저장
	* /WEB-INF : 각종 환경설정 정보들이 위치함
		- web.xml : 해당 웹 프로젝트의 환경설정 정보 기록. 서블릿 초기화 파라미터, 각종 매핑, 세션 유효시간, 리스너/필터 설정 등등
		- /classes : 컴파일된 클래스 실행소스가 위치함
		- /lib : 웹서버 구동에 필요한 라이브러리 (jdbc 같은) 가 위치한다
	* Java Resources : 서버의 동적 처리를 위한 서블릿 소스코드가 위치함 (패키지 지정)

### JSP 페이지 구성요소

- 지시자-페이지 디렉티브 : 페이지에 대한 정의 및 환경설정
- 스크립트 요소-스크립트릿 : 간단한 JSP (Java) 로직 수행
- 스크립트 요소-표현식 : HTML 코드 중간에서 데이터 표현
- 스크립트 요소-선언문 : 페이지 내 전역형 멤버 선언
- 중요한 객체들-request, response
	* 뷰 페이지에서 사용자의 요청정보가 담겨 서버로 전송, 서버의 응답내용이 담겨 사용자에게 전달됨(적절한 페이지 처리도 포함)
	* 전달 값에 대한 처리 : getParameter, getParameterValues 등의 파라미터 관련 메서드를 사용하면 된다

#### request, response

- request : 사용자의 요청 정보가 파라미터로 담긴다. Get 방식이라면 요청 헤더에, Post 방식이라면 바디에 파라미터 값이 포함된다
- response : 요청받은 데이터를 처리하고 응답을 브라우저에 전송한다

- 액션 태그
	* include, param, forward, useBean 등의 액션 태그를 사용한다
	* 정적 include
		- jsp:include page="[페이지 경로]" 코드는 현재 페이지 중간에 요청한 페이지의 '출력결과(코드 아님)'만을 삽입하므로 코드상 충돌이 없다
	* 동적 include
		- jsp:include file="[페이지 경로]" 코드는 현재 페이지 중간에 요청한 페이지의 '소스코드'를 삽입하므로 코드상 충돌이 발생할 수 있다
	* jsp:include 액션태그 내에 jsp:param 액션태그로 파라미터 속성을 지정할 수도 있다

#### jsp:include 액션 태그로 로드한 페이지는 자신을 호출한 원본 페이지의 request 파라미터 값을 공유한다

- 일단 form.jsp 페이지의 form 태그에서 각 파라미터(aside, content, name, address, pets[]) 값을 입력한 뒤 post 방식으로 index.jsp 페이지를 요청한다
- 호출된 index 페이지가 로드되며 HTTP 바디에 담긴 파라미터 정보를 토대로 페이지 구성을 한다
- 이때 content 파라미터 경로값이 viewParam.jsp 로 되어 있으므로, index 페이지의 content 부분에 viewParam 정보를 jsp:include 한다
- include 된 viewParam 페이지는 name, address, pets 파라미터를 필요로 하는데,
- 자신이 로드된 index 원본 페이지 내에 각 파라미터 값이 남아 있으므로, 그것을 활용해 페이지를 구성한다
- 이런 삽입 방식을 동적 페이지 include 라 하며 삽입하고자 하는 페이지의 '출력 결과'를 로드한다고 보면 된다

#### 페이지 중간에 include 하는 또다른 방식 - include 디렉티브

- include 디렉티브는 정적 삽입시에만 사용한다
- &lt;@ include file="[include 할 파일명]" &gt; 방식으로 사용함
- 파일명에는 String 문자열만 가능, 변수명을 대입하는 것은 안 된다
- 왜냐하면 동적 삽입이 아닌, 정적 삽입이기 때문이다 >> 동일 코드에 대한 충돌 발생 가능
- 이 방식에서는 최초 페이지 로드 시 기존의 페이지소스에 include 하는 페이지 소스가 뒤섞여서 컴파일됨
- 동적 삽입은 출력 결과만 삽입되므로 충돌 가능성 없음

#### 동적 삽입과 정적 삽입을 이용한 페이지 구성

### 리다이렉트

- JSP 에서 페이지 이동에는 redirect 와 forward 두 방식이 있는데, 전자는 새로운 페이지 요청, 후자는 기존 페이지에 요청 페이지 로딩으로 요약 가능하다
- response.sendRedirect("[이동할 페이지]")
	* 이것은 브라우저의 페이지 자체를 이동시키는 것이다. (이동할 페이지를 다시 호출하여 나타냄)
	* 브라우저의 요청은 서버에서 처리되어 서버는 브라우저에게 sendRedirect 로 지정한 페이지에 대한 정보를 넘기고 (이 페이지로 넘어가라!),
	* 브라우저는 그 정보를 바탕으로 서버에 다시 요청을 보내 최종적인 페이지 응답을 받게 된다
- forward
- 그러니까 redirect 는 브라우저가 서버에 총 두 번 페이지요청을 하고, forward 는 한 번만 한다. 그것도 서버 컨테이너 내에서만 처리되므로 URI 가 바뀌지는 않음

### cookie, session

### 로그인 로직 만들기


#### 웹앱 만들때 고려할 것 세 개

- 로그인 및 세션
- 게시판
- 디비

### 요약

#### JSP 정보 scope

- application : 웹앱 전체에 적용
- session : 접속중인 현재 유저에게 적용
- request : 특정 요청에 한한 정보
- page(pageContext) : 특정 페이지에 적용

#### 정보 setting and getting

- 특정 속성에 특정 값 삽입 : [scope].setAttribute(key, value)
- 특정 속성의 값 얻기(형변환 필) : Object [scope].getAttribute(key)
- 특정 속성의 값 삭제(초기화) : [scope].setAttribute(key, null)
- 특정 속성 자체를 삭제 : [scope].removeAttribute(key)









