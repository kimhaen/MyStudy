
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190114-lightgray.svg?style=flat-square)


## 실습 - JSP 로만 만든 웹 페이지

> 메인 페이지와 로그인 및 로그아웃 로직 만들기

- index~login

### 구성

#### 페이지 호출 - include

- 일단은 모든 흐름을 JSP 로만 만들었기 때문에 HTML, JS, JSP 코드로 view, control, service 로직이 혼재한다
- 구성은 간단하게 index 페이지를 만들고 메뉴 리스트 클릭 시 해당 내용을 화면에 추가해서 출력한다
- 화면에 추가 시 aside, content 영역에 추가되며 aside, content 를 JSP 페이지 변수로 만들어 동적으로 초기화한다.
- 이 변수는 로드하고자 하는 페이지 경로를 String 타입으로 저장하고, jsp:include 액션 태그를 활용해 페이지 중간에 원하는 페이지를 삽입하는 식으로 한다
- 예를 들어 최초 페이지 로딩 시에는 intro.jsp 페이지 경로가 aside, content 에 저장되어 각각 include 되고,
- 이후 메뉴 바에서 원하는 항목을 클릭하면 index.jsp 페이지가 다시 호출되면서 매개변수로 그것에 해당하는 페이지 정보가 넘어와 aside, content 변수에 담긴 후 include 된다
- index 페이지가 호출되며 파라미터가 각 변수에 담기는 과정은 request.getParameter() 메서드를 활용한다

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
	
	<header id='header'>
		<h1 id='title'>My Home Page</h1>
	</header>
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
	<footer id='footer'>
		<h1>Copyleft: daesungRa</h1>
	</footer>

	<script>indexMain();</script>

</body>
</html>
```

이 페이지에는 로그인 여부 판별, 그에 따른 버튼 처리, view 및 동적 페이지 구성 등의 로직이 존재한다
현재는 메뉴 바 중에서 JSP 항목만 페이지 로드 후 적재가 가능하며, JSP 의 각 리스트에 대한 페이지 로드 내용은 content 영역에 적재된다
session 의 name 속성 검증 후 로그인 혹은 로그아웃 버튼이 나타난다

#### 로그인과 로그아웃

- 로그인 여부 판별은 session 정보를 활용한다
- 일반적으로 session 객체는 브라우저의 접속요청 시에 생성된다
- index 페이지가 로드될 때, session 객체의 name 속성의 값이 not null 이라면, 로그인된 상태라고 판단, logout 버튼만 활성화되고
- session name 속성값이 null 이라면 (혹은 name 속성 자체가 없다면) 로그인이 안된 상태라고 판단, login 버튼만 활성화된다
- 각 버튼을 클릭하면 login 혹은 logout 페이지로 이동된다

login.jsp
```HTML
<%@page import="java.sql.ResultSet"%>
<%@page import="java.sql.PreparedStatement"%>
<%@page import="com.myweb.memberBean.DBConn"%>
<%@page import="java.sql.Connection"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login Page</title>
<script>
	function funcLogin () {
		var frm = document.frm;
		var id = document.frm.id.value;
		var pwd = document.frm.pwd.value;
		
		if (id == '') frm.id.focus();
		else if (pwd == '') frm.pwd.focus();
		else frm.submit();
	}
</script>
</head>
<body>

	<div id='login'>
		<h1>gooogle</h1>
		
		<!-- 별도의 action 속성이 없으면, 데이터가 자기 자신 페이지로 전송됨 -->
		<form name='frm' method='POST'>
			<label>아이디 : </label>
			<input type='text' name='id' /><br/>
			<label>비밀번호 : </label>
			<input type='text' name='pwd' /><br/>
			<input type='button' value='로 그 인' id='btnLogin' onclick='funcLogin()'/>
			<p/>
			
			<a href='#' >아이디 찾기</a>
			<a href='#' >비밀번호 찾기</a>
			<a href='insert.jsp' >회원가입</a>
		</form>
		
		<%
			if (request.getMethod().equals("GET")) return;
			// 순수 JSP 를 사용한 DB 연결
			String id = request.getParameter("id");
			String pwd = request.getParameter("pwd");
			
			Connection conn = new DBConn().getConn();
			PreparedStatement ps = null;
			ResultSet rs = null;
			
			String sql = " select * from member where id = ? and pwd = ? ";
			
			try {
				ps = conn.prepareStatement(sql);
				ps.setString(1, id);
				ps.setString(2, pwd);
				rs = ps.executeQuery();
				if (rs.next()) {
					out.println("방가방가!");
					String name = rs.getString("name");
					session.setAttribute("name", name);
					response.sendRedirect("/index.jsp");
				} else {
					out.println("안 방가..");
				}
			} catch (Exception ex) {
				out.println(ex.toString());
			}
			
		%>
	</div>

