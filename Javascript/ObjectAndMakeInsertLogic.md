
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190111-lightgray.svg?style=flat-square)

### 자바스크립트 명명 규칙

- CSS 의 특정 속성을 자바스크립트에서 명시할 때는 다음과 같이 한다
- CSS : background-color >> JS : backgroundColor
- 여기서 '-c' 는 'C' 로 바뀐다
- HTML 내에서 대소문자를 구분하지 않지만, 자바스크립트 영역(script 태그 내)에서는 구분하므로 주의할 것

# 자바스크립트 객체

> 자바스크립트는 객체 지향 언어 중 하나이다. 일반적인 웹 브라우저에서 페이지 구성에 따라 document 객체, location 객체, history 객체, window 객체 등이 있다

## 객체의 종류

* 내장 객체 : Arguments, Array, Date, Math, Function, Number, String, Screen, Object, JSON
* 브라우저 객체 : window, document, history, location, link, navigator
* 폼 객체
	- form 태그와 관련된 객체들이다
	- form, input, text, textarea, password, hidden, button, submit, checkbox, radio, select, fileupload

### Form Object

- 일단 폼 객체는 HTML 코드 상에서 특정 form 태그 전체를 지칭한다
- 사용자가 입력한 데이터에 따라 어떤 처리들을 수행한다면 거의 다 form 태그를 사용한다고 보면 된다
- 어떤 태그에 대해서 this.form 를 자바스크립트 매개변수로 전달한다면, 그것은 자기 자신을 포함하는 form 태그 전체를 매개변수로 전달하는 것과 같다
- 특정 태그에 name 속성을 지정하면, 자바스크립트에서 name 속성의 값만으로 해당 태그 컨트롤이 가능하다


### 실습 - 폼 객체를 활용한 자바스크립트 회원가입 로직 작성

1. 회원가입 뷰 - insert.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta http-equiv='Cache-Control' content='no-cache' />
<meta http-equiv='Pragma' content='no-cache' />
<meta http-equiv='Expires' content='1' />
<title>회원가입</title>
<link rel='stylesheet' href='../styleTest/member.css' />
<style>
div#insert #image {
	position: absolute;
	top: 80px;
	left: 370px;
}
</style>
<script src='../js/member.js'></script>
</head>
<body>

	<div id='insert'>
		<h1 id='title'>회원가입</h1>
		<form name='member' method='post' action='insert_result.jsp'>
			<label>아이디</label>
			<input type='text' name='id' value='hong' /><br/>
			<label>이 름</label>
			<input type='text' name='irum' value='gildong' /><br/>
			<label>비밀번호</label>
			<input type="password" name='pwd' value='1111' /><br/>
			<label>이메일</label>
			<input type='email' name='email' value='hong@gmail.com' /><br/>
			<label>연락처</label>
			<input type='text' name='phone' value='010-1111-2222' /><br/>
			<label>등록일</label>
			<input type="date" name='rdate' /><br/>
			<label>우편번호</label>
			<input type='text' name='postal' value='12-123' /><br/>
			<label>주 소</label>
			<input type='text' name='address' value='서울' size='50' /><br/>
			<label>증명사진</label>
			<input type='file' name='photo' /><br/>
			<label>학 년</label>
			<select size='4' name='grade'>
				<option value='1' selected>1 학년</option>
				<option value='2'>2 학년</option>
				<option value='3'>3 학년</option>
				<option value='4'>4 학년</option>
			</select>
			
			<br/>
			
			<input type='submit' value='등 록' />
			<img id='image' name='image' src='https://via.placeholder.com/150x200' width='150px' height='200px' />
		</form>
	</div>
	
	<script>
		mainInsert();
	</script>

