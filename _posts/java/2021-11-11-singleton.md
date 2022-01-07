---
layout: single
title: "[JAVA] 싱글턴 객체"
excerpt: ''
categories: Java
tag: Java, OOP, Singleton
---

# 싱글턴 객체 (Singleton Instance)
- 프로그램이 실행되는 동안 오직 하나의 객체만 생성해서 사용하는 객체
```java
// 싱글턴 객체로 사용하기
private static UserDao self = new UserDao(); // 객체를 생성해 정적변수에 저장한다
private UserDao() {}                         // 생성자의 접근제한을 private로 설정한다
public static UserDao getInstance() {        // 생성한 객체를 반환하는 정적 메소드를 제공한다
  return self;
}
```
-------------------------------------------------------------
// 싱글턴 객체의 호출
```java
// static으로 선언한 메소드는 클래스명.메소드()로 활용할 수 있다
UserDao userDao = UserDao.getInstance();
```
