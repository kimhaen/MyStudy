
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-181231-lightgray.svg?style=flat-square)

	* 이 글은 JSP 에서 쿠키와 세션을 생성하고 활용하는 방식을 기록한다

## HTTP 에 대한 간단한 고찰

> 서버/OA클라이언트 통신 구조에서 사용되는 HTTP 프로토콜에서는, 서버와 브라우저 간 지속적인 연결이 필요하다. 브라우저의 request 와 서버의 response 로 운영되는 HTTP 는 어떤 데이터든 transfer 할 수 있는 강력한 도구이지만, 지속적인 연결 자체가 성립되지 않는다면 아무런 힘을 발휘할 수 없을 것이다. 여기서 지속적인 연결을 가능하게 하는 것이 바로 쿠키와 세션이다.

- HTTP 자체는 Connectless and Stateless 프로토콜이다. 비연결지향이라고 해석할 수 있는가의 문제에서는, 요청-응답의 한 사이클을 돌고 나면 연결 및 상태정보를 버린다는 의미에서 타당하지만, 전송 계층의 TCP 프로토콜 위 응용 계층에서 동작한다는 의미에서 목적지가 분명한 연결지향이라고 볼 여지도 있다(TCP 는 연결 지향 프로토콜).<br/>어쨌든 HTTP 는 통신 후 정보를 버리기 때문에 대량의 클라이언트 요청 처리에 효율적이고, 전송되는 데이터들은 하이퍼텍스트(문서참조) 통신규약에 따라 운용된다.

- 앞서 언급한 것처럼 접속 클라이언트의 이전 정보가 남아있지 않으므로 연결의 지속성을 위해 서버 측이든 클라이언트 측이든 별도로 정보를 저장해둘 필요가 있다. 이를 위해 처음에는 쿠키를 사용했지만, 최근에는 보안 및 저장성의 측면에서 세션을 주로 사용한다.

## Cookie

> 최초 클라이언트 브라우저의 접속요청 시 서버는 request 정보 및 서버정보를 토대로 쿠키를 생성한다. 생성된 쿠키는 response 객체에 담겨 브라우저로 전송되며, 클라이언트측에 저장 및 재활용(또다른 요청 시 재전송)된다. 서버는 이 쿠키정보를 통해 동일한 유저의 request 임을 식별한다.

- 쿠키의 세 가지 목적
	1. Session management
		* Logins, shopping carts, game scores, or anything else the server should remember
	2. Personalization
		* User preferences, themes, and other settings
	3. Tracking
		* Recording and analyzing user behavior

- 쿠키 사용하기

	* 일반적인 웹 프로젝트에서는 request 시 서버 측 JAVA 코드에서 Cookie 객체로 생성되고, 그것이 response 객체에 담겨 송신된다.

	<pre>
	CookieSet.jsp
	```JAVA
	<%
		Cookie cookie = new Cookie("CookieName", "CookieValue");
		cookie.setMaxAge(60 * 60);

		response.addCookie(cookie);
	%>
	```
	</pre>

	* 클라이언트가 CookieSet.jsp 페이지로 request 객체를 전송하면, 이 페이지는 쿠키 객체를 생성하여 각각 "이름" 과 "값" 을 세팅한다 (시간은 1 시간으로 세팅). 이후 cookie 객체는 response 객체에 담겨 전송된다.
	* 응답을 받은 클라이언트는, request.getCookies() 메서드로 쿠키를 얻고, 게터로 정보를 확인할 수 있다.

- 쿠키의 단점
	1. 저장용량의 한계	: 쿠키는 클라이언트의 브라우저가 설치된 경로에 저장되므로, 그 저장용량에 한계가 있다. 한 클라이언트 당 쿠키는 최대 300 개, 사이즈는 4KB 까지 저장 가능하다.
	2. 보안상의 이유	: 쿠키 객체 자체가 response 에 담겨 송수신된다는 것 자체가 보안상의 취약성을 의미한다. 게다가 클라이언트 단에 저장된 정보는 쉽게 탈취될 가능성이 있다.

## Session

> 세션은 쿠키와 같이 HTTP 프로토콜 환경에서 서버와 클라이언트 간의 접속 상태를 유지하기 위한 수단으로 활용된다. 그것을 위해서는 접속 및 유저정보를 저장해둬야 하는데, 쿠키가 클라이언트 측에 저장되는 것과 달리 세션은 서버측에 저장되어 보안 및 용량의 측면에서 상대적으로 낫다.

- 세션의 동작방식
	1. 클라이언트의 접속 요청 시 접속 유지와 사용자 식별을 위해 세팅된 방식으로 세션 생성(session-id 발급). 세션 아이디는 쿠키를 사용해 서버와 클라이언트 양측에 저장된다. 만약 요청 시 클라이언트가 request-header field 인 cookie 에 session-id 를 보내왔다면, 기존 접속이 존재하는 것이므로 새로 생성하지 않는다.
	2. 생성된 세션 아이디는 클라이언트 측에 발행(응답)되고, 만약 두 번째 이상의 요청이라면 기존 아이디를 사용한다.
	3. 클라이언트가 접속을 종료한다면, 서버에 저장된 session-id 는 소멸된다

## 쿠키와 세션의 차이 곱씹어보기

- 둘 다 생명시간이 정해져 있지만, 쿠키정보는 클라이언트 측에 저장되므로 소멸되지 않고 잔류할 수도 있다. 반명 세션 정보는 서버측에서 관리되므로 바로 폐기된다.
- 속도 면에서 쿠키에 접속정보가 담겨 있다면 그만큼 속도가 빠른 것. 세션은 정보가 서버에 있으므로 한 번 이상 검증해야 한다.
- 저장성 측면에서 쿠키는 상당히 제한적이다(300 개, 4kb). 그러나 세션은 서버에 저장되므로 제한이 없다.
- 보안의 측면에서 세션의 경우 클라이언트에는 세션 아이디만 저장되므로, 모든 접속정보가 클라이언트에 존재하는 쿠키에 비해 안전하다.
- 세션은 아무래도 서버 측에 저장되기 때문에 무분별하게 생성할 수 없다. 그러므로 쿠키를 사용해야 하는 경우가 발생할 수도 있다.



참조 : <https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies>, <https://jeong-pro.tistory.com/80>, <http://mohwaproject.tistory.com/entry/HTTP-Session-%EC%9D%B4%EB%9E%80>




