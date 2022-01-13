---
layout: single
title: "[Vuejs] Component 구성 및 axios 연동"
excerpt: ''
categories: Vuejs
tag: Vuejs
---

# 1. Vue 컴포넌트 살펴보기
Components 폴더에는 Vue 컴포넌트들이 들어있다. Common 폴더는 페이지에 공통적으로 들어가는 vue 컴포넌트가, book 폴더에는 book 관련 vue 컴포넌트가, views에는 Content 컴포넌트에 렌더링 되는 컴포넌트가, 그리고 app에는 루트 vue 컴포넌트가 들어있다.

![image](https://user-images.githubusercontent.com/87356533/149303166-db2b7a9d-3986-4bd8-b0e2-0a90f198741f.png)

루트 vue 컴포넌트인 app.js는 아래와 같이 정의된다. 

```js
<template>
  <div>
    <Header />
    <Content /> // 해당 컴포넌트에 views 폴더에 정의한 컴포넌트들이 들어간다
    <Footer />
  </div>
</template>

<script>
import Header from './components/common/Header.vue';
import Content from './components/common/Content.vue';
import Footer from './components/common/Footer.vue';

export default {
  name: 'bookstore',
  components: {
    Header,
    Content,
    Footer
  }
}
</script>

<style>
</style>

```

<br>
---

# 2. axios 설치하기
axios는 node.js와 브라우저를 위한 Promise 기반 HTTP 클라이언트이다. 즉, **서버와 HTTP 통신을 지원하는 라이브러리**이다. axios의 주요 API는 **get, post, put, delete**가 있고, 메소드 실행 시에는 then(성공 시 수행되는 메소드), catch(에러 응답 시 수행되는 메소드), then(항상 실행되는 메소드)를 포함하고 있는 promise 객체가 반환된다.
<br><br>
axios를 설치하는 방법은 간단하다. ctrl + `을 눌러 터미널 창을 키고
```
npm -save install axios
```
를 입력하면 된다. package.json에서 axios가 다음과 같이 추가되어 있으면 정상적으로 설치된 것이다.
```
"dependencies": {
    "axios": "^0.24.0",
    "core-js": "^3.6.5",
    "vue": "^3.0.0",
    "vue-router": "^4.0.0-0"
  }
```

<br>
---

# 3. axios의 요청 처리
아래 예시는 get, post, put, delete 메소드 모두 해당되는 처리 방식이다.

```javascript
    axios.get(url)
        .then(function(response) {
            // 성공적인 응답 핸들링
        })
        .catch(function(error) {
            // 에러 응답 핸들링
        })
        .then(function() {
            // 항상 실행되는 영역
        })
```

성공적인 응답이 왔을 때 콜백함수로 전달되는 response 정보는 아래와 같다.

```javascript
    {
        data: {}, // 서버가 제공하는 응답데이터
        status: 200, // HTTP 응답 코드
        statusText: "OK", // HTTP 상태 메세지
        headers: {}, // 응답 헤더 정보
        config: {}, // 요청을 위해서 axios가 사용한 구성정보
        request: {} // 이번 응답에 대한 요청, web에서 XMLHttpRequest다
    }
```

<br>
---

# 4. axios 초기화 및 서버 요청 전송
해당 코드는 src 폴더 하위에 service 폴더를 따로 만들어서 작성한 **BookService.js 코드**이다.

```javascript
    // 1. axios 임포트
    import axios from 'axios';
    
    // 2. 초기화한 axios의 정보를 http 객체에 저장한다
    // baseURL은 json 형태의 텍스트 정보를 받아올 곳으로 정의한다
    const http = axios.create({
        baseURL: 'http://localhost/api',
        withCredentials: false, // 인증정보를 전송하지 않는다는 의미이다
        headers: {
            Accept: 'application/json', // 응답컨텐츠의 타입
            'Content-Type': 'application/json' // 요청컨텐츠의 타입 
        }
    });

    // 스크립트에서는 다른 모듈을 import하거나 해당 모듈을 export 할 수 있다.
    // 타 컴포넌트에서 import 할 수 있다.
    export default {
        getAllBooks() { // springboot에서 정의해놓은 service 메소드
            // 서버로 요청 보내기
            // 요청방식: GET
            // 요청URL: http://localhost/api/book
            return http.get("/book"); // http.get, http.put, http.post 등등이 존재함
        }
    }
```

<br>
---

# 5. export한 메소드의 사용
위에서 정의한 getAllBooks() 메소드를 BookList 컴포넌트에서 정의해 보도록 하자. script 코드 부분만 확인해 보면 아래와 같다.

> BookList 스크립트 코드

```js
    // 1. 해당 컴포넌트에서 사용하는 자식 컴포넌트를 import한다.
    import SearchForm from "../components/book/SearchForm.vue";
    import List from '../components/book/List.vue';
    import Pagination from '../components/common/Pagination.vue';

    // 2. 해당 컴포넌트에서 사용하는 공통기능(서비스)이 구현된 객체를 import한다.
    import BookService from '../service/BookService';

    export default {
        name: "BookList", // 컴포넌트의 이름을 정의한다
        components: {     // 해당 컴포넌트가 가지는 자식 컴포넌트들을 정의한다
            SearchForm,
            List,
            Pagination,
        },
    // data() 메소드가 반환하는 객체는 이 컴포넌트가 가지고 있는 데이터이다.
    data() {
        return {
            books: []
        }
    },
    // created()는 서버와 통신해서 컴포넌트의 초기 데이터를 가져오는 작업을 수행하기 적절한 라이프 사이클 메소드다.                 
    // Vue 컴포넌트의 라이프사이클 메소드이다. 
    // Vue 컴포넌트 객체가 생성되었고, data에 접근할 수 있지만, 아직 화면에 마운트되지는 않았다.
    created() {        
        BookService.getAllBooks()
        .then(response => this.books = response.data)
        .catch(error => console.log(error))
        .then(() => console.log('서버로부터 응답이 옴'));
    }
}
</script>
```

> BookList template 코드

template 코드는 아래와 같다. **BookList 컴포넌트**는 아래 코드와 같이 **SearchForm, List, Pagination이라는 자식 컴포넌트로 구성**되어져 있다. \<List> 태그를 살펴보면 :bookList="books"라고 정의되어 있는 것을 확인할 수 있는데, bookList는 이 컴포넌트가 전해 줄 데이터를 받을 List 컴포넌트의 props 이름이다.

```jsp
    <template>
        <main>
            <!-- script에서 import한 컴포넌트들 -->
            <SearchForm /> 
            <List :bookList="books"/>
            <Pagination />
        </main>
    </template>
```

> bookList를 전달받은 List 컴포넌트의 스크립트 코드

이제 List 컴포넌트에서 props 이름을 어떻게 사용하는지 확인해 보도록 하자. 
```js
    export default {
        name: "List",
        props: ["bookList"] // BookList 컴포넌트에서 전달해 준 props 이름
    }
```

리스트의 형태로 전달받았으니, template에서는 **\<v-for="book in bookList" :key="book.no">**와 같은 형태로 반복해서 쓸 수 있다.
