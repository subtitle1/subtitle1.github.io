---
layout: single
title: "[Spring] HandlerInterceptor"
excerpt: ''
categories: Spring
tag: spring, java, mvc, Interceptor
---

# 1. HandlerInterceptor
- 컨트롤러의 요청핸들러 메소드 실행 전처리/후처리를 구현하는 객체다. 실행은 **interceptor -> argumentResolver -> 요청핸들러 메소드 실행** 순이다. 로그인을 예시로 들면 interceptor가 제일 먼저 실행되어 세션에 로그인된 사용자가 있는지 확인하고, 없으면 에러페이지로 redirect 한다. 있으면 argumentResolver가 실행되어 세션 사용자 정보를 조회하고, 그 이후 요청 핸들러 메소드가 실행된다.


- 주요 API
    - **boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)**
        - 컨트롤러의 **<u>요청핸들러 메소드 실행 전 실행</u>**되는 메소드다.
        - 이 메소드가 true를 반환하면 요청핸들러 메소드를 실행하고, false를 반환하면 요청핸들러 메소드를 실행하지 않는다.
    - **void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)**
        - 컨트롤러의 요청핸들러 메소드 실행 후 실행되는 메소드다.
    - **void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)**
        - 클라이언트의 요청을 처리하고, DispatcherServlet에서 응답을 보낸 후 실행되는 메소드다.

<br>
---

# 2. Interceptor의 예시
- 응답 요청에 따라서 일반적인 interceptor와 restInterceptor가 존재한다.

```java
public class LoginCheckInterceptor implements HandlerInterceptor {

	private static Logger logger = LogManager.getLogger(LoginCheckInterceptor.class);
	
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		
		HandlerMethod handlerMethod = (HandlerMethod) handler;
		MethodParameter[] methodParameters = handlerMethod.getMethodParameters();
		
		boolean hasLogineUser = false;
		for (MethodParameter methodParameter : methodParameters) {
			if (methodParameter.hasParameterAnnotation(LoginedUser.class)) {
				hasLogineUser = true;
				break;
			}
		}
		
		logger.info("로그인 체크 여부: " + hasLogineUser);
		
		if (!hasLogineUser) {
			return true;
		}
		
		User user = (User) SessionUtils.getAttribute("LOGIN_USER");
		if (user == null) {
			response.sendRedirect("/error/login/form.do");
			return false;
		}

        // rest 응답인 경우
        User user = (User) SessionUtils.getAttribute("LOGIN_USER");
		if (user == null) {
			response.sendRedirect("/error/login/rest.do");
			return false;
		}
		
		return true;
	}
}
```

<br>
---

# 3. ErrorController
```java
@Controller
@RequestMapping("/error")
public class ErrorController {

	@RequestMapping("/login/form.do")
	public String loginform() {
		throw new LoginErrorException("로그인 후 사용가능한 서비스 입니다.");
		// 아래의 예외처리 핸들러 메소드가 실행되도록 예외를 강제로 발생시켰다.
	}
	/*
	@ExceptionHandler(LoginErrorException.class) 
	public String handleLoginErrorException(LoginErrorException e, Model model){
		
		model.addAttribute("error", e.getMessage());
		return "loginform.jsp";
	}
	 */
	
	
	@RequestMapping("/login/rest.do")
	public ResponseDto<?> rest() {
		throw new RestLoginErrorException("로그인 후 사용가능한 서비스입니다.");
		// 아래의 예외처리 핸들러 메소드가 실행된다.
	}
	
	/*
	@ExceptionHandler(RestLoginErrorException.class)
	public @ResponseBody ResponseDto<?> handleCartErrorException(RestLoginErrorException e) {
		
		ResponseDto<?> response = new ResponseDto<>();
		response.setStatus("FAIL");
		response.setError(e.getMessage());
		
		return response;
	}
	 */
}
```
