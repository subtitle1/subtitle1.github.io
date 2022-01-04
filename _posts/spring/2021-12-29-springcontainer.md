---
layout: single
title: "[Spring] 스프링의 web.xml 살펴보기"
excerpt: 'web.xml'
categories: spring
tag: spring, java
---

# 1. Root Spring Container
- <span style="color:red;">**HTTP 요청을 직접 처리하지 않는 객체**</span>들이 생성되는 container이다.
- Root Spring Container에서 관리하는 주요 객체들은 아래와 같다.
    - Connection Pool**(BasicDataSource)**
    - DB 엑세스에 필요한 객체**(JdbcTemplate, xxxDaoImpl)**
    - 업무로직 실행에 필요한 객체**(xxxServiceImpl)**
    - 선언적 트랜잭션 처리를 지원하는 객체

<br>

# **Root Spring Container를 생성하는 주체**
- 아래 xml 코드를 살펴보면 ContextLoaderListener이 \<listener-class>에 정의되어 있다.
- ContextLoaderListener은 **ServletContextListener 인터페이스를 구현한 클래스**이다.
- 인터페이스 안에 정의된 메소드들은 각각 서버가 시작될 때, 서버가 종료될 때 실행되는 메소드이다.
    - contextInitialized();
    - contextDestroyed();
- contextInitialized 메소드는 Root Spring Container를 생성하고, <br> \<param-value /> 태그에 설정된 빈 파일을 읽어서 객체를 생성한다.

```xml
<!-- Root Spring Container 생성을 위한 설정 시작 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>
        /WEB-INF/context-root.xml
    </param-value>
</context-param>
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!-- Root Spring Container 생성을 위한 끝 -->
```

> **ContextLoaderListener**
- 웹 서버가 시작될 때 ContextLoaderListener의 **contextinitialized() 메소드가 실행**된다.
- 이 메소드에서는 스프링 빈 설정파일(context-root.xml)을 읽어서 객체를 생성한다.

---

# 2. Child Spring Container

- <span style="color:red;">**클라이언트의 HTTP 요청을 처리하는 객체들이 생성**</span>되는 container이다. (즉 컨트롤러를 말한다)
    - 요청을 처리할 컨트롤러와 요청핸들러 메소드를 실행한다.
    - 요청처리가 완료되면 jsp 페이지로 내부이동/재요청 URL을 응답으로 보낸다.
- DispatcherServlet 객체가 초기화 될 때 스프링 빈 설정파일(context-web.xml)을 읽어서 <br> Child Spring Container 객체를 생성한다.

```xml
<!-- Child Spring Container 생성을 위한 설정 시작 -->
<servlet>
    <servlet-name>action</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/context-web.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>action</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
<!-- Child Spring Container 생성을 위한 설정 끝 -->
```

<br>

# 3. context-web.xml 파일과 context-root.xml 파일
- context-root.xml 파일은 **[링크](https://subtitle1.github.io/spring/db-access/)**에서 확인할 수 있다.
- context-web.xml은 아래 코드과 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

	<mvc:annotation-driven></mvc:annotation-driven>
	<mvc:view-resolvers>
		<mvc:jsp prefix="/WEB-INF/jsp/" suffix=""/>
	</mvc:view-resolvers>
	<!-- 해당 경로에 있는 컨트롤러 객체를 생성한다 -->
	<context:component-scan base-package="com.sample.controller"></context:component-scan>
</beans>
```