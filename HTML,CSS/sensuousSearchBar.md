
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190103-lightgray.svg?style=flat-square)

출처: [검색 폼의 Input과 버튼에 관한 CSS 코드 실험]

# 감각적인 검색 폼 만들기

> input 태그의 search 타입을 검색하다가 search bar 에 대한 매우 매우 유용한 튜토리얼을 만났다!

- 부품은 Font-awesome 의 돋보기 모양 아이콘과 input 태그의 search 타입 검색 바 두 개이다
- 이 튜토리얼에서는 여러 방식의 검색 바를 소개했지만, 나는 두 개정도만 따라해봤다

## searchForm01.html

```HTML
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<link rel='stylesheet' href='//maxcdn.bootstrapcdn.com/font-awesome/4.1.0/css/font-awesome.min.css'/>
<style>
	body {
		background-color: #343d46;
	}
	.box {
		margin: 100px auto;
		width: 300px;
		height: 50px;
	}
	.container-1 {
		width: 300px;
		vertical-align: middle;
		white-space: nowrap;
		position: relative;
	}
	.container-1 input#search {
		width: 300px;
		height: 50px;
		background: #2b303b;
		border: none;
		font-size: 10pt;
		float: left;
		color: #63717f;
		padding-left: 45px;
		-webkit-border-radius: 5px;
		-moz-border-radius: 5px;
		border-radius: 5px;
		
		-webkit-transition: background .55s ease;
		-moz-transition: background .55s ease;
		-ms-transition: background .55s ease;
		-o-transition: background .55s ease;
		transition: background .55s ease;
	}
	.container-1 input#search::-webkit-input-placeholder {
		color: #65737e;
	}
	.container-1 input#search::-moz-placeholder {
		color: #65737e;
	}
	.container-1 input#search:-moz-placeholder {
		color: #65737e;
	}
	.container-1 input#search:-ms-input-placeholder {
		color: #65737e;
	}
	.container-1 .icon {
		position: absolute;
		top: 50%;
		margin-left: 17px;
		margin-top: 13px;
		z-index: 1;
		color: #4f5b66;
	}
	.container-1 input#search:hover, .container-1 input#search:focus, .container-1 input#search:active {
		outline: none;
		background: #ffffff;
	}
</style>
</head>
<body>

	<div class='box'>
		<div class='container-1'>
			<span class='icon'><i class='fa fa-search'></i></span>
			<input type='search' id='search' placeholder='Search...'/>
		</div>
	</div>

</body>
</html>
```

이 코드의 결과는 다음과 같다

- searchForm01

![searchForm01](https://github.com/daesungRa/MyStudy/tree/master/imgs/searchForm01.PNG)

- searchForm01_hover

![searchForm01](https://github.com/daesungRa/MyStudy/blob/master/imgs/searchForm01_hover.PNG)

## searchForm01.html

```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<link rel='stylesheet' href='//maxcdn.bootstrapcdn.com/font-awesome/4.1.0/css/font-awesome.min.css'/>
<style>
	body {
		background-color: #343d46;
	}
	.box {
		margin: 100px auto;
		width: 300px;
		height: 50px;
	}
	.container-2 {
		width: 300px;
		vertical-align: middle;
		white-space: nowrap;
		position: relative;
	}
	.container-2 input#search {
		width: 50px;
		height: 50px;
		background: #2b303b;
		border: none;
		font-size: 10pt;
		float: left;
		color: #262626;
		padding-left: 36px;
		-webkit-border-radius: 5px;
		-moz-border-radius: 5px;
		border-radius: 5px;
		
		-webkit-transition: width .55s ease;
		-moz-transition: width .55s ease;
		-ms-transition: width .55s ease;
		-o-transition: width .55s ease;
		transition: width .55s ease;
	}
	.container-2 input#search::-webkit-input-placeholder {
		color: #65737e;
	}
	.container-2 input#search::-moz-placeholder {
		color: #65737e;
	}
	.container-2 input#search:-moz-placeholder {
		color: #65737e;
	}
	.container-2 input#search:-ms-input-placeholder {
		color: #65737e;
	}
	.container-2 .icon {
		position: absolute;
		top: 50%;
		margin-left: 17px;
		margin-top: 13px;
		z-index: 1;
		color: #4f5b66;
	}
	.container-2 input#search:focus, .container-2 input#search:active {
		outline: none;
		width: 300px;
	}
	.container-2:hover input#search {
		width: 300px;
	}
	.container-2:hover .icon {
		color: #93a2ad;
	}
</style>
</head>
<body>

	<div class='box'>
		<div class='container-2'>
			<span class='icon'><i class='fa fa-search'></i></span>
			<input type='search' id='search' placeholder='Search...'/>
		</div>
	</div>

</body>
</html>
```

이 코드의 결과는 다음과 같다

- searchForm02

![searchForm01](https://github.com/daesungRa/MyStudy/tree/master/imgs/searchForm02.PNG)

- searchForm02_hover

![searchForm01](https://github.com/daesungRa/MyStudy/tree/master/imgs/searchForm02_hover.PNG)



[검색 폼의 Input과 버튼에 관한 CSS 코드 실험]: https://webdesign.tutsplus.com/ko/tutorials/css-experiments-with-a-search-form-input-and-button--cms-22069




