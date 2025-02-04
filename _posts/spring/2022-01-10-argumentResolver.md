---
layout: single
title: "[Spring] 사용자정의 어노테이션 생성 및 ArgumentResolver"
excerpt: 'ArgumentResolver'
categories: Spring
tag: spring, java, mvc, argumentResolver
---

# 1. 사용자 정의 어노테이션 정의하기
어노테이션을 생성하기 위해서 패키지를 생성하고, 우클릭으로 Annotation 파일을 선택한다. 어노테이션을 생성할 때는 두 가지 설정이 필요하다. <br> **@Target**은 어노테이션 적용 대상을 말한다. ElementType.PARAMETER은 메소드나 생성자의 매개변수에 부착할 수 있는 어노테이션임을 나타낸다. <br> 또 **@Retention**은 어노테이션의 유지되는 시점을 나타낸다. RetentionPolicy.RUNTIME은 이 어노테이션이 프로그램 실행 시점까지 유지됨을 나타낸다. 그렇기 때문에 아래와 같이 정의해 줄 수 있다.

```java
    @Target(ElementType.PARAMETER)
    @Retention(RetentionPolicy.RUNTIME)
    public @interface LoginedUser {}
```

생성한 어노테이션을 controller에서 아래와 같이 적용 가능하다. 요청핸들러 메소드에서 세션 객체로 로그인 된 사용자 정보를 불러올 필요가 사라진다. @LoginedUser 어노테이션이 붙은 user 매개변수에 세션 객체에 있는 정보를 부여할 수 있는 것이다. 이와 같이 어노테이션을 활용할 수 있도록 도와주는 것이 argumentResolver이다. ArgumentResolver는 2번에서 확인해 보도록 하자.

```java
@GetMapping("/list.do")
public String list(@LoginedUser User user, Model model) {
    /* 세션에 저장된 사용자 정보를 조회한다
    User user = (User) SessionUtils.getAttribute("LOGIN_USER");
    if (user == null) {
    	throw new LoginErrorException("로그인 후 사용가능한 서비스 입니다.");
    }*/
    
    // 사용자번호로 장바구니 아이템정보를 조회한다.
    List<CartItemDto> dtos = cartItemService.getMyCartItems(user.getNo());
    // 조회된 장바구니 아이템보를 뷰페이지로 전달하기 위해서 model에 저장한다.
    model.addAttribute("cartItems", dtos);
    
    return "cart/list.jsp";
}
```

<br>
---

# 2. HandlerMethodArgumentResolver
**요청 핸들러 메소드의 매개변수를 분석해서 해당 매개변수에 적절한 값을 전달**하는 인터페이스이다. 즉, 컨트롤러의 매개변수를 분석한다. 보통 컨트롤러 요청 핸들러 메소드의 매개변수는 요청 파라미터나 폼 입력값으로 취급되는데, ArgumentResolver는 우리가 생성한 어노테이션이 붙은 매개변수는 다르게 처리하게끔 도와준다.
- 주요 API
    - boolean supportsParameter(MethodParameter parameter)
    - Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
				NativeWebRequest webRequest, WebDataBinderFactory binderFactory)
- **supportsParameter 메소드**는 boolean 타입이기 때문에 true/false를 반환하는데, 이 메소드가 true를 반환해야 resolveArgument 메소드가 실행된다.
- **resolveArgument 메소드**는 위의 메소드가 true를 반환할 때만 실행되며, 이 메소드가 전달하는 값이 요청핸들러 메소드의 매개변수에 전달된다.

```java
public class LoginedUserArgumentResolver implements HandlerMethodArgumentResolver{
	
	@Override
	public boolean supportsParameter(MethodParameter parameter) {
        // 위에서 생성한 어노테이션 클래스가 있으면 true를, 없으면 false를 반환한다
		return parameter.hasParameterAnnotation(LoginedUser.class);
	}

	@Override
	public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, 
          NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
		// HttpSession객체에 "LOGIN_USER"라는 속성명으로 저장된 사용자정보를 반환한다.
		return SessionUtils.getAttribute("LOGIN_USER");
	}	
}
```

- 생성한 클래스를 context-web.xml에 등록까지 해 주면 완료!

```xml
	<mvc:annotation-driven >
		<mvc:argument-resolvers>
			<bean class="com.sample.argumentResolver.LoginedUserArgumentResolver"></bean>
		</mvc:argument-resolvers>
	</mvc:annotation-driven>
```
