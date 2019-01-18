
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190117-lightgray.svg?style=flat-square)

## MVC 구조 및 흐름에 대한 고찰 - 스프링 MVC 구조에 대하여

> 웹앱을 만든다면 지겹도록 반복하는게 MVC 구조이다.
> 그렇게 반복해서 만들다보면 자연스레 익숙해 지겠지만, 구조의 각 부분에 대한 분명한 이해와 고찰이 없이는 언제까지나 혼란스러운 상태로 남아 있을 것이다

### View

- 뷰 단은 말 그대로 브라우저에 표현되는 부분이다
- 보통 브라우저의 URL 요청에 대한 Controller(스프링 컨테이너 DispatcherServlet) 응답 화면을 의미한다
- HTML, CSS, Javascript, JSP 로 구성된다
- 오늘날에는 자바스크립트의 놀라운 발전으로 인해 뷰 단에서부터 간단한(아니면 세밀한) 데이터 처리 후 서버에 요청을 보내던가
- 반응형 웹 처리를 스스로 담당하기도 한다

### Controller

- 스프링 컨테이너의 핵심이며 모든 요청 및 데이터 서비스 처리의 흐름을 담당한다
- 자바 클래스 중 @Controller 어노테이션된 컴포넌트가 컨트롤러이며,
- 회원, 게시글, 관리자 등의 넓은 범주에서의 데이터에 따른 컨트롤러가 각각 존재하기도 한다
- 이는 프로젝트의 구조가 커짐에 따라 컨트롤러의 기능을 세분화한 것으로 이해하면 된다

#### Controller 의 동작 흐름

- DispatcherServlet 은 web.xml 파일 설정에 따라 빈 형식으로 생성되어 모든 로직 처리를 담당한다
- 이것의 init-param 으로 적용된 servlet-context.xml (스프링 설정파일) 에 의해 HandlerMapping, HandlerAdapter, ViewResolver 가 생성된다
- 스프링 컨테이너에 자동 생성된 HandlerMapping 으로 요청에 적절한(보통 매핑된 url) 컨트롤러를 찾고,
- 찾은 컨트롤러에 HandlerAdapter 로 실제 접근한다
- 해당 컨트롤러는 이제 적절한 메서드로 요청 service 를 처리하고, 로직에 따라 결과 데이터를 Model 혹은 ModelAndView 객체에 담은 후 반환한다(모델 객체와 뷰에 조합할 파일이름)
- 이후 ViewResolver 는 컨트롤러의 반환값과 servlet-context.xml 에 설정된 beans:property 속성 (prefix, suffix) 값들을 활용해 뷰 페이지를 조합하고,
- 최종적으로 DispatcherServlet 은 그것을 브라우저에 response 한다. 화면에 보여지는 것은 뷰 페이지 조합의 결과이다
- 결과 페이지에는 model 객체에 담긴 로직 처리 결과 데이터가 표현될 수 있다

#### Service 와 DAO, Data Object

- 프로젝트에 따라 차이가 있지만, 보통 service 단위와 dao 단위를 분리해서 고려한다. 이는 모듈 간의 결합도를 더욱 느슨하게 하기 위함이다
- 하지만 규모가 작은 프로젝트라면, 서비스와 dao 의 기능이 거의 동일한 것으로 느껴질 수 있다. 그러므로 둘 간의 차이점을 분명히 인식해야 혼란이 없을 것이다
- 일단 Service 는 특정 작업의 트랜잭션 단위, DAO 는 좀더 세분화된 (DO 의 데이터 단위와 일치하는) 데이터 처리 단위로 이해하면 된다
- CRUD 로직에서 Service 의 '삽입' 트랜잭션 단위는 register 개념이며, dao 는 실제 insert 개념이다
- 같은 방식으로 search / select, modify / update, remove / delete 개념이다 (트랜잭션 / 실제작업 or 큰거 / 작은거 or ...)

### 그럼, Model 은?

- 모델 객체는 VO 와 Dao, Service 에 따른 데이터 묶음이나 처리 결과를 지칭한다
- 즉, DB 에 실제 적용되는 데이터 단위이며, 이것을 활용해 뷰 단을 표현하거나 데이터 처리를 진행한다
- Model 객체와 ModelAndView 객체의 차이
  * 컨트롤러 각 메서드에서 데이터는 Model 에 담기고 View 는 반환값으로 처리된다
  * 반면 메서드에서 ModelAndView 객체를 반환하기로 지정하면, mav 객체를 생성하고 거기에 처리 데이터와 뷰 이름을 함께 담아서 한 번에 반환한다

