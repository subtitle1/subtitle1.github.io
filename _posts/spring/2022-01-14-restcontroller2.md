---
layout: single
title: "[Spring] RestController의 반환값"
excerpt: 'ajax'
categories: Spring
tag: spring, java, mvc, restController
---

# 1. restController 요청핸들러 메소드의 반환값
restController를 통해 객체를 반환할 때의 **응답데이터는 보통 json 텍스트**이다. 그 응답데이터를 자바스크립트에서 **객체나 배열로 변환**해 받는다. 각 객체마다 반환값, 응답데이터, 자바스크립트에서 변환되는 값을 확인해 보자.

- **Object**
    - 응답데이터: {"no":1, "title":"이것이 자바다"}
    - 자바스크립트: {no:1, title:"이것이 자바다"}
- **Map\<String, Object>**
    ```java
    Map<String, Object> map = new HashMap<>();
    map.put("no", 1);
    map.put("title", "이것이 자바다");
    ```
    - 응답데이터: {"no":1, "title":"이것이 자바다"}
    - 자바스크립트: {no:1, title:"이것이 자바다"}
- **List\<Book>**
    - 응답데이터: [{"no":1, "title":"이것이 자바다"}, {"no":2, "title":"자바의 정석"}]
    - 자바스크립트: [{no:1, title:"이것이 자바다"}, {no:2, title:"자바의 정석"}]

<br>
---

# 2. json 텍스트 컨텐츠가 Content-Type일 때의 처리 과정

![image](https://user-images.githubusercontent.com/87356533/149507690-602e5723-394a-46b0-9c4b-8f0c69293bcb.png)

![image](https://user-images.githubusercontent.com/87356533/149508083-1e6a9456-e527-4a57-b79f-fffba3bfa413.png)

요청메세지의 헤더부에 요청 메서드는 POST이고, URL은 POST http://localhost/api/book인 것을 확인할 수 있다. 또 컨텐츠의 타입은 application/json이다.
<br>
바디부는 요청객체에 전달받은 값이 그대로 저장되어 있는 것을 확인할 수 있다. 즉, /api/book와 같은 요청이 오면 

> 
1. 컨트롤러는 요청메세지의 body부에서 json 텍스트를 조회한다. 
2. jackson-databind 라이브러리는 jsonText를 분석해 BookForm 객체로 변환한다. 
3. save() 메소드를 호출할 때 bookForm 객체를 전달한다. 

컨트롤러의 코드는 다음과 같다.

```java
    @PostMapping
    public Book save(@RequestBody BookForm form) {
        log.info("폼 입력값: " + form);
        
        Book book = Book.builder()
                .title(form.getTitle())
                .author(form.getAuthor())
                .publisher(form.getPublisher())
                .pubDate(form.getPubDate())
                .price(form.getPrice())
                .discountPrice(form.getDiscountPrice())
                .stock(form.getStock())
                .build();
        
        Book savedBook = bookService.insertNewBook(book);
        return savedBook;
    }
```