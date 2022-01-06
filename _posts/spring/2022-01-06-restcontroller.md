---
layout: single
title: "[Spring] RestController의 활용"
excerpt: 'restController를 통해 ajax 통신하기'
categories: Spring
tag: spring, java, mvc, restController, javascript, jQuery
---

# 1. RestController
- RestController는 webPage(jsp)가 아니라 데이터를 내려보낸다. 그렇기 때문에 restController는 일반 Controller와 다르게 **반환값이 보통 객체**이다. RestController를 사용하기 위해서는 <u>객체를 반환 시 json으로 변환해 주는 라이브러리가 필요</u>하다. 해당 라이브러리는 요청핸들러 메소드가 반환하는 값을 json 형식의 텍스트로 변환하고, 응답메세지의 body부에 포함시켜서 클라이언트에게 보낸다.

> **1. pom.xml 설정**
- <u>자바객체를 json 형식의 텍스트로, json 형식의 텍스트를 자바객체로 변환</u>시키는 라이브러리를 의존성 주입한다. jacson-databind를 사용하였다.

```xml
    <dependencies>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.13.1</version>
        </dependency>
    </dependencies>
```

> **2. RestController 설정**
- 일반적인 Controller는 @Controller 어노테이션을 사용하지만, restController는 <u>@RestController 어노테이션을 사용</u>한다.
- 아래의 컨트롤러는 "localhost/프로젝트명/rest/book/detail.do?no=150"와 같은 요청이 오면 데이터를 반환하는 요청 핸들러 메소드이다. 
- url을 입력했을 때 사진과 같이 json 형식의 텍스트로 내려온다.
![image](https://user-images.githubusercontent.com/87356533/148380218-a3c4788d-a01d-4fad-b198-91d150ea5bcf.png)
- 또, Book 객체를 보낼 때 vo의 프로퍼티에 아래와 같은 설정을 해야 유닉스타임이 아닌 지정한 형식으로 json에 반영된다.
    ```java
        @JsonFormat(pattern = "yyyy년 M월 d일")
        private Date pubDate;
    ```



```java
    @RestController
    @RequestMapping("/rest/book")
    public class BookRestController {

        @Autowired
        private BookService bookService;

        @GetMapping("/detail.do")
        public Book detail(int no) {
            Book book = bookService.getBookDetail(no);
            return book;
        }
```



> **3. context-web.xml 설정**
- @RestController 어노테이션이 부착된 클래스를 스캔해서 스프링 컨테이너의 빈으로 등록시킨다.

```xml
    <context:component-scan base-package="com.sample.restcontroller"></context:component-scan>
```

---

# 2. 설정한 RestController로 AJAX 통신하기
![image](https://user-images.githubusercontent.com/87356533/148378959-645f3fa3-26f8-4519-8b8c-ab8b062a5241.png)

- **ajax란 화면의 리로딩 없이 화면의 특정 영역, 엘리먼트를 갱신하는 것**이다. 
- 아래 사진의 카트 리스트에서 책 제목을 클릭했을 때,

![image](https://user-images.githubusercontent.com/87356533/148379198-af198e69-cfef-4a01-8122-13f8bc5a4792.png)

- 위 사진처럼 리로딩없이 모달창을 띄우는 코드를 살펴보도록 하자.
- 모달은 부트스트랩5를 사용하였다.

```jsp
<script type="text/javascript">
	$(function() {
		// 모달객체 생성
		var bookInfoModal = new bootstrap.Modal(document.getElementById('modal-info-book'), {
		      keyboard: false
		});
		
		$("#table-cart-items a").click(function(event) {
			event.preventDefault(); 
			var bookNo = $(this).attr("data-book-no");
			
			$.getJSON('/rest/book/detail.do', {no:bookNo}, function(book) {
				// 응답으로 받은 책 정보를 모달창 테이블의 각 칸에 표시한다.
				$("#span-book-created-date").text(book.createdDate);
				$("#span-book-updated-date").text(book.updatedDate || ''); 
				$("#span-book-title").text(book.title);
				$("#span-book-author").text(book.author);
				$("#span-book-publisher").text(book.author);
				$("#span-book-price").text(book.price.toLocaleString());
				$("#span-book-discountPrice").text(book.discountPrice.toLocaleString());
				$("#span-book-pubDate").text(book.pubDate);
				$("#span-book-stock").text(book.stock.toLocaleString());
				
				// 모달창을 화면에 표시한다.
				bookInfoModal.show();
			});
		});
	});
</script>
```

> **스크립트 코드 살펴보기**

- **$("#table-cart-items a").click(function(event) { ... }**
    - 장바구니 테이블 안에 있는 a 태그 클릭 시 실행될 이벤트 핸들러 함수를 등록한다.
- **event.preventDefault();**
    - a 태그에서 클릭이벤트가 발생하면 링크된 페이지로 이동하는 기본동작이 일어나지 않게 한다.
- **var bookNo = $(this).attr("data-book-no");**
    - 지금 이벤트가 발생한 엘리먼트(클릭된 엘리먼트)의 data-book-no 속성값을 조회한다.
- **$.getJSON('/rest/book/detail.do', {no:bookNo}, function(book) { ... }**
    - no라는 이름으로 bookNo를 전달한다.
        - no는 restController에서 설정한 매개변수명이다.
        - bookNo는 사용자가 클릭한 책제목의 번호이다.
	- "http://localhost/rest/book/detail.do?no=150"과 같은 형태의 요청을 서버로 보낸다.
	- 서버로부터 성공적인 응답이 오면 function(응답데이터) { ... } 함수가 실행된다.
	- jQuery는 서버로부터 성공적인 응답이 오면 응답컨텐츠(json 형식의 텍스트)를 자바스크립트 객체나 배열로 변환한 다음, 함수를 실행할 때 전달한다.

<br>
전체 코드는 **[링크](https://github.com/subtitle1/sample-spring-app)**에서 확인 가능합니다.
			