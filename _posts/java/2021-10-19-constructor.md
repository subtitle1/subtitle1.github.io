---
layout: single
title: "[JAVA] 생성자, constructor"
excerpt: 'constructor'
categories: Java
tag: Java, constructor, Java_basic
---

# 생성자 (constructor)
- 생성자는 메소드이다.
- 생성자는 클래스와 동일한 이름을 가진다.
- 반환타입이 없다.
- 생성자가 하나도 정의되어 있지 않으면 컴파일 시에 실행파일(Student.class)에 기본생성자 메소드가 자동으로 추가된다.

```java
public class Student {
    // 클래스 Student의 필드 (멤버변수)
    int no;
    int grade;
    String name;
    
    // 기본 생성자 메소드
    public Student() {
    ...
    }
    
    // 기본 생성자 메소드는 매개변수만 다르게 해서 중복정의가 가능하다
    public Student(String name, int no) {
    	this.name = name;
        this.no = no;
    }
}
```

```java
public static void main(String[] args) {
	// Student 객체 생성하기
	Student student1 = new Student();
        Student student2 = new Student();
}
```

## 생성자 메소드 사용하기
- Student student1 = new Student(); 에서 `Student();`가 바로 생성자 메소드이다.
- new 키워드 다음에 생성자 메소드를 적는다.
- new 키워드는 생성자 메소드의 이름과 동일한 이름의 실행파일(Student.class)를 메모리에 로딩한다.
- 메모리에 로딩된 실행파일(Student.class)을 설계도 삼아 Student 객체를 생성한다.
- Student 객체의 생성이 완료되면, 생성자 메소드를 실행해서 객체 생성 직후에 수행할 작업을 실행시킨다.
- Student 객체의 주소값이 참조변수 student1에 대입된다.
