
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190116-lightgray.svg?style=flat-square)

참조: 최범균의 JSP 2.3 웹 프로그래밍

## JSP 웹앱 개발

### 경로 문제

- 웹 프로젝트를 진행하다보면 경로를 잘못 지정해 404 에서가 나는 경우가 많다.
- 경로설정에는 절대경로, 상대경로 방식이 있으므로 충분히 이해할 것
- 개발시엔 전체 기준이 되는 메인 페이지를 지정하고 그것으로부터 각 페이지의 경로를 고려하여 개발한다
- 위에 톰캣 컨테이너와 프로젝트 의 웹 프로젝트 세팅 설정으로 Context Root 경로를 고정하고 그것을 기준으로 절대경로 개발해도 된다

### 페이지 모듈화 이슈

- 앞서 페이지 동적 로딩과 정적 로딩을 살펴봤다
- 각각 <jsp:include page="경로" /> / <@ include file="경로" /> 가 그것이다
- 동적 로드는 요청한 페이지의 출력결과만 삽입하므로 소스 충돌이 없지만, 정적 로드는 현 페이지 로딩 시 요청 페이지까지 같이 컴파일되므로 충돌 가능성이 있다
- 모든 페이지에 변함없이 적용되며 충돌 가능성이 적은 페이지 요소라면 정적으로 로드하고,
- 출력 결과만 나타내면 되는 요소에 대해서는 동적 로드를 수행하면 된다

#### index.jsp 페이지의 모듈화

- index 뿐 아니라 거의 모든 페이지에 적용될 header 와 footer 부분을 정적 로드 방식으로 모듈화한다

index.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>메인 페이지</title>
<link rel='stylesheet' href='./css/index_large.css' media='screen and (min-width: 800px)' />
<link rel='stylesheet' href='./css/index_mini.css' media='screen and (max-width: 799px)' />
<script>
	function indexMain () {
		var btnLogin = document.getElementById('btnLogin');
		var btnLogout = document.getElementById('btnLogout');
		var header = document.getElementById('title');
		
		if (btnLogin != null) {
			btnLogin.onclick = function () {
				location.href = './views/member/login.jsp';
			}
		}
		if (btnLogout != null) {
			btnLogout.onclick = function () {
				location.href = './views/member/logout.jsp';
			}
		}
		header.onclick = function () {
			location.href = 'index.jsp?aside=./views/intro.jsp&content=./views/intro.jsp';
		}
	}
</script>
</head>
<body>
	
	<%
		/*
		* 페이지 로드 시 aside 의 값이 무엇인지 검증하는 스크립트릿
		*/
		request.setCharacterEncoding("utf-8"); // 인코딩
		String aside = request.getParameter("aside");
		String content = request.getParameter("content");
		String component = "./views/dateObj.jsp";
		if (aside == null) {
			aside = "./views/intro.jsp";
		}
		if (content == null) {
			content = "./views/intro.jsp";
		}
	%>
	
	<div id='log'>
		<%
			if (session.getAttribute("name") == null) {
		%>
				<input type='button' id='btnLogin' value='login' />
		<%
			} else {
		%>
				<b><%=session.getAttribute("name") %></b> 님 환영합니다&nbsp;
				<input type='button' id='btnLogout' value='logout' />
		<%
			}
		%>
	</div>
	
	<%@ include file="./views/main/header.jsp" %>

	<nav>
		<div id='component'>
			<jsp:include page="<%=component %>" />
		</div><br/>
		<ul id='menu'>
			<li>HTML</li>
			<li>CSS3</li>
			<li>JS</li>
			<li>Ajax</li>
			<li>MyBatis</li>
			<li>jQuery</li>
			<li><a href="index.jsp?aside=./views/index_jsp.jsp">JSP</a></li>
			<li>Spring</li>
		</ul>
	</nav>
	<section id='wrap'>
		<aside id='aside'>
			<h1>
				<jsp:include page="<%=aside %>" />
			</h1>
		</aside>
		<article id='content'>
			<h1>
				<jsp:include page="<%=content %>" />
			</h1>
		</article>
	</section>
	
	<%@ include file="./views/main/footer.jsp" %>

	<script>indexMain();</script>

</body>
</html>
```

header.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>header page</title>
</head>
<body>

	<header id='header'>
		<h1 id='title'>My Home Page</h1>
	</header>

</body>
</html>
```

footer.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>footer page</title>
</head>
<body>

	<footer id='footer'>
		<h1>Copyleft: daesungRa</h1>
	</footer>

