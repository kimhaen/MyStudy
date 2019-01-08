
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190104-lightgray.svg?style=flat-square)


# HTML 이어서

## 기본 태그

- textarea

```HTML
<textarea rows="5" cols="50">hihi~</textarea><br/>
```

- select, optgroup, option

	- 속성 multiple
	- optgroup, option ~
	- 이것은 잘 사용하지 않음. 웹 페이지에 어울리지 않기 때문
	- 일반적으로 select 와 같은 기능은 자바스크립트를 이용해 하나하나 만든다

- fieldset, legend

## 공간 분할 태그

- div, span, p

	- div 는 특정 공간을 차지하고, span 은 인라인 형식으로 쫙 나열된다(사실상 분할의 의미만 있음). p 태그는 문단 단위로 나눈다(상호간 공백 발생)
	- 특히 div 는 레이어 형식으로 각각 CSS 를 씌우며 작업할 때 많이 사용된다

## 의미론적 태그(Sementic Tag)

- 이것 자체로는 페이지 상의 효과를 나타내지는 못하지만,
- 이 태그들로 인해 각각의 컴포넌트가 특정한 의미를 내포하게 된다
- 이것은 브라우저가 코드를 해석할 때 모호하지 않도록 가이드를 제시한다
- header, nav, aside, section, article, footer




# CSS 선택자

> CSS 에서 가장 중요한 것은 선택자이다. 아무리 아름다운 디자인을 적용하고 싶어도 화면의 수많은 컴포넌트 중 디자인 적용 대상을 정확하게 짚어내지 못하면 소용 없기 때문이다.

- CSS 의 여러 속성들도 정확하게 이해하고 있어야 하겠지만,
- 그룹과 id, class 등의 선택자를 적절하게 사용하여 여러 계층의 레이아웃 중 정확한 대상을 골라내야 한다

## 선택자 종류

- 태그 선택자 : body, h1, div 등 일반 태그를 지칭
- id : 특정 태그(하나만!)에 부여하여 그것만 지칭
- class : class 가 적용된 태그들의 그룹을 지정하여 해당 그룹 모두를 지칭
- 속성 선택자
- 후손 선택자 : [부모선택자] [후손선택자], 부모 하위 동종의 모든 후손들을 지칭
- 자손 선택자 : [부모선택자] > [자손선택자], 부모로부터 바로 한 세대 자식 선택자 지칭
- 선택자 + 선택자 : 전위선택자 바로 뒤의 후위선택자 (하나만)
- 선택자 ~ 선택자 : 전위선택자 뒤의 모든 동종 후위선택자
- 반응 선택자 : [선택자]:active, [선택자]:hover >> active 는 선택 시, hover 는 마우스를 올리기만 해도 반응함

이밖에 수많은 선택자들이 있으므로 필요에 의해 잘 찾아서 사용해볼 것!
(적용 가능한 속성 중에 호환되는 웹 브라우저에 따른 transition-duration 적용하는 것도 있다 > 변화가 진행되는 속도 조절)

## CSS 선택자 우선순위

<strong><i>태그에 직접적용 > id > class > 태그 선택자</i></strong> 순이다

## CSS 적용하기

- 외부 파일에 CSS 를 작성한 뒤 @import 또는 link 태그를 사용하여 적용한다 (가장 많이 사용하는 방식)
- 페이지의 head 태그 내 style 태그에 작성한다
- 특정 태그 자체에 style 속성을 직접 지정할 수도 있지만 이것은 그닥..

## 예시

### 페이지 내에 style 을 직접 적용하기

```HTML
<style type="text/css">
	h1 {
		color: #34ff82;
	}
	h1.jemok {
		color: #827272;
	}
	h1#subject {
		color: #872532;
	}
	div#title {
		background-color: #E0E0F8;
		border: 2px solid #aaaaff;
		border-radius: 10px;
		font-weight: bold;
		color: #63717f;
		box-sizing: border-box;
		padding-left: 20px;
		padding-top: 10px;
		margin: 10px auto;
		box-shadow: 10px 10px 10px #aaaaff;
	}
<style>
```

결과는 다음과 같다

