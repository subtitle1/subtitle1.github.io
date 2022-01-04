---
layout: single
title: "[Spring] 요청핸들러 메소드의 사용"
excerpt: '요청핸들러 메소드'
categories: spring
tag: spring, java, controller
---

# 1. 스프링에서의 요청핸들러 메소드
```java
@Controller
@RequestMapping("/emp")
public class EmployeeController {

	@Autowired
	private EmployeeService employeeService;

    @RequestMapping(path = "/list.do")
	public String list(Model model) {
		List<Employee> employees = employeeService.getAllEmployees();
		model.addAttribute("employees", employees);
		
		return "employee/list.jsp";
	}
}
```
빈 조립이 다 되어있다고 가정을 하고 EmployeeController를 살펴보자. <br>
- 위의 코드는 localhost/emp/**list.do 요청을 처리하는 요청핸들러 메소드**이다.
- 사용자가 list.do url을 요청하면 이 메소드가 실행된다.
- 요청핸들러 메소드가 반환하는 값은 내부이동 혹은 재요청 URL이다.
- 요청 처리를 완료하면 return으로 반환되는 jsp로 내부이동을 시킨다.
- 이전에 JSP로만 요청 파라미터를 꺼내기 위해서는,

```java
	String id = request.getParameter("id");
	String password = request.getParameter("password");
```

- 위 코드와 같이 request.getParameter() 메소드를 사용해서 객체를 꺼내왔었다.
- 스프링에서는 HttpServletRequest 대신 **Model 객체**를 사용한다.
- Model이란 ui에 표현할 데이터를 담아놓는 객체로, list.jsp에서 표현할 데이터를 모델 객체에 저장한다.

```jsp
<table>
    <thead>
        <tr>
            <th>아이디</th>
            <th>이름</th>
            <th>전화번호</th>
            <th>직종</th>
            <th>급여</th>
            <th>입사일</th>
        </tr>
    </thead>
    <tbody>
        <c:forEach var="emp" items="${employees }">
            <tr>
                <td>${emp.id }</td>
                <td><a href="detail.do?id=${emp.id }">${emp.firstName } ${emp.lastName }</a></td>
                <td>${emp.phoneNumber }</td>
                <td>${emp.jobId }</td>
                <td><fmt:formatNumber value="${emp.salary }" pattern="##,###"></fmt:formatNumber> 달러</td>
                <td><fmt:formatDate value="${emp.hireDate }" pattern="yyyy년 M월 d일"/></td>
            </tr>
        </c:forEach>
    </tbody>
</table>
```

- 모델 객체에서 employees 객체를 "employees"라는 이름으로 저장하였기 때문에 view page에서도 employees라는 이름으로 꺼내주어야 한다.
- items는 모델 객체에서 저장한 이름이 들어간다. for문으로 표현하면 아래와 같다.

```java
for (Employee emp : employees) {
    // 편의상 System.out.println으로 표현
    System.out.println(emp.id);
    System.out.println(emp.phoneNumber);
}
```

<br>
<br>

# 2. 같은 URL 요청도 처리할 수 있다
아래의 두 요청핸들러 메소드는 **/insert.do**라는 같은 url을 요청으로 받지만, 메소드에 붙어있는 어노테이션이 다른 것을 볼 수 있다. 요청 방식이 
**Get이면 @GetMapping**을, **Post 방식이면 @PostMapping**을 사용한다.

```java
@GetMapping("/insert.do")
public String form() {
    return "employee/form.jsp";
}

@PostMapping("/insert.do") 
public String save(EmployeeSaveForm employeeForm) {
    employeeService.addNewEmployee(employeeForm.toEmployees());
    return "redirect:list.do";
}
```

<br>
전체 코드는 **[깃허브](https://github.com/subtitle1/spring-database)**에서 확인 가능합니다.