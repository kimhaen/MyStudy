
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190108-lightgray.svg?style=flat-square)

# 반응형 웹

> 반응형 웹은 여러 가지 브라우저 환경에서 적절한 뷰를 표현하기 위한 방법론이다.
> 일반 데스크탑, 태블릿, 스마트폰에 따라 화면 구성 및 배치를 동적으로(반응하여) 변경한다.

## @import 와 link 태그 규칙

link 는 정적 느낌, import 는 동적 느낌이 강하다.
무엇을 사용해야 할지 정해진 룰은 없으나, 각각 장단점이 있으므로 상황에 따라 적절히 활용해야 한다.

## @media 규칙

> 이것이 바로 다양한 장치에서 동적으로 화면구성을 변경하도록 하는 규칙이다.

- style 태그 안에 @media screen 이라면, 혹은 @media print 라면 등등으로 각각 적용한다
- 스타일을 css 파일로 분리하기 위해, 페이지에서는 @import ~.css 방식으로 별도 작성된 css 파일을 참조한다
- 중요한 속성 : width, devide-width, orientation
- orientation 속성을 제외하고, min / max 접두사를 붙여 반응의 경계값을 정한다.

### 예제

화면이 screen 이면서 너비 (0px ~ 767px) or (768px ~ 959px) or (960px ~ ) 인 반응형 media 스타일 적용하기

```HTML
<style>
	@media screen and (max-width: 767px) {
		...
	}
	@media screen and (min-width: 768px) and (max-width: 959px) {
		...
	}
	@media screen and (min-width: 960px) {
		...
	}
</style>
```

이 예제를 활용하여 실제 작성해보면,

responsive.css
```HTML
@charset "UTF-8";

body {
	color: #ffffff;
	font-weight: bold;
}
@media screen and (max-width: 799px) {
	body {
		background-color: #ff0000;
	}
}

@media screen and (min-width: 800px) and (max-width: 1199px) {
	body {
		background-color: #00ff00;
	}
}

@media screen and (min-width: 1200px) {
	body {
		background-color: #0000ff;
	}
}
```

play.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>반응형 웹페이지</title>
<style>
	@import url(responsive.css) screen;
</style>
</head>
<body>

	<div id='play'>
		<h1>반응형 웹페이지</h1>
		
		<div id='content'>
			화면의 넓이에 따라 바탕색이 변경됩니다.
			
			<ul>
				<li> ~ 799 : red</li>
				<li>800 ~ 999 : green</li>
				<li>1000 ~ : blue</li>
			</ul>
		</div>
	</div>

</body>
</html>
```

결과 화면은 각 사이즈에 따라 배경색이 스위치된다






