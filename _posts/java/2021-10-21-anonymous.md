---
layout: single
title: "[JAVA] 익명객체"
excerpt: 'anonymous'
categories: Java
tag: Java, anonymous object
---

# 익명객체

## 익명객체 예시
```java
public interface Click {
	void actionPerformed();
}
```

- Click이라는 인터페이스를 먼저 구현한다.

```java
public class ClickApp {

	public static void main(String[] args) {
		Click homeButtonClick = new Click() {
			public void actionPerformed() {
				System.out.println("홈페이지를 표시합니다.");
			}
		};
		
		Click prevButtonClick = new Click() {
			public void actionPerformed() {
				System.out.println("이전 페이지를 표시합니다.");
			}
		};
		
		Click nextButtonClick = new Click() {
			public void actionPerformed() {
				System.out.println("다음 페이지를 표시합니다.");
			}
		};
		
		// 람다표현식
		Click nextButton = () -> {System.out.println("다음 페이지를 표시합니다.");};
	}
}
```
- Clink 인터페이스를 구현한 클래스는사용되는 곳이 한 곳 뿐이기 때문에 객체를 여러 번 생성할 필요가 없다.
- 클래스를 생성하지 않고도 객체를 생성할 수 있는 방법을 제공하는 것이 익명객체다.

## 익명객체 사용법
- 익명객체를 사용하면 **별도의 클래스 구현 없이 객체를 생성**할 수 있다.
- 익명객체를 생성하기 위해서는 **반드시 부모(인터페이스, 추상클래스)가 필요**하다.
- 부모의 **불완전메소드(추상메소드)를 재정의한 코드로 객체를 생성**한다.
- 딱 한 번밖에 사용되지 않는 객체에 익명객체를 주로 사용한다.
