---
layout: single
title: "[SpringBoot] 스프링부트 설정"
excerpt: 'with lombok'
categories: Spring
tag: Spring, lombok
---

# 1. 스프링부트가 지향하는 목표
스프링과 다르게 설정을 최소화시키고, 의존성 설정도 스프링보다 훨씬 간편해졌다. 규모와 목적에 상관없이 실행가능한 환경을 빠르게 만드는 것이 스프링부트가 지향하는 목표이다. 스프링부트 프레임워크는 **COC(convention over configuration, 설정보다 관습) 원칙**을 준수한다. 


![image](https://user-images.githubusercontent.com/87356533/149317193-7abe909b-6c81-429f-a998-0c8604d04286.png)


프로젝트 생성 후에 SpringApplication 파일과 application.properties 파일이 생성되어 있는 것을 확인할 수 있을 것이다. **SpringApplication**은 main 메소드를 갖는 클래스이고, **<u>이 클래스가 위치한 하위 패키지에 있는 클래스들을 스프링의 빈으로 자동으로 등록</u>**한다. properties 파일은 기존 스프링의 xml 설정 파일을 대신한다. 톰캣이 내장되어 있고 서버 설치가 따로 필요없다.


SpringApplication 소스를 열어보면 **@SpringBootApplication** 어노테이션이 붙어 있는 것을 확인할 수 있다. 해당 인터페이스에 접근해 확인한 코드는 아래와 같다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication { ... }
```

> 간략한 어노테이션 설명 
- **@Configuration**
    - 자바클래스가 스프링 빈 설정파일(xml)을 대신한다.
- **@EnableAutoConfiguration**
    - 프로젝트에 라이브러리들이 포함되어 있는지 체크하고, application.properties 파일을 분석한다.
- **@ComponentScan**
    - 이 클래스가 위치한 패키지 및 그 하위 패키지의 클래스들을 스캔해서 스프링의 빈으로 등록한다.

<br>
---

# 2. 스프링부트의 application.properties 파일 설정
properties 파일은 name=value 형식이고, #은 주석을 의미한다.

```java
# logging level
logging.level.root=INFO

# mybatis mapper logging level
logging.level.com.sample.bookstore.mapper=TRACE

# webserver port number
server.port=your port number

# connection pool
spring.datasource.driver-class-name=oracle.jdbc.OracleDriver(example)
spring.datasource.url=jdbc:oracle:thin:@localhost:1521:xe(example)
spring.datasource.username=your username
spring.datasource.password=your password

# mybatis
mybatis.config-location=classpath:/mybatis/mybatis-config.xml
mybatis.mapper-locations=mybatis/mappers/*.xml
```

<br>
---

# 3. lombok을 사용한 VO 객체 정의
설정이 간편해진 것 외에 개발 방식은 기존의 스프링과 같다. 또, getter/setter와 생성자를 lombok 라이브러리를 이용해 더욱 간략하게 정의해 줄 수 있다. 참고로 **@Data 어노테이션**은 getter, setter, toString을 한꺼번에 정의해 주는 어노테이션이다. @Data를 사용하지 않고 **@Getter, @Setter, @ToString**과 같이 정의하는 것도 같다. vo 객체를 살펴보자.

```java
    @NoArgsConstructor // 매개변수 없는 기본 생성자
    @AllArgsConstructor // 필드의 모든 매개변수를 가지고 있는 생성자
    @Builder
    @Data
    public class Book {

        private int no;
        private String title;
        private String author;
    }
```

# 4. Dao 설정
![image](https://user-images.githubusercontent.com/87356533/149320563-73c62188-e6d7-4525-80da-99ef42e49156.png)

src/main/java 폴더의 mapper 패키지를 생성하고, mapper interface를 생성해 그 안에 메소드를 정의한다. src/main/resources에는 mybatis 설정파일과 mapper.xml 파일이 존재하는데 **xml파일명은 사진에서 체크해 둔 것처럼 mapper 인터페이스의 클래스명과 동일해야 한다.**
`mybatis-config.xml 설정파일은 스킵!`


매퍼스캔 설정은 SpringApplication 클래스에서 정의할 수 있다. **해당 클래스 하위에 있는 패키지만 스캔 가능**하니까 주의하도록 하자.

```java
    @SpringBootApplication
    @MapperScan(basePackages = "com.sample.bookstore.mapper")
    public class SpringbootBookstoreApplication {

        public static void main(String[] args) {
            SpringApplication.run(SpringbootBookstoreApplication.class, args);
        }
    }
```