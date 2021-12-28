---
layout: single
title: "[JAVA] Collection 프레임워크"
excerpt: 'List, Set'
categories: Java
tag: Java, Object, Collection
---

> **Collection Framework**

- **자바가 구현해놓은 자료구조**이다.
    - 자료구조란? <span style="color:red">**객체의 저장, 삭제, 조회 등의 기능을 제공**</span>하는 것이다.
- 자바의 모든 자료구조 구현 클래스는 Collection 인터페이스를 구현한 클래스다.
- 자바의 자료구조 특징
    - 객체만 저장할 수 있다.
    - 크기가 가변적이다.
    - 다양한 메소드를 지원한다.

---

> **Collection\<E>**

- 모든 자료구조 클래스의 최상위 인터페이스
- 주요 메소드
    - **boolean add(Object o)**
    - **boolean addAll(Collection c)**
        - 지정된 객체(o) 혹은 Collection(c)의 객체들을 Collection에 추가한다.
    - **void clear()**
        - Collection의 모든 객체를 삭제한다.
    - **boolean contains(Obect o)**
    - **boolean containsAll(Collection c)**
        - 지정된 객체(o) 혹은 Collection(c)의 객체들이 Collection에 포함되어 있는지 확인한다.
    - **boolean equals(Object o)**
        - 동일한 Collection인지 비교한다.
    - **int hashCode()**
        - Collection의 hash code를 반환한다.
    - **boolean isEmpty()**
        - Collection이 비어있는지 확인한다.
    - **iterator iterator()**
        - Collection의 iterator를 얻어서 반환한다.
    - **boolean remove(Object o)**
        - 지정된 객체를 삭제한다.
    - **boolean removeAll(Collection c)**
        - 지정된 Collection에 포함된 객체들을 삭제한다.
    - **boolean retainAll(Collection c)**
        - 지정된 Collection에 포함된 객체만을 남기고 다른 객체들은 Collection에서 삭제한다. 이 작업으로 인해 Collection에 변화가 있으면 ture를, 그렇지 않으면 false를 반환한다.
    - **int size()**
        - Collection에 저장된 객체의 개수를 반환한다.
    - **Object[] toArray()**
        - Collection에 저장된 객체를 객체배열(Object[])로 반환한다.
    - **Object[] toArray(Object[] a)**
        - 지정된 배열에 Collection의 객체를 저장해서 반환한다.

---

> **Collection의 주요 하위 인터페이스**

- **Map** 인터페이스는 List나 Set과 전혀 다른 형태로 컬렉션을 다루기 때문에 Collection 인터페이스의 상속을 받지 않는다. 

- **List**
    - <span style="color:red"><u>순서가 있는 데이터의 집합으로, 데이터의 중복을 허용</u></span>한다.
    - 요소가 저장될 때마다 index가 자동으로 부요된다.
    - **특정 위치에 요소를 저장하거나, 삭제하거나, 꺼내는 작업**에 용이하다.
    - 구현클래스: ArrayList, LinkedList, Stack 등
    - List<E>가 지원하는 메소드
        - void add(int index, Object element)
        - Object get(int index)
        - Object remove(int index)
        - Object set(int index, Object element) 등이 있다.

- **Set**
    - <span style="color:red"><u>순서를 유지하지 않는 데이터의 집합으로, 데이터의 중복을 허용하지 않는다.</u></span>
    - 구현클래스: HashSet, TreeSet 등
    - Collection 인터페이스로부터 상속받은 메소드 외 추가된 메소드가 존재하지 않는다.