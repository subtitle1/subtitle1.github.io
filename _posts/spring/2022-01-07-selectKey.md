---
layout: single
title: "[Spring] mybatis의 selectKey 태그"
excerpt: 'selectKey'
categories: Spring
tag: MVC, model, mybatis, sql
---

# 1. selectKey
selectKey 태그는 현재 테이블에 저장되는 **데이터의 기본키값을 획득**하기 위한 용도로 사용되는 태그이다. 테이블에 저장되는 기본키 값이 다른 테이블의 데이터를 추가할 때 필요한 경우, \<selectKey /> 태그를 사용해 값을 획득하고 parameterType으로 지정된 객체의 멤버변수에 저장한다.

> **selectKey 태그의 속성**
- keyProperty: selectKey 구문의 결과가 저장되는 프로퍼티(멤버변수)
- resultType: 결과의 타입
- order: BEFORE / ARTER를 허용한다. BEFORE로 설정하면 selectKey 구문이 먼저 실행되고, 그 후에 다음 쿼리가 실행된다.

# 2. 사용 예시
> **1. VO, DTO 구성**

```java
public class BookPicture {

	private int bookNo;
	private String picture;

    // constructor, getter/setter 생략
}
```

```java
public class Book {

	private int no;
	private String title;
	private String author;
	private String publisher;
	private int price;
	private int discountPrice;
	@JsonFormat(pattern = "yyyy년 M월 d일")
	private Date pubDate;
	private int stock;
	@JsonFormat(pattern = "yyyy년 M월 d일")
	private Date updatedDate;
	@JsonFormat(pattern = "yyyy년 M월 d일")
	private Date createdDate;

    // constructor, getter/setter 생략
}
```

```java
public class BookDetailDto {
	private int no;
	private String title;
	private String author;
	private String publisher;
	private int price;
	private int discountPrice;
	@JsonFormat(pattern = "yyyy년 M월 d일")
	private Date pubDate;
	private int stock;
	@JsonFormat(pattern = "yyyy년 M월 d일")
	private Date updatedDate;
	@JsonFormat(pattern = "yyyy년 M월 d일")
	private Date createdDate;
	private List<BookPicture> BookPictures;

    // constructor, getter/setter 생략
}
```

<br>
---

> **2. 책 정보와 책 사진을 insert하는 sql 구현**
- void insertBook(Book book) / void insertBookPicture(Bookpicture bookPicture)
- selectKey 구문에서 실행된 값은 Book 객체의 프로퍼티인 no에 저장된다. inserBook 구문이 실행된 후 book.getNo()로 값을 얻을 수 있다.

```sql
    <insert id="insertBook" parameterType="com.sample.vo.Book">
        <selectKey keyProperty="no" resultType="int" order="BEFORE">
            select books_seq.nextval
            from dual
        </selectKey>
        insert into sample_spring_books
        (book_no, book_title, book_author, book_publisher, book_price, book_discount_price, book_pub_date, book_stock)
        values
        (#{no}, #{title}, #{author}, #{publisher}, #{price}, #{discountPrice}, #{pubDate}, #{stock})
    </insert>

	<insert id="insertBookPicture" parameterType="com.sample.vo.BookPicture">
		insert into sample_spring_book_pictures
		(book_no, book_picture)
		values
		(#{bookNo}, #{picture})
	</insert>
```

<br>
---

> **3. service 구현**

```java
    public void addNewBook(Book book, List<BookPicture> bookPictures) {
        bookDao.insertBook(Book);

        for (BookPictures picture : bookPictures) {
            picture.getNo(book.getNo());
            bookDao.insertBookPicture(picture);
        }
    }
```

Controller 코드는 **[링크](https://subtitle1.github.io/spring/upload/)** 하단에서 확인 가능합니다.