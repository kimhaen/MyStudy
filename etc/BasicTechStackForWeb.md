
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190123-lightgray.svg?style=flat-square)

# 일반적인 웹 프로젝트(게시판)를 위한 기본 기술 목록

> 이정도는 알아야, 혹은 좀 더 알면 좋은 기술 목록. 지속적으로 갱신해 나갈 것이다.<br/>
> 자세한 공부 내용은 [MyStudy](https://github.com/daesungRa/MyStudy/) 의 각 카테고리에 정리함

## 사용 언어

- Java
	* 자바의 특성, JDK / JRE,  JVM, GC
	* 변수, 데이터 타입, 연산자, 조건문, 반복문, 참조 타입, 배열(다차원), 클래스, 메서드, 각종 키워드
	* 객체지향
		- 상속, 캡슐화, 다형성, 정보은닉, 접근제한자, 게터와 세터, 자바 빈, 어노테이션, 인터페이스, 추상 클래스
		- 클래스, 인스턴스, 객체, 생성자, 오버로딩 / 오버라이딩, static, final, 중첩클래스, 패키지
	* 예외처리, 스레드, 멀티스레드, 컬렉션 프레임워크, IO, 스트림, 소켓 프로그래밍, 기타 각종 API
	* 제네릭, 람다식
- JDK 1.8

## front

- HTML5
	* iframe tag
- CSS3
	* 검색 폼
	* 반응형
- Bootstrap5

- Javascript
	* 변수, 조건문, 반복문, 배열(다차원), 함수, 사용자 지정 함수
	* Event, Object, 사용자 정의 클래스 및 객체
- jQuery
- JSON

- AJAX
- WebSocket

- JSP
	* page directive
	* scriptlet
	* expression
	* declaration
	* jsp page scope : application > session > request > page(pageContext)
	* action tags : include, useBean
	* redirect : response.sendRedirect, jsp:forward page="~"
	* EL
	* JSTL
	* View-화면 구성
		- index.jsp (main.jsp)
		- 회원 관련(member) : login.jsp / login_result.jsp / logout.jsp / insert.jsp / insert_result.jsp / modify.jsp / modify_result.jsp / remove.jsp / remove_result.jsp
		- 게시판 관련(board) : main.jsp / write.jsp / view.jsp (read.jsp) / modify.jsp (update.jsp) / remove.jsp (delete.jsp)
		- 기타 : 관리자 페이지, 회원 목록, 총 게시글 목록(페이징, 계층형, 댓글), 검색 기능(키워드 통합검색), 파일 업로드
## back

- JSP
	* HttpServletRequest / HttpServletResponse
	* buffer
	* JSP/Servlet 프로젝트 구조
- Oracle (or MySql)
	* 시퀀스
	* 계층형 쿼리
	* 댓글
	* 종속된 업로드 파일 테이블
- Servlet
	* Cookie and Session
	* method: GET, POST
	* ContextPath
	* servlet mapping (web.xml, annotation)
	* init param
- Spring
	* IoC, DI
	* pom.xml, web.xml, root-context.xml, servlet-context.xml
	* spring container
		- lifeCycle, bean lifeCycle, singleton
		- DispatcherServlet, HandlerMapping, HandlerAdapter, ViewResolver
	* AOP
	* apache maven
	* mvc
		- controller
		- service
		- dao, vo, dto
		- view
		- Model, ModelAndView
	* interceptor
	* filter

## 기타

- JDBC Template
- JDBC Template Connection Pool
- myBatis
- spring boot
- spring security
- spring tiles

## Tools

- Eclipse
- tomcat container
- Git
	* git bash > Eclipse 와의 연동 (즉각적인 commit and push)
	* 브랜치 생성 및 관리
		- branch
		- checkout
		- reset
	* pull request 연습
- AWS
	* EC2
- intellij (?)

## Network

- TCP
- HTTP, HTTPS
- WS, WSS

## 외부 API

- 우편번호, 주소 검색
- Google Map
- 계정 연동(Google, Facebook, Twitter, Instagram, kakao, naver, daum, ...)

## 개인적인 기술

- console 환경에서 javac, java 명령어로 
- 스프링 환경설정 목록

- sysdba 계정 관리 및 권한 관리
- DB 계층형 쿼리, 댓글 및 업로드파일 저장 테이블

- 깃 레파지토리 생성 및 연동, 협업 기술

- 리눅스 환경설정 및 리눅스로 개발 / 각종 서버환경 구성
