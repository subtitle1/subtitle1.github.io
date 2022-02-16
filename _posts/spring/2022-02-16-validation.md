---
layout: single
title: "[SpringBoot] 서버단 유효성 검사"
excerpt: ''
categories: Spring
tag: spring, java, mvc, jsp
---

javascript로도 유효성 검사를 할 수 있지만 서버단 측에서도 유효성 체크를 할 수 있다는 걸 오늘 알았다! 우선 validation 체크를 하기 위해서는 pom.xml에 의존성 추가가 필요하다.

```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
```

## 주요 어노테이션
유효성 검사 시 사용하는 주요 어노테이션을 살펴보자.

<div class="notice">
<p><strong>@Email</strong></p>
<span>- email 형식의 값만 들어가야 한다</span><br><br>

<p><strong>@NotBlank</strong></p>
<span>- 공백, 빈 문자열, null값을 허용하지 않는다</span><br><br>

<p><strong>@NotEmpty</strong></p>
<span>- 빈 문자열, null값은 허용하지 않지만 공백은 허용한다</span><br><br>

<p><strong>@Pattern</strong></p>
<span>- 빈 문자열, null값은 허용하지 않지만 공백은 허용한다</span><br><br>

<p>- 그 외 @Past, @Future, @Positive, @Min, @Max, @Size 등이 존재한다.</p>
</div>


## 1. 회원가입 폼 생성
회원가입 시의 유효성 검사를 하기 위해 UserRegisterForm을 생성한다. 

```java
public class UserRegisterForm {

	@NotBlank(message = "이메일은 필수 입력값입니다.")
	@Email(message = "유효한 이메일 형식이 아닙니다.")
	private String email;
	
	@NotBlank(message = "비밀번호는 필수 입력값입니다.")
	@Pattern(regexp = "[0-9a-zA-Z_!?]{8,20}", message = "유효한 비밀번호 형식이 아닙니다.")
	private String password;
	
	@NotBlank(message = "이름은 필수 입력값입니다.")
	@Pattern(regexp = "[가-힣]{2,}", message = "유효한 이름 형식이 아닙니다.")
	private String name;
	
	@NotBlank(message = "전화번호는 필수 입력값입니다.")
	@Pattern(regexp = "\\d{2,3}-\\d{3,4}-\\d{4}", message = "유효한 전화번호 형식이 아닙니다.")
	private String tel;
	
	public User getEntity() {
		User user = new User();
		user.setEmail(email);
		user.setPassword(password);
		user.setName(name);
		user.setTel(tel);
		
		return user;
	}
	
	/*
	@Past(message = "생일은 현재 시간보다 과거여야 한다.")
	private Date birth;
	
	@Future
	private Date reserveDate;
	
	@Positive
	private int quantity;
	
	@Min(value = 0)
	@Max(value = 100)
	private int score;
	
	@Size(min = 3, max = 10)
	private String nickName;
	*/
}
```

## 2. Service 코드
값의 형식에 대해서는 어노테이션으로 충분히 커버가 가능하지만, 이메일이 중복되는지의 확인 등은 DB 접속 후에 알 수 있는 정보들이다. 이 경우는 Service 코드에서 정의해 줄 수 있다. DB 조회 후 중복되는 이메일이 존재하면 RuntimeException을 던진다.

> **참고: Optional**<br>
Optional은 자바8 버전부터 등장한 API<br>
NullPointerExcetion을 피하기 위해서 null을 검사하는 기능이 포함된 Optional<T>가 추가되었다
{: .notice}

```java
    @RequiredArgsConstructor
    @Service
    public class UserService {

        private final UserRepository userRepository;
        
        public void saveUser(User user) {
            Optional<User> optional = userRepository.findByEmail(user.getEmail());
            if (optional.isPresent()) {
                throw new RuntimeException("이미 가입한 이메일 입니다.");
            }
            userRepository.save(user);
        }
    }
```

## 3. Controller 코드

controller에서 주요 코드는 두 가지가 있는데, 하나씩 살펴보도록 하자. <br> 사용자가 회원가입 버튼을 클릭할 때 registerForm jsp로 이동한다. <br> 아래 코드에서 중요한 부분은 <u>model에 위에서 생성한 UserRegisterForm을 담아주는 것</u>이다.
{: .notice}


