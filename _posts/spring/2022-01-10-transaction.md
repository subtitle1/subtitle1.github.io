---
layout: single
title: "[Spring] 선언적 트랜잭션"
excerpt: '@Transactional'
categories: Spring
tag: spring, java, mvc, transactional
---

# 1. @Transactional
트랜잭션 처리란 **<u>부분적인 성공을 지원하지 않는다</u>**는 의미이다. **여러 개의 서비스 실행 중 한 곳이라도 에러가 발생하면 모든 메소드 실행을 취소한다.** (all or nothing) <br>
컨트롤러가 실행될 때 여러 서비스 메소드가 실행될 수 있다. 서비스 실행 중 하나라도 오류가 나면 모든 작업이 취소되고, rollback 되어야 하는데 이를 가능하게 만들어 주는 것이 **transactional 어노테이션**이다. 트랜잭션 처리가 적용될 곳은 서비스 객체의 업무로직 메소드이다. 트랜잭션 처리를 지원받기 위해 어노테이션을 인터페이스, 클래스, 혹은 메소드에 붙여서 사용한다.

```java
@Service
@Transactional
public class BookService {

    // 구현 생략
}
```

# 2. context-root.xml 설정
1. 선언적 트랜잭션 처리를 지원하는 트랜잭션매니저 객체를 스프링 컨테이너에 등록시키기
    - spring-jdbc, ibatis, mybatis를 사용해서 데이터베이스 엑세스를 구현한 경우, <br>
    DataSourceTransactionManager를 스프링 컨테이너에 빈으로 등록시킨다.
2. @Transactional 어노테이션을 활성화해서 @Transactional이 부착된 객체의 모든 메소드가 실행될 때 마다 스프링이 트랜잭션처리를 지원한다.
    - 어노테이션은 인터페이스, 클래스, 메소드에 부착할 수 있다.
    - 메소드 > 클래스 > 인터페이스의 순서로 우선순위가 높다.

```xml
    // 1.
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>

    // 2.
    <tx:annotation-driven transaction-manager="txManager"/>
```