</body>
</html>
```

- head 부분 : 캐시 미사용, 만료시간 1초, 외부 CSS 와 Javascript 링크
- body 부분 : 회원가입 정보 입력 폼, 이미지 미리보기
- 하단 스크립트 : 링크된 외부 JS 파일에 기술된 mainInsert() 함수를 호출한다. 이 함수는 현재 일자, 이미지 미리보기, submit 시 데이터 체크 기능을 수행한다

2. 동적 스크립트 - member.js
```JAVASCRIPT
/**
 * refer by insert.jsp
 */
function mainInsert() {
    var frm = document.member;
    var d = new Date();

    // 가입일 현재 일자로 초기화
    /* var temp = d.getFullYear() + 1900 + ' - ' + d.getMonth() + 1 + ' - ' + d.getDate();
    frm.rdate.value = temp; */
    frm.rdate.valueAsDate = d;

    // 이미지를 선택하거나 수정한 경우
    frm.photo.onchange = imagePreView;

    // 전송 버튼이 클릭된 경우
    frm.onsubmit = function (){
        var obj = inputDataCheck(frm);
        // obj 가 null 이면, submit 이 그대로 실행됨
        if (obj != null) {
            obj.focus();
            alert('데이터를 확인해 주세요...');
            return false;
        }
    }
}

function imagePreView (e) {
    var frm = document.member;
    var url = e.srcElement;
    var file = url.files[0];
    var reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = function (e2) {
        var img = new Image();
        img.src = e2.target.result;
        frm.image.src = img.src;
    }
}

function inputDataCheck (frm) {
    var obj = null;
    var emailExp01 = /^[_A-Za-z0-9-\\+]+(\\.[_A-Za-z0-9-]+)*@[A-Za-z0-9-]+(\\.[A-Za-z0-9]+)*(\\.[A-Za-z]{2,})$/;
    var emailExp02 = /^\w+@\w+.\w(.\w){1,2}/;
    var phoneExp01 = /^01([0|1|6|7|8|9]?)-?([0-9]{3,4})-?([0-9]{4})$/;
    var phoneExp02 = /^\d{2,3}-\d{3,4}-\d{4}$/;

    if (frm.id.value == '') obj = frm.id;
    else if (frm.irum.value == '') obj = frm.irum;
    else if (frm.pwd.value == '') obj = frm.pwd;
    else if (frm.rdate.value == '') obj = frm.rdate;
    else if (!emailExp02.test(frm.email.value)) obj = frm.email;
    else if (!phoneExp02.test(frm.phone.value)) obj = frm.phone;

    return obj; // ok == null, data err == data
}
```

- mainInsert() : 현재 일자, 이미지 미리보기, submit 시 데이터 체크 기능 수행
- imagePreView(e) : 폼 태그 내부 이름이 photo 인 input[type=file] 태그에 변경 이벤트(onchange) 가 발생하면 실행되는 메서드. 이벤트 e 매개변수는 자동으로 투입된다. 이것의 실행 결과로, img 태그의 src 에 지정 경로의 파일이 임시적으로 로드된다.
- inputDataCheck(frm) : 입력된 회원정보의 공백 체크, 이메일 및 전화번호 형식 체크(정규식)를 수행하고 문제 없으면 null, 잘못된 부분이 있으면 그 부분에 해당하는 태그 객체를 반환한다.

3. 페이지 디자인 구성 - member.css
```CSS
/*
	공통
*/
* {
	font-family: Staatliches;
}
div#insert label, div#modify label, div#view label {
	display: inline-block;
	width: 75px;
	text-align: right;
	margin-right: 5px;
}
div#insert input[type=submit], div#modify input[type=submit] {
	width: 80px;
	height: 35px;
	margin-top: 10px;
	margin-left: 86px;
}
div#insert select, div#modify select {
	width: 80px;
}

/*
	insert.jsp
*/
div#insert {
}
div#insert #image {
	position: absolute;
	top: 80px;
	left: 370px;
}

...

```

#### 회원정보 수정 페이지도 거의 동일하므로 작성해볼 것













