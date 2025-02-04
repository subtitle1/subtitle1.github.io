---
layout: single
title: "[SpringBoot] exception 처리 리팩토링"
excerpt: ''
categories: Spring
tag: spring, java, mvc
---

예전에 관람평 작성 전 review exception을 처리하기 위해 구현했던 기능을 다시 리팩토링 해 보았다. 기존의 코드는 **[링크](https://subtitle1.github.io/spring/review-c/)**에서 확인 가능하다. 

원래 구현한 서비스 로직은 아래와 같이 두 가지 파트로 나누어 구분했었는데, 컨트롤러 측에서 exception 처리를 해 주어야 했기 때문에 컨트롤러의 코드가 길어지는 점이 마음에 들지 않았다.

```java
    public boolean isReserved(int customerNo, int movieNo) {
		List<String> isReserved = reviewMapper.getQualification(customerNo, movieNo);
		return !isReserved.isEmpty();
	}

	public boolean isWrited(int customerNo, int movieNo) {
		Review review = reviewMapper.getMyReviewByMovieNo(customerNo, movieNo);
		return review == null;
	}
```

## 수정한 service 코드
메소드를 위와 같이 두 파트로 나눌 필요 없이 하나의 메소드로 묶어주고, 기존 코드는 controller 측에서 exception 처리를 했었는데 그 부분도 비즈니스 로직에서 처리할 수 있도록 변경해 주었다.

```java
    public boolean getQualification(int customerNo, int movieNo) {
		List<String> isReserved = reviewMapper.getQualification(customerNo, movieNo);
		Review review = reviewMapper.getMyReviewByMovieNo(customerNo, movieNo);
		
		if (isReserved.isEmpty()) {
			throw new ReviewErrorException("관람평은 실관람 이후 작성 가능합니다.");
		}

		if (review != null) {
			throw new ReviewErrorException("이미 작성한 관람평이 있습니다.");
		}

		return true;
	}
```

## 수정한 controller 코드
서비스 코드를 수정하니 컨트롤러 측 코드가 훨씬 더 가벼워졌다! 아래와 같이 수정 후 오는 json 응답을 사진으로 확인해 보도록 하자.

```java
    @GetMapping("/check")
	public ResponseDto<Map<String, Object>> checkQualification(@LoginedUser Customer customer, int movieNo) {
		
		ResponseDto<Map<String, Object>> response = new ResponseDto<>();		
		boolean isPossibleToWrite = reviewService.getQualification(customer.getNo(), movieNo);
		
		if (isPossibleToWrite) {
			response.setStatus(true);
			response.setItems(Map.of("review", "작성 가능"));
		}
		return response;
	}
```

## json 응답
예매를 완료했고, 이미 작성한 관람평이 없다면 다음과 같은 json 응답이 오게 된다. 다음과 같은 응답이 오면 리뷰 모달창이 뜨게 된다.

![image](https://user-images.githubusercontent.com/87356533/155875184-c690acea-7934-4978-bad1-eb5ce5ba0183.png)

만약 작성한 관람평이 있거나, 예매하지 않았을 때 오는 json 응답과 모달창이다.
![image](https://user-images.githubusercontent.com/87356533/155875239-8086ffe1-156f-475f-b399-91d50232e434.png)
