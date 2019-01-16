
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190117-lightgray.svg?style=flat-square)

# Spring MVC

> 현재 웹앱 프로젝트에서 소스를 IoC, DI, AOP 방식으로 관리하고 생명주기를 컨트롤하는 데 가장 많이 사용되는 스프링 컨테이너를 활용한 MVC 구조에 대해서 알아본다

## 대략적인 구조

![spring mvc structure](https://github.com/daesungRa/MyStudy/blob/master/imgs/SpringStudy/SpringMVCStructure.PNG)

- 일단 브라우저의 모든 요청을 스프링 컨테이너가 받는데, 그것은 DispatcherServlet 을 의미한다
- 이후 DispatcherServlet 은 HandlerMapping 으로 적합한 Controller 클래스를 찾고, HandlerAdapter 로 실제 그 컨트롤러 클래스에 접근한다
- 컨트롤러는 적합한 메서드를 활용해 실제 데이터 처리를 수행하고, Model model 객체에 결과값을 담은 후 정해진 값을 리턴한다
- 이 정해진 값을 토대로 ViewResolver 는 브라우저에 표현할 view 페이지를 구성하고, 최종적으로 DispatcherServlet 은 view 페이지를 브라우저에 응답한다(JSP 페이지 등)

## 세부 구조

- DispatcherServlet 은 web.xml (웹앱 환경설정 파일) 에 '/' 로 매핑하되, init-param 으로 servlet-context.xml 파일 (스프링 설정파일) 을 지정한다
- 이 파일은 스프링 컨테이너에 HandlerMapping, HandlerAdapter, ViewResolver 을 자동생성한다. 그냥 가져다 사용하기만 하면 됨
- 만약 DispatcherServlet 에 별도의 스프링 설정파일을 지정하지 않았다면, 기본 appServlet-context.xml 파일을 통해 스프링 컨테이너를 생성한다
- 이제 이 스프링 컨테이너를 가지고 Controller 로 Model 에 대한 데이터 처리를 수행하거나, View 응답정보를 생성한다

### DispatcherServlet 의 작업과정

- DispatcherServlet 은 스프링 컨테이너의 객체 및 정보들을 활용해 순차적으로 작업한다
- Controller
	* 스프링 컨테이너에서 컨트롤러에 대한 등록은 설정파일인 servlet-context.xml 에서 한다
	* <annotation-driven /> 태그가 그것이며, 여기서 등록된 컨트롤러는 실제 자바 클래스 파일에 @Controller 어노테이션 적용된 파일과 대응된다
	* 컨트롤러 어노테이션 적용된 클래스 소스에 @RequestMapping 어노테이션도 적용할 수 있는데, 이는 웹앱에 요청된 특정 URI 방식과 대응된다
	* 만약 브라우저가 '[웹앱]/success' 로 페이지 요청했다면, 컨트롤러 클래스 중 @RequestMapping("/success") 이 적용된 객체가 응답한다
- Model Control
	* 사실상 모델 부분은 컨트롤러에 의해 처리된다
	* 컨트롤러 객체는 특정 로직을 활용해 Model 의 데이터를 처리한다.
	* 처리 결과는 DispetcherServlet 에 전달되고, 컨트롤러의 반환값은 메서드에 지정된 뷰 페이지의 이름이다 ("success")
- View Control
	* 스프링 컨테이너의 ViewResolver 는 컨트롤러 작업 이후 DispetcherServlet 에 의해 동작한다
	* 뷰리졸버 객체에 세팅된 prefix 프로퍼티 값(/WEB-INF/views/)과 컨트롤러 반환값, suffix 프로퍼티 값(.jsp)을 조합해 최종적인 뷰 페이지를 구성한다
	* DispatcherServlet 은 이 최종 뷰 페이지를 브라우저에 전달하며 동작을 마친다
	* 위 예시의 경우 '/WEB-INF/views/success.jsp' 페이지가 반환된다


