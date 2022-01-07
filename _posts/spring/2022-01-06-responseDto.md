---
layout: single
title: "[Spring] 응답데이터의 정형화"
excerpt: 'ResponseDto<T>'
categories: Spring
tag: spring, java, mvc, restController, ResponseDto<T>, ajax
---

# 1. Json 응답데이터
클라이언트의 요청에 따라 다양한 요청이 올 수 있다. 예를 들어 책 상세정보를 조회했을 때 "/rest/book/detail.do?no=100"과 같은 요청이 온다면 응답 예시는 아래와 같을 것이다.
```xml
// 조회한 책 정보만 응답으로 보내기
{"itemNo":5, "bookNo":100, "title":"자바의 정석", "publisher":"블라블라", ...}
```
예시와 같이 JSON 형식의 응답에는 **Object 표기법, Array 표기법**밖에 존재하지 않는다.
object 표기법은 **{"key":value, "key"value}**와 같은 형식이고, array 표기법은 **[value, value, value]**와 같은 형식이다. 데이터의 응답을 정형화시키는 방법은 여러 가지가 있겠지만, 대표적으로 <span style="color:red">**ResponseDto\<T>를 사용**</span>한다. 

```xml
// 응답데이터의 정형화
{   
    "status": "OK",   // "OK", "FAIL"
    "error": null,    // status가 "FAIL"일 때 에러 메세지
    "items": []       // 응답 데이터, status가 "FAIL"이면 items는 null이다.
}

// 정형화 후 /rest/book/detail.do?no=100 요청 시의 응답
{
    "status": "OK"
    "error": null
    "items": [{"itemNo":5, "bookNo":100, "title":"자바의 정석", ...}]
}
```

> **1. ResponseDto\<T> 생성**

```java
    public class ResponseDto<T> {

        private String status;
        private String error;
        private List<T> items;
    }
    
    // 생성자 및 getter/setter, toString 생략
```

> **2. dao와 service 로직 구현**

```java
    // dao 객체(인터페이스)
    CartItem getCartItemByNo(int no);
    void deleteCartItem(int no);

    // service 객체
    public void deleteCartItem(int userNo, int itemNo) {
		CartItem cartItem = cartItemDao.getCartItemByNo(itemNo);
		if (cartItem.getUserNo() != userNo) {
			throw new RuntimeException("다른 사용자의 장바구니 아이템은 삭제할 수 없습니다.");
		}
		cartItemDao.deleteCartItem(itemNo);
	}
```

> **3. restController 구현**
- 예시는 카트의 리스트에서 삭제하는 방법을 다루었다.
- "/rest/cart/delete.do?no=5"와 같은 요청이 오면 실행되는 요청핸들러 메소드를 정의한다.
- 반환값은 ResponseDto<?>로 설정한다.

```java
@RestController
@RequestMapping("/rest/cart")
public class CartRestController {
    @GetMapping("/delete.do")
	public ResponseDto<?> delete(int no) {
		User user = (User)SessionUtils.getAttribute("LOGIN_USER");
		
		if (user == null) {
			ResponseDto<?> response = new ResponseDto<>();
			response.setStatus("FAIL");
			response.setError("로그인된 사용자가 아닙니다.");
			return response;
		}
		
		try {
			cartItemService.deleteCartItem(user.getNo(), no);
			
			ResponseDto<Integer> response = new ResponseDto<>();
			response.setStatus("OK");
			return response;
			
		} catch(RuntimeException e) {
			ResponseDto<?> response = new ResponseDto<>();
			response.setStatus("FAIL");
			response.setError(e.getMessage());
			return response;
		}
	}
```

> **4. restController 코드 순서대로 살펴보기**

```java
    User user = (User)SessionUtils.getAttribute("LOGIN_USER");
		
    if (user == null) {
        ResponseDto<?> response = new ResponseDto<>();
        response.setStatus("FAIL");
        response.setError("로그인된 사용자가 아닙니다.");
        return response;
    }
```

1. 세션에서 로그인된 사용자의 정보를 찾는다.
2. 사용자가 로그인 된 상태가 아니라면, response의 status와 에러 메세지를 설정한다.
3. 그대로 responseDto를 반환한다.

![image](https://user-images.githubusercontent.com/87356533/148388259-629a5f5e-30bb-434a-8769-86aa25fefb8e.png)

---

```java
    try {
        cartItemService.deleteCartItem(user.getNo(), no);
        
        ResponseDto<Integer> response = new ResponseDto<>();
        response.setStatus("OK");
        return response;
    }
```

1. 삭제 성공 시에 실행되는 로직이다. 
2. service를 실행해서 세션 정보에서 꺼낸 유저의 번호와 삭제할 카트의 번호를 담는다.
3. status를 OK로 설정하고, responseDto 객체를 반환한다.

![image](https://user-images.githubusercontent.com/87356533/148390014-75dcd4f3-8b3b-4a98-87ff-224387c5191c.png)

---

```java
    catch(RuntimeException e) {
            ResponseDto<?> response = new ResponseDto<>();
            response.setStatus("FAIL");
            response.setError(e.getMessage());
            return response;
        }
```

1. service 클래스에서 설정한 RuntimeException이 발생했을 때 실행되는 로직이다.
2. 에러 메세지를 담아 responseDto를 반환한다.
3. 로그인한 유저가 아닌 다른 사용자가 장바구니의 아이템을 삭제하지 못하게 방지하는 exception이다.

![image](https://user-images.githubusercontent.com/87356533/148390207-40be559d-5044-4e24-a8c5-5b8e47a2a539.png)
  
  <br>

전체 코드는 **[링크](https://github.com/subtitle1/sample-spring-app)**에서 확인 가능합니다. 
