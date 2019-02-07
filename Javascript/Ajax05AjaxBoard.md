
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190207-lightgray.svg?style=flat-square)

## Ajax 방식으로 게시판을 만들어 본다

- 기존의 게시판 소스를 분석하고, UI 구상한 후 (insert, insert_result, delete_result, rep, rep_result, modify, modify_result, view, list)
- ajax 방식으로 어떻게 할지?
- 스크립트든 로직이든 자꾸 반복되는 코드가 있다면 가급적 모듈화시키는 것이 좋다

### 어려웠던 점 - 비동기 ajax 로 post 파라미터 값을 넘기며 페이지 요청하기, FormData 와 multipart 활용하기

- 페이지 요청 시 일반적인 get/post 요청에 더해서 ajax 비동기 방식으로 처리하는 부분이 어려웠음
- 예를 들어 index 에서 게시판 페이지를 처음으로 로드할 때는 get 방식으로 전체 페이지를 갱신 했지만,
- 이후 페이징이나 검색 처리 시에는 ajax 방식으로 되도록 (동일한 서블릿 요청임, list.brd) 분기시키는 것이 까다로웠음
- 특히, ajax 자바스크립트에서 post 방식으로 파라미터를 넘기면서 페이지 요청을 날리려면 FormData 객체가 필요한데,
- 이것을 사용하기 위해서는 뷰단에서 form 태그에 enctype='multipart/form-data' 처리를 한 후
- 서블릿 단에서도 이 폼을 받기 위해 MultipartRequest 변환 객체를 사용해야만 했다 (그래야 request 객체 내 파라미터를 사용할 수 있음)

### 코드 리뷰

- 코드는 게시판 테이블 정보 및 페이지를 대상으로 한다

#### index 페이지에서 일반적인 get 방식 요청

index.jsp
```JSP
	...

	<li><a href='list.brd'>게시판</a></li>

	...
```
- 이것에 대한 서블릿 응답
```JAVA
	...

	if (request.getMethod().equals("GET")) {
		url += page + ".jsp";
	} else {
		...
	}

	...

	RequestDispatcher dispatcher = request.getRequestDispatcher(url);
	dispatcher.forward(request, response);

	...
```
- url 및 page 조합의 결과는 index, aside, content 부분을 포괄하는 전체 페이지임
- 단순 get 요청이므로 그러한 전체 페이지만 리턴하여 그것이 화면에 리로드됨


#### board list 페이지가 최초 로드된 이후 페이지 이동 및 검색 요청 처리 (ajax)

list.jsp
```JSP
	...

	<input class='btnCircle' type='button' value='2' onclick='movePage(2)' />

	...
```
- 위로 게시판 리스트 화면이 쭉 있다고 치고, 2 페이지로 이동하기 위한 버튼(.btnCircle)을 클릭하면, movePage() 함수가 실행되며
custom.js
```JAVASCRIPT
	...

	<!-- 검색 -->
	<div id='boardSearchForm'>
		<form name='boardFrm' method='post' enctype='multipart/form-data'>
			<input type='search' name='search' value='${(requestScope.search == null)? "" : requestScope.search }' />
			<input type='button' name='btnSearch' onclick='searchBoard(0)' value='검색' />
			<input type='button' name='btnSearchAll' onclick='searchBoard(1)' value='전체검색' />
			<input type='hidden' name='id' value='' />
			<input type='hidden' name='nowPage' value='${(requestScope.nowPage == null)? 1 : param.nowPage }' />
			<input type='hidden' name='email' value='' />
		</form>
	</div>

	...

	function movePage (nowPage) {
    		var boardFrm = document.boardFrm;
    		boardFrm.search.value = '';
    		boardFrm.nowPage.value = nowPage;
    		boardFrm.action = 'list.brd';
    		// boardFrm.submit();
    
    		var xhr = new XMLHttpRequest();
    		xhr.open('post', 'list.brd');
    		var formData = new FormData(boardFrm);
		xhr.send(formData);
		xhr.onreadystatechange = function () {
			if (xhr.readyState == 4 && xhr.status == 200) {
				var result = xhr.responseText;
				var content = document.getElementById('content');
				content.innerHTML = result;
			}
		}
	}

	...
```

- 스크립트에서는 넘겨받은 nowPage 및 search 요소에 적절한 폼 데이터 처리 후 ajax 비동기 방식으로 지정한 경로 페이지 요청을 날린다
