```java
    @RequiredArgsConstructor
    @Controller
    public class HomeController {

        private final UserService userService;
        @GetMapping("/register")
        public String registerForm(Model model) {
            // 회원가입폼에 회원가입정보를 저장할 UserRegisterForm객체를 생성해서 Model에 담아서 전달한다.
            model.addAttribute("userForm", new UserRegisterForm());
            return "registerForm";
        }
    }
```

<div class="notice">
    <p><strong>@ModelAttribute("userForm")</strong> : 모델에서 <u>userForm이라는 이름으로 저장된 객체</u>를 찾아온다.</p>
    <p><strong>@Valid</strong> : <u>폼입력값에 대한 유효성 체크를 수행</u>시킨다. 유효성 체크를 수행할 값을 포함하고 있는 객체 앞에 설정한다.</p>
    <p><strong>@BindingResult</strong> : <u>유효성 체크 검사결과를 저장하는 객체</u>다. 유효성케르를 수행할 값을 포함하고 있는 객체 바로 다음에 위치시킨다.</p>
</div>

```java
    @RequiredArgsConstructor
    @Controller
    public class HomeController {
        public String register(@ModelAttribute("userForm") @Valid UserRegisterForm form, BindingResult errors) {

            // BindingResult객체에 에러가 포함되어 있는 경우 다시 회원가입페이지로 이동한다.
            if (errors.hasErrors()) {
                return "registerForm";
            }
            
            try {
                userService.saveUser(form.getEntity());
            } catch (RuntimeException e) {

                /*
                    UserRegisterForm에서 어노테이션으로 체크할 수 없는 폼입력값에 대한 체크를 수행하고,
                    체크를 통과하지 못했을 때
                    BindingResult객체의 rejectValue(입력필드명, 에러코드, 기본에러메세지) 메소드를 이용해서 BindingResult객체에
                    에러를 추가할 수 있다.
                */
                errors.rejectValue("email", null, e.getMessage());
                return "registerForm";
            }
            return "redirect:/completed";
        }
    }
```

## 4. JSP 코드
jsp 코드에서는 스프링 프레임워크에서 제공해 주는 태그 두 개를 포함시켜 주어야 한다.
form은 유효성 체크 시 필요한 태그이니 꼭 include 시켜주자!

```jsp
    <%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
    <%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
```

\<form:form />태그는 form태그를 나타낸다. <br>
\<form:form modelAttribute="userForm" />은 회원정보를 저장하기 위해서 <u>Model에 저장했던 UserRegisterForm의 이름</u>이다. <br><br>
\<form:input path="email" />는 \<input type="text" name="email" />을 나타낸다. <br>
**<u>path는 UserRegisterForm에서 해당 입력필드와 대응되는 멤버변수 이름</u>**이다. <br>
\<form:errors path="email" /> 이메일 필드의 값이 서버에서 유효성 체크를 통과하지 못했을 때 에러 메세지가 표시되는 곳이다.
{: .notice}

```jsp
    <div class="row mb-3">
        <div class="col">
            <p>이메일, 비밀번호, 이름, 전화번호를 입력하세요</p>
            <form:form modelAttribute="userForm" action="/register" class="border bg-light p-3" method="post">
                <div class="mb-3">
                    <label for="email-field" class="form-label">이메일</label>
                    <form:input class="form-control" id="email-field" path="email" />
                    <form:errors path="email" cssClass="text-danger"/>
                </div>
                <div class="mb-3">
                    <label for="password-field" class="form-label">비밀번호</label>
                    <form:password class="form-control" id="password-field" path="password" />
	                <form:errors path="password" cssClass="text-danger"/>
                </div>
                <div class="mb-3">
                    <label for="name-field" class="form-label">이름</label>
                    <form:input class="form-control" id="name-field" path="name" />
	                <form:errors path="name" cssClass="text-danger"/>
                </div>
                <div class="mb-3">
                    <label for="tel-field" class="form-label">전화번호</label>
                    <form:input class="form-control" id="tel-field" path="tel" />
	                <form:errors path="tel"  cssClass="text-danger"/>
                </div>
                <div class="text-end">
                    <a href="/" class="btn btn-secondary">취소</a>
                    <button type="submit" class="btn btn-primary">회원가입</button>
                </div>
            </form:form>
        </div>
    </div>
```