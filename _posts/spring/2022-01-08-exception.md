---
layout: single
title: "[Spring] 스프링의 예외처리"
excerpt: '@ControllerAdvice와 @RestController'
categories: Spring
tag: spring, java, mvc, exception
---

# 1. @ControllerAdvice와 @RestControllerAdvice
<span style="color:red">**@ControllerAdvice**</span>는 @Controller / @RestController 컨트롤러에서 관련된 예외처리가 구현된 클래스를 나타내는 어노테이션이다. 해당 어노테이션이 붙은 클래스의 예외처리 메소드는 예외가 발생하면 **오류페이지, 혹은 JSON 응답을 제공**할 수 있다. 반면에 <span style="color:red">**@RestControllerAdvice**</span>는 @RestController 클래스 관련된 예외처리가 구현된 클래스를 나타내는 어노테이션이다. 이름에서 알 수 있듯이 예외가 발생하면 **JSON 응답만 제공**할 수 있다.
<br><br>
ControllerAdvice를 사용하는 이유는 **컨트롤러에서 던져지는 예외들을 일괄적으로 처리하기 위해서**이다. ControllerAdvice의 예외핸들러 메소드에는 **@ExceptionHandler**를 사용하는데, 예외를 종류별로 처리할 수 있게 된다. 예외핸들러 메소드는 크게 두 가지로 나뉜다.
- 에러 페이지로 내부이동하는 메소드(내부이동)
- 오류응답데이터가 전달되게 하는 메소드(JSON 응답)

<br>
---

# 2. 예외처리 핸들러 메소드 예시
- 예외발생 시 jsp 경로로 내부이동하는 예외처리 핸들러 메소드이다. 

```java
    @ExceptionHandler(예외클래스명.class)
    public String handle예외클래스명(예외클래스 e) {
        return "오류정보를 표시하는 jsp 페이지 경로";
    }
```

- @ResponseBody
    - ResponseBody 어노테이션은 응답메세지의 바디부에 리턴하는 값을 전달한다.
-  예외발생 시 오류메세지(JSON 텍스트 데이터)를 응답으로 제공하는 예외처리 핸들러 메소드이다.

```java
    @ExceptionHandler(예외클래스명.class)
	public @ResponseBody ResponseDto<?> handle예외클래스명(예외클래스 e)
        ResponseDto(?) response = new ResponseDto<>();
        response.setStatus("FAIL");
        response.setMessage(e.getMessage);
        
        return response;
	}
	  
	 // 위의 예외처리 메소드는 {"status":"FAIL", "error": "오류 메세지 내용", "items": null}을 응답으로 보낸다.
```

<br>
---

# 3. 사용자 정의 예외클래스 구현
1. RuntimeException을 상속받아 사용자정의 예외클래스를 구현한다.
```java
    public class CustomException extends RuntimeException {
        private static final long serialVersionUID = 9040654718873050613L;
        public CustomException(String message) {
            super(message);
        }
    }
```
2. 1번에서 구현한 사용자정의 예외클래스를 상속받아서 구체적인 예외클래스를 구현한다.
```java
    public class LoginErrorException extends CustomException {
        private static final long serialVersionUID = -7799696001358188839L;
        public LoginErrorException(String message) {
            super(message);
        }
    }
```
3. 2번에서 구현한 에러를 처리하는 서비스 메소드를 한번 살펴보자!
```java
    @Service
    public class UserService {
        @Autowired
        UserDao userDao;

        public User login(String id, String password) {
            // 회원정보
            User user = userDao.getUserById(id);
            if (user == null) {
                throw new LoginErrorException("회원정보가 존재하지 않습니다.");
            }
            if ("Y".equals(user.getDisabled())) {
                throw new LoginErrorException("탈퇴처리된 회원 아이디입니다.");
            }
            if (!password.equals(user.getPassword())) {
                throw new LoginErrorException("비밀번호가 일치하지 않습니다.");
            }
            // 인증된 사용자 정보를 반환
            return user;
        }
    }
```
4. 2번에서 정의한 예외클래스에 대한 예외처리 핸들러 메소드를 정의한다.
```java
    @ControllerAdvice
    public class ExceptionHandlerControllerAdvice {

        @ExceptionHandler(LoginErrorException.class)
        public String handleLoginErrorException(LoginErrorException e, Model model) {
            model.addAttribute("error", e.getMessage());
            return "loginform.jsp";
        }
    }
```