</body>
</html>
```

- 가장 주의할 점은 소스 충돌 여부와 적절한 경로 설정 문제이다
- 충분히 고려해서 잘 짜볼 것 (정적 삽입은 변수 사용 불가. 문자열을 삽입해줘야 한다)
- 개선할 점 : 사실상 nav 부분도 header 에 포함되어야 하지만, 시계 컴포넌트가 충돌이 일어나는것 같아 header 와 함께 모듈화하지 못함

### 버퍼 활용

- page 디렉티브로 설정한다
- 일반적으로 8k 사이즈가 사용되며
- 출력 내용이 이 사이즈를 넘어갈 시 autoFlush = true 옵션을 사용한다

### 개발한 웹앱 배포하기

- 현재는 eclipse 에서 하고 있지만, 개발 소스를 배포했을 때는 어떤 환경에서 그것이 활용될지 알 수 없다
- 게다가 eclipse-tomcat container 환경은 제대로 된 웹서버 환경이라고 볼 수 없다
- 로컬에서 개발을 마치거나 유지보수가 이루어졌을 때 그 결과를 

#### 배포 과정 (외부 톰캣 활용-웹서버)

- 톰캣 installer 로 설치 후 ID, PWD 지정 (계정 찾기는 conf 폴더 내 tomcat-users.xml 파일을 확인한다)
- eclipse 톰캣 컨테이너 중지 (동일한 포트번호를 사용한다고 가정)
- configure tomcat 실행 후 service status 를 start 로 설정
- 톰캣 컨테이너가 구동되면, Tomcat Manager 실행 후 로그인 한다 >> 외부 톰캣 서버 구동(웹서버 구동)
- 서버 컨테이너 메인 화면을 보면, 현재 컨테이너에 적재된 웹 프로젝트들이 보인다 (여기서 구동, 중지, 제거 등)
- 여기서 war 파일을 Deploy 하는 것
	- export 로 war 파일 생성
	- 이클립스 해당 프로젝트에서 export-war export 클릭
	- war 를 생성하고자 하는 경로를 지정하고 finish 클릭 (소스 포함할건지, 오버라이트 할건지 옵션 선택)
	- 이제 톰캣 매니저 페이지의 Deploy 항목에 생성한 war 파일을 추가하고 구동시키면 된다
- 여기까지가 이클립스-톰캣 에 관계없이 외부 서버에서 웹앱을 구동시키는 절차이다

### 페이지 이동 처리

- 브라우저가 특정 페이지를 요청하면, 서버는 요청된 페이지의 흐름에 따른 결과를 브라우저에 응답한다
- 만약 요청된 페이지 중간에 '페이지 이동 처리' 부분이 있다면, 최종적으로 웹 브라우저는 이동된 페이지의 내용을 나타내게 될 것이다

#### forward

- jsp 문법으로는 forward 액션 태그가 있다
- <jsp:forward page="이동할 페이지" /> 방식으로 사용된다. 이것의 결과 요청한 페이지의 내용이 사라지고 이동할 페이지의 내용이 버퍼로 응답된다
- 브라우저 입장에서 페이지 URI 는 요청 페이지 주소이지만, 나타난 화면은 '이동할 페이지' 의 내용이다. 사용자는 내부 내용을 알 수 없다
- 출력 버퍼와의 관계
	* 버퍼를 사용한다면, 요청 페이지 내용이 버퍼에 저장되다가 forward 액션 태그를 만나는 순간 버려진다
	* 그리고 '이동할 페이지' 의 내용이 새롭게 버퍼에 저장된 후 응답으로 전송된다
	* 만약 버퍼 내용이 flush 된 후거나 버퍼를 아예 사용하지 않는 경우에 forward 부분을 만난다면,
	* 서버 컨테이너에 따라 다르겠지만 일반적으로는 500 에러가 발생된다

#### sendRedirect

- sendRedirect 메서드는 response 객체를 기반으로 실행된다
- 브라우저의 요청에 응답하는 개념이기 때문
- 이 메서드는 그냥 페이지 자체를 이동시키는 것이므로 이해가 쉽다. 브라우저 입장에서는 URI 자체까지도 변경되므로 페이지 변경 인식 가능

#### 전달되는 데이터 보존 유무

- forward 방식은 보존되고 redirect 메서드는 보존되지 않는다
- 왜 그런지는 페이지 요청이 각각 몇번 수행되는지를 고려해서 잘 생각해볼 것

## JAVA Bean 과 JSP 의 useBean 액션 태그

- 웹 프로젝트에서 빈번하게 사용되는 데이터 모음 객체를 빈(Bean) 이라고 한다
- 보통 자바 클래스 소스로 작성하고 필요에 의해 인스턴스화해서 사용하는데,
- JSP 에서 빈을 활용하는 방법은, jsp:useBean 태그로 scope 에 적용되는 빈을 생성하는 것이다
- 사용법

```HTML
...
<jsp:useBean id="[빈 객체 이름]" class="[패키지.빈 클래스]" scope="page"/>
<jsp:setProperty name="[빈 객체 이름]" property="*" />
...
```

- 빈 클래스에 해당하는 빈 객체를 만들고 이름을 지정,
- setProperty 태그를 활용해 requst 객체의 파라미터에 포함된 모든 값을 세팅한다
- 빈 객체의 적용 범위는 page 이다



















