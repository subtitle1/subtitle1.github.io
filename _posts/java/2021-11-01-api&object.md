---
layout: single
title: "[JAVA] Object와 API"
excerpt: 'Object 클래스의 주요메소드'
categories: Java
tag: Java, Object
---
# API (Application Programming Interface)
- 자바를 이용해서 애플리케이션을 구현할 수 있도록 지원하는 **라이브러리들의 집합**
- 자바에서는 개발자들의 구현 부담을 최소화 하기 위해서 **시스템, 입출력, 화면처리, 네트워크, 스레드 등을 구현할 때 필요한 인터페이스나 클래스**들을 미리 구현해서 제공하고 있는데 이것을 Java API라고 한다.

---

# API 문서				
- Java API에서 제공하는 여러 인터페이스나 클래스들에 대한 설명 및 사용법을 제공하는 문서							
- 소스코드에서 Document 주석을 사용해서 설명이나 사용법을 추가해놓으면 javadoc.exe로 자동으로 API 문서를 생성할 수 있다.							
- 이클립스는 해당 프로젝트에서 마우스 우클릭 -> export -> java -> javadoc 기능을 이용해 API 문서를 생성할 수 있다.							
- API 문서는 HTML 형식의 문서로 작성된다.							

---
# API 구성								
- Java SE 11 API > Module > package > interface + class		
    - Desktop에서 실행할 수 있는 응용 프로그램 개발에 필요한 Java API를 제공한다.						
    - 여러 Module로 구성되어 있다.						
                            
- Module							
    - Java 9버전부터 지원하는 자바의 새로운 그룹화 방식이다.
    - 하나의 모듈은 여러 개의 패키지를 포함하고 있다.				
    - java.base 모듈이 가장 많이 사용되는 모듈이다.	<br>			
    (java.lang, java.io, java.net, java.util 패키지 등을 포함하고 있는 모듈이다)						
- Package							
    - 관련 있는 클래스나 인터페이스의 묶음이다.						
    - 같은 패키지 내의 클래스들끼리는 import없이 다른 클래스를 사용할 수 있지만,						
        패키지가 다른 경우에는 반드시 import 구문을 사용해서 포함시킬 패키지명과 클래스명을 정의해 주어야 한다.						
    - 대표적인 패키지						
        - **java.lang** 	
            - 가장 기본적인 인터페이스, 클래스들을 포함하고 있는 패키지				
            - 이 패키지의 인터페이스나 클래스는 import 구문없이 바로 사용할 수 있다				
            - Object, Class, System, String, Wrapper 클래스 등을 포함하고 있다				      
        - **java.io**	
            - 입출력과 관련된 인터페이스, 클래스들을 포함하고 있는 패키지		
            - xxxInputStrean, xxxOutputStream, xxxReader, xxxWriter 클래스를 포함하고 있다			        
        - **java.net**					
            - 네트워크 통신을 지원하는 인터페이스, 클래스들을 포함하고 있는 패키지				
            - Socket, ServerSocket, URL 등의 클래스를 포함하고 있다				       
        - **java.util**					
            - 자료구조, 날짜/시간 등을 다루는 유용한 클래스들을 포함하고 있는 패키지				
            - 애플리케이션 구현에서 가장 많이 사용하는 패키지다		
            - Collection, List, Set, Map, Date, Calendar 등을 포함하고 있다	

---

> **java.lang**

- 자바의 가장 기본적인 인터페이스, 클래스를 포함하고 있는 패키지다.
- Object							
	- 모든 객체의 최상위 부모 클래스다.						
	- Object 클래스의 메소드는 모든 객체에서 사용 가능하다.					
- Class							
	- 객체의 설계도 정보를 표현하는 클래스다.						
	- 클래스명, 필드, 생성자, 메소드의 정보를 포함하고 있는 객체다.					
- Boolean, Chracter, Byte, Short, Integer, Long, Float, Double							
	- Wrapper 클래스다.						
	- 기본자료형 타입의 값을 객체로 표현할 때 사용되는 클래스다.						
- String, StringBuffer, StringBuilder							
	- String은 문자열을 표현하는 클래스다. 문자열을 조작하는 다양한 메소드를 포함하고 있다.						
	- StringBuffer와 StringBuilder은 내부에 버퍼(임시저장소)가 있어서 문자열을 추가할 수 있다.						
							
- Math							
	- 수학연산과 관련된 상수와 정적메소드를 제공한다.						
	- 클래스의 모든 메소드가 정적메소드다.						
							
- System, Runtime							
	- System은 자바프로그램이 실행 중인 운영체제와 관련된 정보를 제공하는 클래스다.						
	- Runtime은 운영체제와의 인터페이스를 지원하는 클래스다. 운영체제와 관련된 명령어를 실행할 수 있다.

- Thowable
	- Error와 Exception 클래스의 부모 클래스다.				
	- 자바에서 발생하는 모든 오류 정보를 표현하는 것이 Error와 Exception 클래스다.						

# Object 클래스의 주요 메소드
- boolean equals(Object obj): 현재 객체와 전달받은 객체가 동일한 객체인지의 여부 반환
- int hashCode(): 객체의 해시코드 값 반환
- String toString(): 클래스이름@16진수해시코드 값 반환
- Class<?> getClass(): 클래스 정보 반환
- Object clone(): 현재 객체를 복제한 복사본 반환