</body>
</html>
```

logout.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Logout Page</title>
</head>
<body>

	<div id='logout'>
		<h1>Logout Page</h1>
		
		<%
			session.setAttribute("name", null);
			response.sendRedirect("/index.jsp");
		%>
	</div>

</body>
</html>
```

- 로그인 확인은 세션의 name 속성으로 하므로,
- 로그인 시 DB Connection 으로 유저의 존재를 확인하고 있으면 세션에 name 속성을 세팅하고 없으면 로그인 실패 메시지를 나타낸다
- 로직이 완료되면 sendRedirec("/index.jsp") 로 되돌려보낸다 (로그아웃도 마찬가지)
- 로그아웃은 세션의 name 속성값을 null 로 초기화하고 되돌려보낸다
- 그럼 index.jsp 페이지는 현재 세션의 name 속성값이 null 이므로 로그아웃 상태임을 확인한다

**초기화면**
![index.jsp - 초기화면](https://github.com/daesungRa/MyStudy/blob/master/imgs/MyWebProject/index-init.PNG)

**aside, content 페이지 로드 후 적재**
![index.jsp - aside, content 페이지 로드 후 적재](https://github.com/daesungRa/MyStudy/blob/master/imgs/MyWebProject/index-loadPage.PNG)

**로그인 화면**
![로그인 화면](https://github.com/daesungRa/MyStudy/blob/master/imgs/MyWebProject/login.PNG)

**로그인 성공 시 활성화 화면**
![로그인 후 화면](https://github.com/daesungRa/MyStudy/blob/master/imgs/MyWebProject/login-success.PNG)

**로그아웃 시 활성화 화면**
![로그아웃 시 되돌아감](https://github.com/daesungRa/MyStudy/blob/master/imgs/MyWebProject/logout-result.PNG)


## 이슈

1. 빈번히 사용되지만 다소 복잡한 로직은 어떻게 할까?

- HTTP 프로토콜은 비저장성이다
- 그러므로 웹 페이지마다 일일히 데이터 교환 로직을 만들지 않으면 페이지 이동 시 값이 그냥 버려진다
- 하다못해 자기 페이지를 리로드 해도 지금까지 입력한 데이터는 남아 있어야 한다
- 이런 점이 개발자로 하여금 많은 스트레스를 받게 한다
- 어쨌든 데이터 보존 이슈는 매우 중요하므로, 필요에 따라 빨리 사용할 수 있도록 사전에 외워둬야 한다
- 이해하는 것도 필요하긴 하지만, 외워서 빠르게 사용할줄 아는 것도 필요하다

2. 톰캣 컨테이너에 대한 인코딩 설정

- server.xml 파일에 설정하기

3. DB 커넥팅

- jdbc 라이브러리 세팅 방식
	* 웹앱 프로젝트의 Build Path 라이브러리 목록에 사용할 jdbc 라이브러리 추가
	* 웹앱/WEB-INF/lib 폴더 내에 jdbc 라이브러리가 위치하도록 세팅
	* 전자는 현재 실행중인 이클립스 프로젝트 내에서만 적용되고, 후자는 추후 어플을 배포할 때 jdbc 도 함께 배포된다

4. 로그인 상태 유지, 일회용 로그인 등 처리

- 쿠키 사용하기
- HTML5 구현 서버라면, local storage 에 해당 유저의 상태값 저장
	* session storage 는 브라우저를 종료하면 사라지지만 local storage 는 남아있는다

5. CSS - index 페이지 자동 사이즈 조절

6. 경로 문제

- 웹 프로젝트를 진행하다보면 경로를 잘못 지정해 404 에서가 나는 경우가 많다.
- 경로설정에는 절대경로, 상대경로 방식이 있으므로 충분히 이해할 것
- 개발시엔 전체 기준이 되는 메인 페이지를 지정하고 그것으로부터 각 페이지의 경로를 고려하여 개발한다
- 위에 톰캣 컨테이너와 프로젝트 의 웹 프로젝트 세팅 설정으로 Context Root 경로를 고정하고 그것을 기준으로 절대경로 개발해도 된다

7. JSP 코드가 컴파일된 결과를 보고 싶다면?

- D:\1806_Ra\Workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\work\Catalina\localhost















