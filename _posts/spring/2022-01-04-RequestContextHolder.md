---
layout: single
title: "[Spring] 로그인, 로그아웃 기능 구현"
excerpt: 'RequestContextHolder 유틸 클래스'
categories: Spring
tag: spring, java, RequestContextHolder
---

# 1. RequestContextHolder 클래스

RequestContextHolder는 스프링에서 제공하는 유틸 클래스로, **요청객체와 세션객체의 속성에 접근**할 수 있는 기능을 제공한다. 해당 클래스에 안에 있는 getRequestAttribute() 메소드는 **<u>RequestAttributes 객체를 반환</u>**한다. class 파일에서 코드를 살펴 보면 아래와 같다. 

```java
    public static RequestAttributes getRequestAttributes() {
        RequestAttributes attributes = requestAttributesHolder.get();
        if (attributes == null) {
            attributes = inheritableRequestAttributesHolder.get();
        }
        return attributes;
    }
```


---


# 2. RequestAttributes 객체의 주요 API
**setAttribute** 지정된 scope에 주어진 객체를 지정된 이름으로 저장하는 메소드이다. 이때 scope는 요청객체, 세션객체가 가능하다. **removeAttribute**는 해당 속성명으로 저장된 객체를 삭제하고, getAttribute는 해당 속성명으로 저장된 객체를 반환하는 메소드이다. **SCOPE_REQUEST와 SCOPE_SESSION**를 scope에 지정해서 요청객체/세션객체를 구분해서 속성을 관리할 수 있다.

```java
    void setAttribute(String name, Object value, int scope);
    void removeAttribute(String name, int scope);
    Object getAttribute(String name, int scope);
```

---

# 3. SessionUtils 클래스 생성
HttpSession 객체에 속성을 저장, 조회, 삭제하는 기능을 제공하는 유틸 클래스를 생성해 볼 것이다. 컨트롤러에서 model 객체에 담아 attribute에 조회하는 방법도 가능하지만, 보통은 아래 코드와 같이 클래스를 생성해 사용한다.

> **SessionUtils 코드**

```java
    public class SessionUtils {
        /**
        * 속성명, 객체를 전달받아서 HttpSession 객체에 저장한다.
        * @param name 속성명
        * @param value 객체
        */
        public static void addAttribute(String name, Object value) {
            RequestContextHolder.getRequestAttributes().setAttribute(name, value, RequestAttributes.SCOPE_SESSION);
        }

        /**
        * 속성명을 전달받아서 HttpSession객체에 해당 속성명으로 저장된 속성(객체)을 삭제한다.
        * @param name 삭제할 속성명
        */
        public static void removeAttrubute(String name) {
            RequestContextHolder.getRequestAttributes().removeAttribute(name, RequestAttributes.SCOPE_SESSION);
        }

        /**
        * 속성명을 전달받아서 HttpSession객체에 해당 속성명으로 저장된 속성(객체)을 반환한다.
        * @param name 조회할 속성명
        * @return HttpSession 객체에 저장된 속성
        */
        public static Object getAttribute(String name) {
            return RequestContextHolder.getRequestAttributes().getAttribute(name, RequestAttributes.SCOPE_SESSION);
        }
    }
```

> **Controller 코드**

```java

    @Controller
    public class HomeController {

        @GetMapping("/login.do")
        public String loginform() {
            return "loginform.jsp";
        }

        @PostMapping("/login.do")
        public String login(String id, String password, Model model) {
            // 아이디와 비밀번호가 비어있거나 공백만 있으면 loginform.jsp로 내부이동한다.
            if (!StringUtils.hasText(id) || !StringUtils.hasText(password)) {
                model.addAttribute("error", "아이디와 비밀번호는 필수 입력값입니다.");
                return "loginform.jsp";
            }

            try {
                // 로그인이 성공하면 session 객체에 "LOGIN_USER" 이름으로 담아 메인 페이지로 리다이렉트한다.
                User user = userService.login(id, password)   
                SessionUtils.addAttribute("LOGIN_USER", user);
                return "redirect:home.do";
            } catch(RuntimeException e) {
                // exception이 발생하면 model 객체에 담아 loginform.jsp에서 오류 메세지를 출력한다. 
                // exception이 발생할 때의 예외처리는 UserService에서 진행한다.
                model.addAttribute("error", e.getMessage());
                return "loginform.jsp";
            }
        }

        @GetMapping("/logout.do")
	    public String logout() {
		SessionUtils.removeAttrubute("LOGIN_USER");
		return "redirect:home.do";
	    }
    }
```