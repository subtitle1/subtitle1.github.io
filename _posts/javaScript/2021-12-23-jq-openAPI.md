---
layout: single
title: "오픈 API로 일자별 영화 정보 조회하기"
excerpt: 'jquery와 오픈 api 활용 '
categories: javaScript
tag: javaScript, library, jQuery, ajax, openAPI, JSP
---
> **영화진흥위원회에서 제공하는 오픈API로 간단한 상영 정보 조회하기!**

사진과 같이 날짜를 선택하고 조회버튼을 클릭했을 때 10개의 영화 정보를 띄우고, <br> 상세 정보 클릭 시에 영화에 대한 정보를 띄우는 모달창을 만들었다. (부트스트랩 사용)

일별 박스오피스, 상세정보 오픈API: <br>
**[링크](https://www.kobis.or.kr/kobisopenapi/homepg/apiservice/searchServiceInfo.do)**에서 맨 하단으로 가면 url을 제공받을 수 있다. <br>

일별 박스오피스 조회 시에 필수 요청변수로 **key**와 **targetDt**, <br>
상세정보 조회 시에는 **key**와 **movieCd**가 있으니 참고하자.
![image](https://user-images.githubusercontent.com/87356533/147237250-e34f503c-6d7d-44d0-9512-63c3fb196742.png)
![image](https://user-images.githubusercontent.com/87356533/147237297-7b86db97-b744-4a7b-b224-9244d698c782.png)

오늘의 코드는 조금 많이 길다..!

> **jsp 코드**

```jsp
<div class="row mb-3">
    <div class="col">
        <div class="mb-3 d-flex justify-content-end">
            <form class="row row-cols-lg-auto g-3 align-items-center">
                <div class="col-12">
                    <label class="form-label">조회날짜</label>
                </div>
                <div class="col-12">
                    <input type="date" name="date" class="form-control"/>
                </div>
                <div class="col-12">
                    <button type="button" class="btn btn-outline-dark" id="btn-get-boxoffice">조회</button>
                </div>
            </form>
        </div>
        <table class="table" id="table-boxoffice">
            <thead>
                <tr>
                    <th style="width: 8%;" class="text-center">순위</th>
                    <th style="width: 32%;">제목</th>
                    <th style="width: 10%;">개봉일</th>
                    <th style="width: 10%;" class="text-end pe-3">관객수</th>
                    <th style="width: 15%;" class="text-end pe-3">누적관객수</th>
                    <th style="width: 10%;" class="text-end pe-3">점유율</th>
                    <th style="width: 10%;" class="text-end pe-3"></th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>
    </div>
</div>
<!-- 영화상세정보를 표현하는 모달 -->
<div class="modal" tabindex="-1" id="modal-boxoffice">
    <div class="modal-dialog modal-xl">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">영화 상세정보</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
                <div class="card">
                    <div class="card-header"><strong id="movie-title"></strong></div>
                    <table class="table">
                        <tr>
                            <th style="width: 10%;">개봉일자</th>
                            <td style="width: 35%;"><span id="movie-open-data"></span></td>
                            <th style="width: 10%;">상영시간</th>
                            <td style="width: 55%;"><span id="movie-show-time"></span> 분</td>
                        </tr>
                        <tr>
                            <th>영화명(영문)</th>
                            <td colspan="3"><span id="movie-name-en"></span></td>
                        </tr>
                        <tr>
                            <th>쟝르</th>
                            <td><span id="movie-genre"></span></td>
                            <th>관람등급</th>
                            <td><span id="movie-watch-grade"></span></td>
                        </tr>
                        <tr>
                            <th>감독</th>
                            <td><span id="movie-directors"></span></td>
                            <th>배우</th>
                            <td><span id="movie-actors"></span></td>
                        </tr>
                        <tr>
                            <th><span>제작/배급사</span></th>
                            <td colspan="3"><span id="movie-companys"></span></td>
                        </tr>
                    </table>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-dark" data-bs-dismiss="modal">확인</button>
            </div>
        </div>
    </div>
</div>
```
---
> **script 코드 1**

- 스크립트 코드는 1, 2로 나누어서 살펴보도록 하자.
    1. 조회버튼을 클릭 시 실행되는 이벤트핸들러 메소드를 등록하기
        - **$("#btn-get-boxoffice")**이 '조회' 버튼에 해당되는 제이쿼리 집합객체이다
    2. **var $tbody = $('#table-boxoffice tbody').empty();**
        - 영화 정보들이 출력될 곳을 미리 비워둔다. 이 과정 없이 append()를 하면 그 전에 조회한 목록들 다음에 영화 목록이 출력된다.
    3. **var date = $("input[name=date]").val().replace(/-/g, '');**
        - name이 date인 input에서 값을 조회하고, replace로 날짜에 붙은 -를 없앤 후 변수에 저장한다.
    4. $.ajax() 함수에 대한 내용은 **[해당 게시글 하단](https://subtitle1.github.io/javascript/jq-ajax/)**에 있다.
    5. **success: function(result)**
        - result의 값은 하단 이미지처럼 json 형식이다. <br> 
        - 데이터 출력에 필요한 값은 result 객체 안에 있는 boxOfficeResult 하위에 존재하는 dailyBoxOfficeList다. <br> 
        - **var boxOfficeList = result.boxOfficeResult.dailyBoxOfficeList;**

    ![image](https://user-images.githubusercontent.com/87356533/147243511-b248a62b-49fb-48ad-80e2-efe92618ef79.png)

```jsp
<script type="text/javascript">
    // 조회버튼 클릭할 때 실행되는 이벤트핸들러 메소드 등록
    $("#btn-get-boxoffice").click(function() {
        var $tbody = $('#table-boxoffice tbody').empty();
        var date = $("input[name=date]").val().replace(/-/g, '');

        if (date == '') {
            alert("조회날짜를 입력하세요");
            return;
        }
        
        $.ajax({
            type: 'get',
            url: 'http://kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json',
            data: {key: "f5eef3421c602c6cb7ea224104795888", targetDt: date},
            dataType: 'json',
            success: function(result) {
                var boxOfficeList = result.boxOfficeResult.dailyBoxOfficeList;
                $.each(boxOfficeList, function(index, boxOffice) {
                    var row = "<tr>";
                    row += '<td class="text-center">'+boxOffice.rank+'</td>';
                    row += '<td>'+boxOffice.movieNm+'</td>';
                    row += '<td>'+boxOffice.openDt+'</td>';
                    row += '<td class="text-end pe-3">'+parseInt(boxOffice.audiCnt).toLocaleString()+' 명</td>';
                    row += '<td class="text-end pe-3">'+parseInt(boxOffice.audiAcc).toLocaleString()+' 명</td>';
                    row += '<td class="text-end pe-3">'+boxOffice.salesShare+'%</td>';
                    row += '<td class="text-end pe-3"><button class="btn btn-outline-dark btn-sm" data-movie-code="'+boxOffice.movieCd+'">상세정보</button></td>';
                    row += '</tr>';
                    
                    $tbody.append(row);
                })
            },
            error: function() {
                alert("오류가 발생하였습니다.");
            }
        });
    });
</script>
```

> **script 코드 2**

1. 상세정보 버튼을 클릭했을 때 실행될 이벤트 핸들러 함수를 등록한다.
    - **$("#table-boxoffice tbody").on('click', '.btn', function()**
    - 아이디가 table-boxoffice tbody인 곳에 클래스가 btn인 버튼(상세정보)을 클릭 시 실행될 메소드
2. **var movieCode = $(this).attr("data-movie-code");**
    - 선택한 버튼에서 data-movie-code 속성값을 movieCode 변수에 저장한다. <br>
    이 값이 있어야 상세정보 조회가 가능하다.
3. 스크립트 코드 1과 같은 방식으로 화면에 출력한다.

```jsp
<script>
    // 모달객체 생성
    var boxOfficeModal = new bootstrap.Modal(document.getElementById('modal-boxoffice'), {
        keyboard: false
    });

    $("#table-boxoffice tbody").on('click', '.btn', function() {
        var movieCode = $(this).attr("data-movie-code");
        
        // 요청을 보냄
        $.getJSON("https://www.kobis.or.kr/kobisopenapi/webservice/rest/movie/searchMovieInfo.json", 
            {key: "f5eef3421c602c6cb7ea224104795888", movieCd: movieCode}, 
            
            // 응답이 올 때 result 객체에 값이 전달돼서 옴
            function(result) {
                var movie = result.movieInfoResult.movieInfo;
                $("#movie-title").text(movie.movieNm);
                $("#movie-name-en").text(movie.movieNmEn);
                $("#movie-open-data").text(movie.openDt);
                $("#movie-show-time").text(movie.showTm);
                $("#movie-genre").text(movie.genres.map(item => item.genreNm).join(', '));
                $("#movie-watch-grade").text(movie.audits.map(item => item.watchGradeNm).join(', '));
                $("#movie-directors").text(movie.directors.map(item => item.peopleNm).join(', '));
                $("#movie-actors").text(movie.actors.map(item => item.peopleNm).filter((item, index) => index < 5).join(', '));
                $("#movie-companys").text(movie.companys.map(item => item.companyNm + "("+item.companyPartNm+")").join(', '))
                
                // 모달창을 표시한다.
                boxOfficeModal.show();		
        });
    });
</script>
```
