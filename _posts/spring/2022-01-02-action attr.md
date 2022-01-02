---
layout: single
title: form의 action값 변경
excerpt: 'jQuery의 attr() 메소드'
categories: spring
tag: spring, java, jquery, javascript
---

jQuery 메소드 참고 문서 : **[링크](https://github.com/eungsu/documents/blob/master/05-front-end/04%20jquery.txt)**


사진과 같이 가격 변경, 재고 변경 버튼이 두 개 존재한다. 가격변경을 클릭하면 책번호, 가격, 할인가격을 서버로 전달하고, 재고변경 버튼을 클릭하면 책번호와 입고 수량을 서버로 전달해 보자. 

![image](https://user-images.githubusercontent.com/87356533/147872891-b78cd420-cd08-4945-9e0f-d825bdd72428.png)

> **$(선택자).attr("action", "action.jsp")**

- jQuery는 엘리먼트, 엘리먼트의 속성, 엘리먼트의 컨텐츠를 **조회/변경/추가/삭제 작업과 관련된 기능**을 제공한다.
- `$(selector).attr(name)`은 엘리먼트의 속성값을 반환한다. (name, id, class, action, type, src, href 등등)
- `$(selector).attr(name, value)*는 엘리먼트의 속성값을 value값으로 변경한다. 이 메소드를 사용해서 위 사진에 있는 버튼 두 개에 각각 다른 값을 부여할 수 있다.
- form의 id는 **form-update-book**이다.
- form의 action값은 "updatePrice.do"이다.
- 가격변경 버튼 클릭 시 updatePrice.do로, 재고변경 버튼 클릭 시 updateStock.do로 요청한다.
- 가격변경 버튼에는 **btn-update-price**를 아이디로 부여하였고, 재고변경 버튼에는 **btn-update-stock**를 아이디로 부여하였다.

> **BookController 코드**

```java
// updatePrice.do 요청이 왔을 때 실행되는 요청핸들러 메소드
@PostMapping("updatePrice.do")
public String updatePrice(int no, int price, int discountPrice) {
    service.updateBookPrice(no, price, discountPrice);
    return "redirect:detail.do?no=" + no;
}

// updateStock.do 요청이 왔을 때 실행되는 요청핸들러 메소드
@PostMapping("updateStock.do") 
public String updateStock(int no, int amount) {
    service.updateBookStock(no, price);
    return "redirect:detail.do?no=" + no;
}
```

> **jsp 하단의 javascript 코드**

```javascript

    // 1. 가격 변경 버튼 클릭 시
    $("#btn-update-price").click(function() {
        var price = parseInt($(":input[name=price]").val());
        var discountPrice = parseInt($(":input[name=discountPrice]").val());

        // form의 action 값이 updatePrice.do이기 때문에 attr로 속성을 변경할 필요가 없다.
        if (price >= discountPrice) {
            $("#form-update-book").trigger('submit');
        } else {
            alert('입력된 가격이 올바르지 않습니다.');
        }
    });

    // 2. 재고 변경 버튼 클릭 시
    $("#btn-update-stock").click(function() {
        $("#form-update-book").attr("action", "updateStock.do").trigger('submit');
    });
```

전체 코드는 **[링크](https://github.com/subtitle1/spring-mybatis)**에서 확인 가능합니다.<br>
(경로: src/main/webapp/WEB-INF/jsp/book/modifyForm)