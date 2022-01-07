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
ExceptionHandlerControllerAdvice를 사용하면 Service, controller에서 try / catch 구문을 사용할 필요가 사라진다. 즉, 예외처리를 한 곳에서만 진행할 수 있게 되고 예외별로 각기 다른 jsp 페이지로 보낼 수 있다.

# 2. 사용자 정의 예외클래스 구현
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