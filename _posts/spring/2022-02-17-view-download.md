---
layout: single
title: "[SpringBoot] 파일 다운로드"
excerpt: ''
categories: Spring
tag: spring, java, mvc, jsp
---

## 1. View
파일 다운로드 코드를 보기 전에, View의 개념을 한번 살펴보고 가자. **<u>View</u>**는 **Model에 저장된 데이터를 특정 타입의 컨텐츠로 변환해서 클라이언트에게 응답으로 제공하는 객체**이다. 즉, 단순히 화면만을 제공하는 것이 View가 아니라 **특정 타입의 컨텐츠로 변환하는 것 자체를 view**라고 한다.

MVC 실행 순서를 간략하게 살펴보자면 아래와 같다.

> 1. HandelrAdapter가 컨트롤러를 실행하고, 컨트롤러는 ModelAndView 객체를 반환한다.
2. ModelAndView 안에는 크게 세 가지 정보가 저장된다
    - String viewName
    - Map<String, Object> model
    - View view
3. dispatcherServlet은 ModelAndView 객체를 전달받고, view가 null인지 아닌지를 확인한다.
4. dispatcherServlet은 viewResolver에게 view 이름을 확인하고, 
5. viewResolver는 redirectView, JstlView, InternalResourceView 중 하나를 다시 dispatcherServlet에게 반환한다.
6. 서블릿은 자신이 전달받은 그 뷰를 render() 메소드로 실행한다.
{: .notice}

View의 개념은 아래와 같다.

> **View** <br>
**특정 타입의 컨텐츠로 변환하는 것**
1. redirectView
    - 재요청URL을 응답으로 제공
2. JstlView, InternalResourceView 
    - jsp를 실행시켜서 text/html을 제공 (Model -> text/html)
3. JacksonDataBinder가 제공하는 View (컨트롤러가 반환하는 데이터를 json 형태로 변환해서 반환)
    - Json 응답을 제공 (Model, 혹은 반환하는 데이터 -> application/json)
4. FileDownloadView
    - 첨부파일을 응답으로 제공 (Model(디렉토리 경로, 파일명) -> binary 데이터)
5. ExcelView
    - 엑셀파일을 응답으로 제공 (Model(객체) -> 엑셀 파일)
{: .notice}

## 2. 컨트롤러 코드
다음 코드는 서버에 저장된 파일을 내려받는 코드이다. 파일이 이미 만들어져 있어서 해당 파일이 있는 위치나 디렉토리만 따로 설정해 주면 된다. 아래에서 사용한 **classpath는 jar 파일의 특정 위치, src/main/java의 특정 위치, resources의 특정 위치**에서 찾을 수 있는 것이다.

아래 코드를 살펴보면,
1. 요청핸들러 메소드가 ModelAndView객체에 어떤 View를 사용할지 지정하였다. (`mav.setView("내려보낼 view 객체")`)
2. DispatcherServlet은 ModelAndView에 포함된 FileDownload를 사용한다.
3. FileDownloadView는 디렉토리명, 파일명 혹은 클래스패스 경로에 해당하는 파일을 읽어서 응답으로 제공한다.
4. jsp에서 a 태그를 정의하고, href는 /book/books로 하면 정의한 경로에 있는 파일이 다운로드된다.

```java
    @Controller
    @RequestMapping("/book")
    public class BookController {

        @Autowired
        private FileDownloadView filedownloadView;

        @GetMapping("/books")
        public ModelAndView booksListFile() {
            ModelAndView mav = new ModelAndView();

            // ModelAndView 객체에 View 객체 저장
            mav.setView(filedownloadView);

            // ModelAndView 객체에 Model 정보 저장
            mav.addObject("classpath", "classpath:excel/도서목록.xlsx");

            // classpath 말고 아래와 같이 정의해 줘도 된다.
            // mav.addObject("directory", "전체 디렉토리 경로");
            // mav.addObject("filename", "파일명");

            return mav;
        }
    }
```

## 3. FileDownloadView 코드
response 객체에는 바이너리 파일을 내려보내기 위한 컨텐츠 타입 및 응답헤더 설정이 필요하다.

> **application/octet-stream**<br>
    - 텍스트 파일이 아닌 다른 모든 경우를 위한 기본값이다. 알려지지 않은 파일 타입은 이 타입을 사용해야 한다. <br><br>
**Content-Disposition**<br>
    - multipart 본문 내의 필드에 대한 정보를 제공한다. <br><br>
**attachmentl filename**<br>
    - 다운로드로 동작하는 첨부파일이라는 뜻으로, 정의해주지 않으면 브라우저에서 바로 열린다. <br><br>
**URLEncoder**<br>
    - 인코딩해 주지 않으면 파일명이 깨지기 때문에 정의해 주어야 한다.
{: .notice}

1. `String classpath = (String) model.get("classpath");` 는 위 컨트롤러에서 `mav.addObject();`로 정의한 클래스패스를 가져온다.
2. classpath가 null이 아니라면 ResourceUtils(org.springframework.util.ResourceUtils)를 사용해 경로에 저장된 file을 가져와 file에 저장한다.


```java
    @Slf4j
    @Component
    public class FileDownloadView extends AbstractView {

        @Override
        protected void renderMergedOutputModel(Map<String, Object> model, HttpServletRequest request,
                HttpServletResponse response) throws Exception {
            
            String classpath = (String) model.get("classpath"); 
            String directory = (String) model.get("directory"); 
            String filename = (String) model.get("filename");

            log.info("클래스패스: " +classpath); 
            log.info("디렉토리 경로: " +directory); // null
            log.info("파일명: " +filename); // null
            
            
            File file = null;
            if (classpath != null) {
                file = ResourceUtils.getFile(classpath);
            } else {
                file = new File(directory, filename);
            }

            response.setContentType("application/octet-stream");
            response.setHeader("content-Disposition", "attachment; filename=" + URLEncoder.encode(file.getName(), "UTF-8"));
            
            // 파일을 읽어오는 스트림 객체
            InputStream in = new FileInputStream(file);
            
            // 응답객체에서 브라우저로 내려보내는 스트림 객체 획득
            OutputStream out = response.getOutputStream();
            
            // 브라우저로 파일 내보내기
            // input 스트림으로 받아온 파일을 output 스트림으로 내보낸다
            FileCopyUtils.copy(in, out);
        }
    }
```