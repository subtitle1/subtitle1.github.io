---
layout: single
title: "[Spring] @RequestParam과 활용"
excerpt: '@RequestParam'
categories: Spring
tag: spring, java, RequestContextHolder
---

# 1. @RequestParam이란?
기본적으로 controller 내부의 메소드에서 **매개변수로 존재하는 값들은 반드시 전송해 주어야 한다.** 
페이지네이션을 예시로 컨트롤러 코드를 살펴보도록 하자. 사용자가 "list.do"를 요청할 때에는 아래와 같은 두 가지 URL이 전달될 수 있는데,

- list.do
- list.do?page=3

만약 해당 매개변수가 전송되지 않으면 에러가 발생하게 된다. 이 때문에 반드시 필요한 변수가 아니라면 **RequestParam**을 사용해서 필수 파라미터 여부의 값을 조정해 줄 수 있다. 이 파라미터를 사용하지 않고 요청을 보낼 때 기본으로 받을 값 또한 defaultValue를 통해 설정할 수 있다. 

```java
    @GetMapping("/list.do")
    public String list(@RequestParam(name="page", required=false, defaultValue="1") String page, 
    Model model, Criteria criteria) {
        // 구현 내용 생략
    }
```

> 위의 코드처럼 요청파라미터와 관련된 설정을 정의해 줄 수 있는 것이 **RequestParam**이다. 
- name: 요청파라미터 이름
- required: 필수 파라미터인지 아닌지에 대한 여부, 기본값은 true다
- defaultValue: 기본값 


