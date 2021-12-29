---
layout: single
title: "[JAVA] ArrayList에 저장된 객체의 삭제"
excerpt: 'arrayList'
categories: Java
tag: Java, Object, Collection, arrayList
---

> **ArrayList에 저장된 <u>첫번째 객체부터 삭제</u>하는 경우**

- ArrayList의 요소를 삭제하는 경우, <u>삭제할 객체의 바로 아래에 있는 데이터를 한 칸씩 위로 복사</u>해서 삭제할 객체를 덮어쓰는 방식으로 처리한다.
- ArrayList에 저장된 **첫번째 객체부터 삭제하는 경우, 배열 복사가 발생**한다.

```java
for (int i = 0; i <= list.size(); i++) {
    list.remove(i);
}
```
- 위와 같은 코드로 삭제 시, i번째 인덱스에 해당하는 값을 삭제하고 그 아래에 남아있는 객체들은 위로 올라가게 된다. 
- 데이터의 이동이 발생한다. (배열 복사)
- 즉 아래 사진과 같이 모든 객체가 삭제되지 않고 남아 있다.

![image](https://user-images.githubusercontent.com/87356533/147568802-213be50a-3153-47df-8a2c-12fe114fe096.png)

---

> **ArrayList에 저장된 <u>마지막 객체부터 삭제</u>하는 경우**

- 마지막 객체부터 삭제하는 경우 배열 복사가 발생하지 않는다.
- list.size()는 현재 5이므로, 인덱스 번호에 맞게 -1을 해 준다.
- i는 4부터 시작되어 모든 객체를 다 삭제할 수 있다.
- **데이터의 이동이 없기 때문에**  작업 시간이 짧다.

```java
for (int i = list.size() - 1; i >= 0; i--) {
    list.remove(i);
}
```

![image](https://user-images.githubusercontent.com/87356533/147569401-b58b6684-578d-4319-b5a3-7fde2b463266.png)

참고: **[자바의 정석](https://youtu.be/_2e-cgwMOyc)**