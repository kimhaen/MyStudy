
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190114-lightgray.svg?style=flat-square)

참조: 최범균의 JSP 2.3 웹 프로그래밍

## 서블릿이란?

- JSP 표준이 나오기 전에 만들어진 자바 웹앱 개발 표준
- 자바 클래스를 이용해 웹앱을 개발하게 된다
- 개발순서
	* 규약에 따라 코드 작성 후 컴파일
	* 생성된 클래스파일을 /WEB-INF/classes 폴더 하위 지정된 패키지에 위치시킨다
	* web.xml (웹 어플리케이션 환경설정 파일) 에 서블릿 클래스 설정
	* 컨테이너(톰캣 등)를 실행해 웹 브라우저에서 확인
- JSP 에 비해 구조가 복잡하나, MVC 패턴을 지원하는 스프링과 같은 프레임워크는 서블릿 기반인 경우가 많기 때문에 꼭 습득해야 한다

## 예제 프로젝트 생성 및 서블릿 소스코드 작성

- 이클립스에서 Dynamic Web Project 생성
- 서블릿 소스코드는 src 폴더 하위에 패키지와 함께 위치하며, HttpServlet 클래스를 extends 해야 한다
- 그리고 처리하고자 하는 HTTP 방식(method)에 따라 알맞은 메서드를 구현해야 하는데, doGet() 과 doPost() 가 그것이다
- 오버라이딩한 각 메서드는 HttpServletRequest 와 HttpServletResponse 두 개의 파라미터를 갖는데,
- 이는 각각 JSP 로부터 요청된 request 객체와 응답을 위한 response 객체 이다
- 적절한 언어 호환성을 위한 캐릭터 셋을 지정한다. 일반적으로 utf-8 이며, 이는 요청 객체가 넘어온 JSP 의 page 디렉티브의 그것과 동일하다
```JAVA
response.setContentType("text/html; charset=utf-8");
```
- 마지막으로 각 요청 및 응답에 대한 적절한 로직을 작성한다

## web.xml 로 매핑하기

- 그 다음 작업은 ContextPath 하위 WEB-INF 폴더의 web.xml 환경설정 파일에 작성한 서블릿 클래스를 등록하는 것이다
- 서블릿 클래스 등록 설정은 다음과 같다 (그 외 기본 설정에 대한 부분은 기존의 코드를 가져다 사용하면 된다)

```XML
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmls: ...
...
	<servlet>
		<servlet-name>[이름 지정]</servlet-name>
		<servlet-class>[패키지 경로].[서블릿 클래스]</servlet-class>
	</servlet>

	<servlet-mapping>
		<servlet-name>[위에서 지정한 이름]</servlet-name>
		<url-pattern>/[매핑시킬 URL]</url-pattern>
	</servlet-mapping>
...
</web-app>
```

- 위 서블릿 설정을 보면 알 수 있듯이 먼저 목적 서블릿 전체 경로에 네이밍을 하고, 지정한 이름에 대한 url 매핑 설정을 한다
- 이제 사용자가 매핑된 url 패턴으로(컨텍스트 경로 기준) 접속을 시도하면, 그것이 설정된 서블릿 클래스가 그 요청을 처리한다
- 또한 같은 서블릿 클래스 네임에 대해 여러 개의 url 패턴을 지정할 수 있다

## annotaion 으로 매핑하기

- 서블릿 3.0 버전부터는 '@WebServlet' annotaion 을 사용해 특정 서블릿 소스에 웹앱 등록을 설정할 수 있다
- 이를 지원하는 톰캣 등의 컨테이너 버전은 웹앱 실행 시 이 어노테이션이 적용된 클래스를 자동으로 탐색, 등록한다

annotation 으로 매핑한 서블릿 클래스 소스 예시
```JAVA
...
@WebServlet(urlPatterns = "/[매핑할 url]")
public class [서블릿 클래스명] extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest request, HttpServletResponse) throws ServletException, IOException {
		...
	}

	@Override
	protected void doPost(HttpServletRequest request, HttpServletResponse) throws ServletException, IOException {
		...
	}

}
...
```

- 위와 같은 방식으로 매핑된 서블릿 클래스는, urlPatterns 에 명시된 경로 해당하는 요청을 처리한다