![css result](https://github.com/daesungRa/MyStudy/blob/master/imgs/cssEx01.PNG)<br/>



### css 파일을 링크하는 형식으로 style 적용하기

> 폴더에 css file 을 만들어 style 코드를 작성한 후 그것을 링크 형식으로 본문에 참조하면 된다

index.css
```HTML
@charset "UTF-8";
@import url(https://fonts.googleapis.com/css?family=Staatliches);
* {
	font-family: Staatliches;
}
#header {
	width: 1000px;
	height: 120px;
	padding: 10px 0 10px 10px;
	box-sizing: border-box;
	font-size: 24pt;
	margin: 0 auto;
	background-color: #343d46;
	color: #c0c0c0;
}
#wrap {
	width: 1000px;
	height: 300px;
	padding: 0;
	box-sizing: border-box;
	margin: 10px auto;
	background-color: #565e46;
	color: #343434;
	overflow: hidden;
}
#wrap #aside {
	width: 220px;
	float: left;
	background-color: #9a9e78;
}
#wrap #content {
	width: 780px;
	float: left;
	background-color: #787856;
}
#footer {
	width: 1000px;
	height: 120px;
	padding: 10px 0 10px 20px;
	box-sizing: border-box;
	margin: 0 auto;
	background-image: url('../images/camel.png');
	background-size: contain;
	color: #efefef;
	font-weight: bold;
}
```

index.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>메인 페이지</title>
<link rel='stylesheet' type='text/css' href='./styleTest/index.css' />
</head>
<body>

	<header id='header'>
		<h1>My Home Page</h1>
	</header>
	<section id='wrap'>
		<aside id='aside'>
			<h1>Aside</h1>
		</aside>
		<article id='content'>
			<h1>Content</h1>
		</article>
	</section>
	<footer id='footer'>
		<h1>Copyleft: daesungRa</h1>
	</footer>

</body>
</html>
```

위와 같이 index page 에서 index.css 를 참조하여 css 를 적용하는 것을 볼 수 있다

결과
![index.jsp](https://github.com/daesungRa/MyStudy/blob/master/imgs/index.PNG)


### member 테이블에 대한 회원 목록 조회하기

회원 목록을 표현하는 list.jsp 화면 만들기가 목적임

member.css
```HTML
@charset "UTF-8";
@import url(https://fonts.googleapis.com/css?family=Staatliches);

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

/*
	modify.jsp
*/
div#modify input[type=button] {
	width: 80px;
	height: 35px;
}

/*
	view.jsp
*/
div#view input[type=button] {
	width: 80px;
	height: 35px;
	margin-top: 10px;
	margin-left: 86px;
}

/*
	list.jsp
*/
div#list {
	width: 680px;
	margin: 100px auto;
}
div#list #title {
	font-size: 18pt;
}
div#list #title #damuni {
	width: 80px;
	float: left;
	margin-right: 10px;
}
div#list form {
	text-align: right;
}
div#list > #subject {
	border-top-style: solid;
	border-top-width: 1px;
	border-bottom-style: solid;
	border-bottom-width: 1px;
	margin-top: 5px;
	padding: 10px 0 10px 0;
	font-size: 16pt;
}
div#list span {
	display: inline-block;
}
div#list .no {
	width: 50px;
}
div#list .id {
	width: 100px;
}
div#list .name {
	width: 100px;
}
div#list .phone {
	width: 200px;
}
div#list .email {
	width: 200px;
}
div#list > #memberList {
	padding: 5px 0 5px 0;
	border-bottom-style: solid;
	border-bottom-width: 1px;
}
div#list #btnZone{
	margin: 5px auto;
	text-align: center;
}
div#list #btnZone input[type=button] {
	margin-right: 5px;
}
div#list #btnZone .btnCircle {
	border-radius: 20px;
}
```

이 css 파일은 회원가입을 위한 insert.jsp, 회원정보 수정을 위한 modify.jsp 의 스타일시트까지 한 번에 포함한다

일단은 회원목록 조회를 위한 list.jsp 를 만들어 본다

list.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>회원 목록 조회</title>
<link rel='stylesheet' href='../styleTest/member.css' />
</head>
<body>

	<!-- 상단에 검색 바 -->
	<!-- 본문에 No, 아이디, 성명, 연락처, 이메일 찍어주기 -->
	<!-- 하단에 페이징 처리 (맨처음, 이전, ..., 다음, 맨끝 -->
	<div id='list'>
		<div id='title'>
			<img src='../images/damuni.jpg' id='damuni'/>
			<h1>회원 목록 조회</h1>
		</div>
		<form name='member' method='post'> <!-- action 속성을 생략하면 자기 자신을 호출한다 -->
			<input type='search' name='search' />
			<input type='button' name='btnSearch' value='검색' />
		</form>
		
		<div id='subject'>
			<span class='no'>No</span>
			<span class='id'>ID</span>
			<span class='name'>NAME</span>
			<span class='phone'>PHONE</span>
			<span class='email'>EMAIL</span>
		</div>
		<div id='memberList'>
			<%
				for (int i = 0; i < 10; i++) {
			%>
				<div class='row'>
					<span class='no'><%= i + 1 %></span>
					<span class='id'>hong</span>
					<span class='name'>gildong</span>
					<span class='phone'>010-1111-2222</span>
					<span class='email'>hong@gmail.com</span>
				</div>
			<%
				}
			%>
		</div>
		
		<div id='btnZone'>
			<input type='button' value='HEAD' />
			<input type='button' value='PRE' />
			<input class='btnCircle' type='button' value='1' />
			<input class='btnCircle' type='button' value='2' />
			<input class='btnCircle' type='button' value='3' />
			<input class='btnCircle' type='button' value='4' />
			<input class='btnCircle' type='button' value='5' />
			<input type='button' value='NEXT' />
			<input type='button' value='TAIL' />
		</div>
	</div>

</body>
</html>
```

일단 스크립트릿으로 테스트 데이터를 반복출력함

결과 화면은 다음과 같다
![list.jsp](https://github.com/daesungRa/MyStudy/blob/master/imgs/memberList.PNG)












