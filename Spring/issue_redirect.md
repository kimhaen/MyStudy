
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190204-lightgray.svg?style=flat-square)

## 리다이렉트 이슈

> 굉장히 아무것도 아닌 것같은 이슈 때문에 상당한 시간을 보냈다. 그것은 컨테이너에서 로그인 처리 후 인덱스 페이지로 리다이렉트하는 문제였다

- 컨텍스트 패스는 "http://localhost:8181/controller" 이고, 인덱스 페이지는 "~/views/index.jsp" 경로에 위치한다
- 인덱스에서 "member/loginForm" 요청을 하면 스프링 컨트롤러는 "http://localhost:8181/controller/views/member/loginForm.jsp" 뷰를 반환하도록 조치했다
- 이때 컨트롤러의 @RequestMapping 경로는 "member" 이고, 로그인 폼 경로는 "loginForm" 으로 매핑된다
- 여기서 아이디 비번 입력 후 제출하면 로그인은 잘 되나, 로그인 후 인덱스 페이지로 리다이렉트 하는 과정에서 문제가 발생했다

### 리다이렉트 문제발생

- 리다이렉트 단계에서 초기 인덱스 페이지로 보낼 것이므로 경로를 "index" 로 지정하면 잘 이동하긴 하지만, (이것도 왜 이렇게 되는지는 모름)
- 상단에 url 을 보면 "http://localhost:8181/member/login" 으로 되어 있다 (잘 보면 "member" + "login" 구성임)
- 혹시나 해서 "controller/index" 나 "controller/index.jsp" 로 해도 마찬가지다
- 문제 원인 및 해결
	* 컨트롤러에서 응답경로를 반환할 때 경로값 앞부분에 "/" 루트 경로를 포함시키지 않으면 컨트롤러와 해당 메서드에 지정된 매핑 url 로 리다이렉트된다
	* 그것이 바로 "member" + "login" 이었음
	* 그러므로 "/" 를 포함하면 된다. "/" 만 있으면 컨텍스트 패스로 직접 가고, 추가경로를 입력하면 그리로 가고, 아니면 절대경로를 입력하면 된다

```JAVA
...

@Controller
@RequestMapping(value="member")
public class MemberController {
	
	...

	@RequestMapping(value="login", method=RequestMethod.POST)
	public String login (@RequestParam(value="userId") String userId, @RequestParam(value="userPwd") String userPwd, HttpSession session) {
		String result = "";
		MemberVo vo = null;
		logger.info("login 시작");
		vo = service.memberLogin(userId, userPwd);
		
		// 성공시 세션 세팅 후 인덱스로 이동, 실패시 실패 이유 알려주지 않음 (실패이유는 로그에 저장됨)
		if (vo != null) {
			session.setAttribute("userId", vo.getUserId());
			session.setAttribute("userName", vo.getUserName());
			session.setAttribute("msg", "로그인에 성공했습니다");
			logger.info("login 성공");
			
			result = "redirect:/";
		} else {
			session.setAttribute("msg", "로그인에 실패했습니다");
			logger.info("login 실패");
			result = "redirect:loginForm";
		}
		
		return result;
	}
	
	@RequestMapping(value="logout", method=RequestMethod.GET)
	public String logout (HttpServletRequest request) {
		HttpSession session = request.getSession();
		session.removeAttribute("userId");
		session.removeAttribute("userName");
		
		return "redirect:/";
	}

	...
```

- 이와 같이 redirect 경로 반환값이 "/" 루트 컨텍스트로 시작하도록 한다

### 정리

- 일반적인 반환 경로값 : 뷰리졸버에 의해 "컨텍스트 패스/views/" + "반환값" + ".jsp" 이런 식으로 조합됨
- 리다이렉트 시 경로값 : "/" 를 포함한 절대 경로로 이동함, "/" 가 포함되지 않으면 자동적으로 "컨트롤러 매핑 + 메서드 매핑" 경로로 이동한다 (해당 경로가 없으면 에러남)

### 추가

- 컨트롤러 각 메서드의 경로 반환값을 "redirect:" 형식으로 하면,
- 기존 서블릿 컨테이너에서의 response.sendRedirect() 메서드와 같은 동작을 한다
- 스프링에서는 이전 페이지로 되돌리거나, 주로 인터셉터에서 커팅할 때 사용한다 (최초 페이지로 보냄)









