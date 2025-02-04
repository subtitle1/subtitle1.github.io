---
layout: single
title: "[SpringBoot] JPA repository"
excerpt: ''
categories: Spring
tag: spring, java, mvc
---

## Spring Data JPA
반복되는 CRUD 문제를 해결하고, 개발자는 인터페이스만 작성한다. **스프링 데이터 JPA가 인터페이스의 구현 객체를 동적으로 생성해서 주입시켜주는 방식**이다. JPA에서 Repository는 인터페이스의 집합이다. 이 repository 안에 정의된 메소드들을 간략하게 살펴보자.

![image](https://user-images.githubusercontent.com/87356533/155283815-11d457c3-f90d-41cb-915d-ebead4eb6fe3.png)

jpa에서 제공하는 메소드들은 아래와 같이 간단한 쿼리의 실행이 가능하다. 그렇기 때문에 개발자는 repository 인터페이스가 제공하는 메소드만을 사용해 db 엑세스 / crud 작업이 가능해진다.

```sql
    select * from table;
    select * from table where 식별자 = id;

    insert into table(col1, col2, col3)
    values (value1, value2, value3);

    delete from table;
    delete from 식별자 = id;

    update table
    set
        col1 = value1,
        col2 = value2
    where 식별자 = id;
```

## Repository 생성
Repository는 아래 코드와 같이 생성 가능하다. 

```java
    public interface BookRepository extends JpaRepository<Book, Long> {

    }
```

인터페이스를 생성하면 JpaRepository 및 **JpaRepository가 상속받은 모든 인터페이스의 메소드를 사용**할 수 있다. BookRepository를 interface로 생성하면, Spring Data JPA가 이 repository의 구현 클래스를 자동으로 생성해 스프링의 빈으로 등록해 주는 것이다.

>
JpaRepository를 상속받을 때 <br>
- public interface SampleRepository extends JpaRepository\<T, ID> {} <br>
- T, ID를 정의해 주어야 하는데, 여기서 **T는 Entity, ID는 해당 Entity의 식별자 데이터 타입을 의미**한다.
{: .notice}

## Service
생성한 BookRepository를 service 측에서 아래와 같이 사용할 수 있다. 실제로 BookRepository 인터페이스 안에는 어떤 메소드도 정의되어 있지 않지만, JpaRepository를 상속받았기 때문에 그 안에 있는 메소드를 사용할 수 있는 것을 확인할 수 있다.

>
**@RequiredArgsConstructor**<br>
- mybatis를 사용했을 때는 @Autowired 어노테이션을 사용해 mapper를 주입시켜 줬었는데, 아래와 같이 상수(final)로 선언하는 것도 가능하다.<br>
- RequiredArgsConstructor 어노테이션은 초기화되지 않은 final 필드의 생성자를 생성해 준다.
{: .notice}

```java
    @RequiredArgsConstructor
    @Service
    public class BookService {

        private final BookRepository bookRepository;
        
        public List<Book> getAllBooks() {
            return bookRepository.findAll();
        }

        public Book getBookDetail(long bookId) {
            return bookRepository.getById(bookId);
        }
    }
```