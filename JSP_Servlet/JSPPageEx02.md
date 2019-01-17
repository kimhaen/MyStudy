
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190117-lightgray.svg?style=flat-square)

## 등록된 멤버 조회 및 수정 페이지 추가

### 조회 및 멤버정보 수정 뷰 페이지 만들기 - /views/member/list.jsp, /views/member/modify.jsp

- 최상단에 타이틀, 우상단에 검색, 센터에 번호, 아이디, 이름, 전화번호, 이메일, 학년 등의 정보를 나타내는 표, 하단에 페이징 처리
- 아이디에는 링크를 달아서 클릭 시 선택된 멤버정보가 modify.jsp 페이지로 출력되도록 함
	* 검색 폼에 hidden 태그를 생성해 페이지 이동 시 필요한 정보들을 보존한다
	* 아이디 클릭 시 아이디에 해당하는 정보와 기타 hidden 정보들을 post 방식으로 수정 페이지에 전달한다
	* 필요한 정보는, 선택한 멤버의 아이디, 데이터 수정 후 list.jsp 페이지로 돌아왔을 때 마지막 페이지와 관련된 정보들이다(검색어, 페이지번호 등)
- link.jsp 로드 후 구성
	* link.jsp 페이지를 만들어 index 통합 페이지에 로드되도록 한 후 모든 경로를 이에 맞춰서 만든다
	* UI 작업 이후 조건에 맞는 멤버 검색 로직을 만든다
		- 검색 버튼 클릭 시 입력된 키워드에 맞는 모든 결과를 DB 커넥션으로 검색해 스크립트릿 반복문으로 출력
		- 전체검색 버튼 클릭 시 모든 멤버가 출력되도록 조치
- 아이디 클릭 시 modify.jsp 로 넘어가는 로직 구현
	* 검색 폼 태그 안에 hidden 태그 추가 name 속성은 'id' 로
	* 아이디를 클릭하면 자바스크립트의 modify(this) 함수 호출
	* function modify(obj)
		- this 로 넘겨받은 obj 를 사용하여 링크 태그의 text 값(id) 추출
		- 추출한 id 값을 hidden 의 value 속성에 대입
		- var url = 'index.jsp?aside=./control.jsp&content=./views/member/modify.jsp';

### 파일 업로드 API - 추후 해볼 것

- servlets.com
- apache ~ 
- servlet 3.0

### JSP 에서 JS 실행하는 방법

1. JS 에게 매개변수를 전달하는 방법

JSP 코드 중간에 스크립트를 열고 자바스크립트 함수에 표현식으로 매개변수를 전달
```JAVASCRIPT
...
	<script>
		sum(<%=a %>, <%=b %>);
	</script>
...
```

2. 스크립트를 생성해서 실행하는 방법

JSP 코드가 진행되다가 순간적으로 JS 알림창이 나타났다가 사라짐
특정 값을 전달할 때 사용
```HTML
...
	<%
		out.print("<script>alert('a')</script>");
	%>
...
```

- 반대로 JS 에서 JSP 실행은 불가하다

### 자바스크립트로 중복체크 로직 - 강사님 ver

- 중복체크 실행 여부 판별
	* mainInsert() 메서드에 var chk = false; 변수 추가
	* 중복체크 안했는데(chk == false) submit 버튼이 클릭되었다면 메시지 띄우고 전송되지 않도록 처리 (btnSubmit.onclick ~~)
- 중복체크 로직
	* btnChk 가 클릭되면(onclick) 중복체크 창이 열리면서 메인 화면의 아이디가 새로 열린 창에 입력됨
```JAVASCRIPT
var win = window('./views/member/idCheck.jsp', 'win', 'width=350px, height=150px');
win.onload = function () {
	win.document.frm.id.value = frm.id.value;
}
```
	* 중복체크 창에서 아이디가 입력되는 창(frm.id)은 form 태그로 만든다
	* 중복체크 버튼을 누르면 자기 자신을 호출함
	* 이때 POST 방식으로 호출되면 하단 스크립트릿이 실행되며 DB 로 중복체크 한다 (if (request.getMethod("POST") { 커넥션 로직 }))
	* 커넥션 로직 내에서 중복체크의 결과에 따라 script 코드 삽입으로 자바스크립트의 chk(boolean b, String id) 함수를 호출함 >> chk 변수를 false 혹은 true 로 세팅
	* script 부분이 중간에 실행되므로 화면이 잠깐 깜빡일 수 있음
```JAVA
...
	if (rs.next()) {
		script = String.format("<script>chk(false, %s)</script>, id);
	} else {
		script = String.format("<script>chk(true, %s)</script>, id);
	}
	out.print("<script>chk(true, ");
...
```

- 중복체크 결과 적용
	* chk(boolean b, String id) 함수가 실행되면, opener 의 chk 변수에 b 가 적용되고,
	* 스크립트릿 부분 실행을 위해 창의 화면이 재호출(POST) 되면서 id 텍스트 박스가 초기화되므로 chk 함수에 넘어온 id 값을 텍스트 박스에 입력한다
	* 중복체크 화면에 boolean b 에 따른 확인 메시지를 출력하고, opener 의 id 에 중복확인된 아이디를 입력한다
























