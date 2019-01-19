
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190119-lightgray.svg?style=flat-square)

# Interceptor

> CRUD 로직을 개발하다 보면 특정 작업을 처리하기 전이나 후, 아니면 모든 트랜잭션 수행 이후 시점에 공통적으로 수행해야 할 로직이 있을 수 있다<br/>
> 물론 이러한 공통 로직을 일일히 코딩할 수도 있겠으나, 반복작업은 최대한 지양하는 것이 생산성 및 유지보수 측면에 유리하므로 스프링에서는 Interceptor 를 사용한다

## 기본 개념

- 말 그대로 요청된 메인 로직 수행 시 특정 시점에 작업의 흐름을 가로채(인터셉트) 자신의 로직을 먼저 실행하는 컴포넌트이다
- 일반적으로 정보 수정 및 삭제 로직에서 세션의 유효성 체크할 때 많이 사용된다

## 인터셉터 추가하기

- Interceptor 는 HandlerInterceptorAdapter 클래스를 상속한 Bean 클래스이다
- 그러므로 여타 빈 클래스와 같이 스프링 설정파일인 servlet-context.xml 파일에 빈 객체 형식으로 등록한다
- 그러나 beans:bean 태그가 아닌 interceptors 라는 특수 태그를 사용하며, 그 안에 beans:bean 태그로 패키지+클래스경로를 등록한다
- 인터셉터가 적용되는 범주를 지정하기 위해 interceptor 내부에서 mapping 혹은 exclude-mapping 태그를 사용한다
- 이는 특정 컴포넌트를 지정하던가, 아니면 모든 요청에 인터셉터를 지정한 후 특정 컴포넌트만 제외하는 방식으로 적용하는 것이다
- 인터셉터는 preHandle, postHandle, afterCompletion 메서드를 재정의하며 각각 트랜잭션 이전, 이후, 최종 완료 후를 의미한다
- 일반적으로 pre 방식을 사용한다

### 멤버의 세션 유효성 검사(로그인 유무 검사)를 위한 인터셉터

[ContextPath]/[package]/MemberLoginInterceptor.java
```JAVA
package com.project.projectmvc.member;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

public class MemberLoginInterceptor extends HandlerInterceptorAdapter{

	// 설정파일에 따라 로직이 실행되기 전에 인터셉트해서 세션 유효성(혹은 존재유무)과 로그인 유무 체크(member)
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		HttpSession session = request.getSession(false);
		
		if (session != null) {
			Member mem = (Member)session.getAttribute("member");
			if (mem != null) {
				return true;
			}
		}
		
		// 세션이 null 이거나 로그인 상태가 아니라면(세션 내 Member 속성값 존재여부) 실패, 메인 페이지로 sendRedirect
		response.sendRedirect(request.getContextPath() + "/");
		return false;
	}
	
	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
		
		super.postHandle(request, response, handler, modelAndView);
	}
	
	@Override
	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
		
		super.afterCompletion(request, response, handler, ex);
	}
}
```

- preHandle 메서드를 주로 사용하며, request, response, Object handler, 추가적으로 MAV 나 EX 를 매개변수로 받는다

### 인터셉터의 등록 및 범주 지정

servlet-context.xml
```XML

	...
	
	<!-- interceptor 는 이곳에 지정한 설정에 따라 특정 컴포넌트가 동작하기 '이전, 이후, 최종' 시점에 자신의 로직을 실행한다 -->
	<interceptors>
		<interceptor>
			<!-- 이렇게 특정 컴포넌트만 지정하던가,
			<mapping path="/member/modifyForm"/>
			<mapping path="/member/removeForm"/>
			-->
			<!-- 전체에 인터셉터를 지정하고 특정 컴포넌트만 제외하는 방식으로 사용한다 -->
			<mapping path="/member/**"/>
			<exclude-mapping path="/member/joinForm"/>
			<exclude-mapping path="/member/join"/>
			<exclude-mapping path="/member/loginForm"/>
			<exclude-mapping path="/member/join"/>
			<exclude-mapping path="/member/logout"/>
			<exclude-mapping path="/member/modify"/>
			<exclude-mapping path="/member/remove"/>
			<!-- 어떤 인터셉터를 사용할 것인지 지정 -->
			<beans:bean class="com.project.projectmvc.member.MemberLoginInterceptor"></beans:bean>
		</interceptor>
	</interceptors>
</beans:beans>
```

- 주석의 내용처럼 인터셉터 적용할 컴포넌트만 설정하던가
- 전체 다 설정하고 특정 컴포넌트만 제외하는 방식으로 사용한다


## 인터셉터 사용시 주의할점

- 인터셉터의 범주를 명확하게 지정하지 않으면 예상치 못한 논리적 오류가 발생할 수 있다
- 나같은 경우는 멤버 데이터와 관련한 컨트롤러 로직에 대해 전체 인터셉터 지정 해놓고
- 회원가입, 로그인에 대한 컨트롤은 제외하는 식으로 적용했는데
- 로그인 요청정보를 작성하는 loginForm.jsp 는 잘 제외를 했으나 그것을 전달하는 memberLogin.java 에 대해서는 제외를 적용하지 못했다
- 그래서 아이디 비번을 잘 입력했는데도 세션에 로그인 정보가 setAttribute 되지 않는 문제가 발생했음
- 이런 논리적 오류는 해당 코드를 찾기까지 원인을 알아내기가 매우 어려우니 애초에 코드를 주의해서 작성할 것