## 두 매핑 방식의 비교

- 각각의 방식으로 서블릿 클래스 매핑 등록설정을 한 후 한참 개발이 진행되고 있다고 가정하자
- 중간에 어떤 이유로 인해 매핑 url 경로명을 변경해야 할 경우가 발생할 수 있는데,
- 이때 annotation 설정한 경우는 직접 해당 클래스파일에 찾아가 수정 후 재컴파일을 해야 하는 반면,
- web.xml 방식은 환경설정 파일의 일부만 수정하면 되므로 상대적으로 간편하다 (유지보수에도 유리하다)
- 그러나 만약 변경 가능성이 매우 희박하고 웹앱 내에서 범용적으로 사용되는 서블릿이라면,
- web.xml 소스를 경량화하고 더이상 신경쓰지 않아도 되도록 하기 위해 annotation 방식을 고려할 수도 있다

#### 매핑 경로 설정 시 유의할 점

- 간혹가다 웹앱 프로젝트 자체의 ContextPath 가 "/" 루트가 아닌 프로젝트명과 같은 방식으로 지정된 경우가 있다
- 이 때 위와 같은 방식으로 매핑하면(/[매핑경로]) 404 에러가 발생하므로, ContextPath 를 의도에 맞게 적절히 지정하는 것이 필요하다

#### 어노테이션 적용된 서블릿을 web.xml 에 또다시 등록한다면?

- 결과적으로 각각의 방식에 따른 두 개의 클래스 객체가 생성된다.
- 그러므로 어노테이션과 web.xml 설정은 서로 다른 객체를 참조하게 된다

## HTTP 각 방식별 구현 메서드 (GET, POST)

- HTTP 는 GET, POST, HEAD, PUT, DELETE 방식의 메서드를 지원하는데, 일반적 웹 브라우저에서는 앞의 두 개를 주로 사용한다
- 각 방식은 서블릿 클래스 내에서 doGet, doPost 메서드를 이용해서 처리된다
- Get 방식 : 브라우저 전송 데이터가 HEAD (url 말미) 에 포함된다. 용량 제한이 있으며 보안에 취약하다
- Post 방식: HEAD 가 아닌 BODY 끝단에 포함되어 전송되어 보안성에 유리하고 용량 제한이 적다

## 서블릿 로딩과 초기화

- 서블릿 컨테이너는 최초 웹앱이 실행될 때, 환경설정 및 어노테이션 정보를 토대로 각 서블릿 객체를 생성하고 등록 및 매핑작업을 수행한다
- 이후 요청에 대해서는 최초 생성된 서블릿 객체를 활용한다
- 서블릿 로딩
	* 컨테이너가 서블릿 객체 생성 후 init() 메서드를 호출하는 과정을 '서블릿 로딩' 과정이라고 한다
	* 개발자는 서블릿 생성 시 필요한 작업을 init() 메서드를 오버로딩 (재정의) 하는 방식으로 지정할 수 있다
	* 만약 환경설정 매개변수 (ServletConfig) 가 있는 init() 메서드가 존재한다면, 그것은 자신의 작업을 수행 후 매개변수가 없는 init() 까지 실행시킨다
- 서블릿 생성에서부터 init() 실행까지 이어지는 서블릿 로딩 작업은 일정 시간이 소요되므로 웹 컨테이너가 구동될 때 수행되는 것이 좋다
- 이것을 위한 설정이 load-on-startup 태그이며, 이 태그가 설정된 각 서블릿 클래스는 작은 숫자부터 오름차순으로 컨테이너에 로딩된다

각 서블릿에 대한 load-on-startup 세팅 예시
```XML
	...
	<servlet>
		<servlet-name>Config</servlet-name>
		<servlet-class>config.ConfigInit</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet>
		<servlet-name>DBCPInit</servlet-name>
		<servlet-class>jdbc.DBCPInit</servlet-class>
		<load-on-startup>2</load-on-startup>
	</servlet>
	...
```

- @WebServlet 어노테이션을 사용하는 서블릿의 경우에는 loadOnStartup 속성에 속성값을 지정함으로써 설정 가능하다




































