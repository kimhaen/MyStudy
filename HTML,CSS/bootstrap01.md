
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190128-lightgray.svg?style=flat-square)

# 부트스트랩 정리

방식 : [w3schools 부트스트랩 튜토리얼](https://www.w3schools.com/bootstrap4/)을 번역

### 왜 부트스트랩을 사용하는가?

- 편리한 사용방식
- 반응형 : phones, tablets, desktops
- Mobile-first approach : 부트스트랩에서 모바일 스타일은 핵심 프레임워크 중 하나이다
- 브라우저 호환성 : 크롬, 파폭, IE 10+, 엣지, 사파리, 오페라 등

### 부트스트랩 적용

- 설치 방식 : getbootstrap.com 에서 받아서 웹 프로젝트에 추가
- cdn 으로부터 부트스트랩 4 를 참조하는 방식 (웹 페이지 소스에 포함시킨다)
- cdn 예시

```HTML
<!doctype html>
<html>
	<head>
		<meta charset="utf-8">
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<title>부트스트랩 4 예제</title>
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css">
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
		<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.6/umd/popper.min.js"></script>
		<script src="https://maxcdn.bootstrapcdn.com/bbotstrap/4.2.1/js/bootstrap.min.js"></script>
	</head>
	<body>
		<div class="container-fluid">
			<h1>첫 부트스트랩 페이지</h1>
			<p>hi, 반가워요~</p>
		</div>
	</body>
</html>
```

- 이와 같이 필요한 css 및 스크립트 정보를 헤드 태그 내에 include 하면, 페이지 컴파일 시 자동으로 로딩 된다
- meta name="viewport" content="width=device-width, initial-scale=1" 부분은 모바일 화면을 고려한 반응형 화면구성 태그이다

### Layout

- Fixed Layout
	* 고정된 너비를 사용하는 레이아웃. 브라우저 화면 크기가 변화해도 고정됨
	* 기본 화면 너비는 960px 로 고정이고, 웹 브라우저의 너비가 960px 이하가 되면 반응형으로 변경됨
	* div class="container" 로 적용
- Fluid Layout
	* 화면 크기에 따라 레이아웃 크기가 변화
	* div class="container-fluid" 로 적용

### navbar (navigation bar)

- 네비게이션 바는 웹 페이지 상단 (혹은 bottom) 에 위치하는 구성 요소이다 (a navigation header)
- navbar도 일반형과 고정형 2 가지 형태가 있다. 일반형은 화면 스크롤을 내리면 보이지 않게 되고, 고정형은 고정적으로 보인다
- 일반형은 .navbar 클래스로 지정한다 (div class="navbar")
- 고정형은 .navbar 클래스명에 fixed-top 을 추가한다 (div class="navbar fixed-top")
- navbar 는 position: fixed; 방식으로 페이지에 적용되므로 화면 상단의 일부분을 가린다
- 그러므로 body 태그에 padding-top 혹은 margin-top 속성을 적용해 겹쳐지지 않도록 조치한다

```HTML
...
<style>
	body {
		padding-top: 60px;
	}
</style>
</head>
<body>
	<div class="navbar fixed-top">
		<div class='navbar-header'>
			<a class='navbar-brand' href="#">Bootstrap</a>
		</div>
	</div>

	<div class='container'>
		<h1>Responsive Columns</h1>
		<p>Resize the browser window to see the effect.</p>
		<p>The columns will automatically stack on top of each other when the screen is less than 576px wide.</p>
		
		<div class="col-sm-3" style="background-color:lavender;">.col-sm-3</div>
		<div class="col-sm-3" style="background-color:lavenderblush;">.col-sm-3</div>
		<div class="col-sm-3" style="background-color:lavender;">.col-sm-3</div>
		<div class="col-sm-3" style="background-color:lavenderblush;">.col-sm-3</div>
	</div>
</body>
```

- 반대로 .fixed-bottom 클래스를 추가하면 바닥에 고정형으로 붙는다
- .navbar 에 .sticky-top 클래스를 추가하면 해당 네비게이션 바가 특수한 위치에 고정된다. 스크롤을 내리면 그대로 상단에 붙어서 고정된다
- 예를 들어, .navbar sticky-top 위에 .container 가 있고, 아래에 본문이 위치한다면,
- 화면에서는 .container 하단에 navbar 가 위치하게 되며, 스크롤을 내리면 navbar 가 그대로 상단에 붙어서 따라온다

```HTML
<body>

	<div class='container' style='background-color: lavenderblush;'>
		<div class='container'>
			<div class='navbar-header'>
				<a class='navbar-brand' href='#'>Bootstrap</a>
			</div>
		</div>
	</div>

	<div class='navbar navbar-expand-sm bg-light justify-content-center sticky-top'>
		<div class='navbar-header'>
			<a class='navbar-brand' href='#'>Bootstrap</a>
		</div>
		
		<ul class='navbar-nav'>
			<li class='nav-item'>
				<a class='nav-link' href='#'>JSP</a>
			</li>
			<li class='nav-item'>
				<a class='nav-link' href='#'>Servlet</a>
			</li>
			<li class='nav-item'>
				<a class='nav-link' href='#'>Spring</a>
			</li>
			<li class='nav-item'>
				<a class='nav-link disabled' href='#'>Gift Page</a>
			</li>
		</ul>
	</div>

	<div class='container' style='background-color: lavenderblush;'>
		<div class='container'>
			<div class='navbar-header'>
				<a class='navbar-brand' href='#'>Bootstrap</a>
			</div>
		</div>
	</div>
	...
</body>
```
### navbar 에 추가 가능한 클래스들

- navbar 배경색
	* 기본 하얀색 배경에 파란색 컨텐트가 적용된다
	* .bg-light navbar-light 는 밝은 회색 바탕에 검정색 혹은 짙은 회색의 컬러가 적용되며
	* .bg-dark navbar-dark 는 어두운 계열의 바탕색에 하얀색 혹은 밝은 회색의 컬러가 적용된다
	* 이 외에 .bg-primary, .bg-success, .bg-info, .bg-warning, .bg-danger, .bg-secondary, .bg-dark and .bg-light 등 적용 가능
- navbar brand / logo
	* .navbar 내부에 a 태그의 .navbar-brand 클래스를 적용하면, 네비게이션 바의 타이틀 (? 브랜드?) 설정이 가능하다
	* 튜토리얼에는 브랜드, 로고, 프로젝트 이름에 하이라이트 적용이 가능하다고 설명되어 있다
	* 만약 이미지 로고를 적용하고 싶다면, .navbar-brand 클래스 내부에 img 태그를 삽입하면 된다
- .navbar-expand-xl|lg|md|sm 클래스는 네비게이션 바가 사이즈에 따라 반응형으로 나타나도록 한다
	* .navbar 내부에 ul 태그의 .navbar-nav 클래스를 적용하면 여러 링크들을 내부 li 태그로 추가할 수 있다.
	* li 태그의 클래스명은 .nav-item 으로 하고, 내용은 a 태그의 .nav-link 로 한다
- .justify-content-conter 클래스를 적용하면 navbar 내부의 컨텐트들이 중앙정렬된다
- Collapsing The Navigation Bar
	* 네비게이션 링크들(목록들)을 숨기고 그것을 모아 클릭 시 나타나도록 하는 하나의 버튼으로 바꾸고 싶을 때
	* .navbar-toggler 클래스, data-toggle='collapse', data-target='#thetarget' 요소들을 사용한다
	* 토글러로 바꾸고자 하는 목적 링크들(목록들)에는 div class='collapse navbar-collapse' 클래스를 사용한다. 이것의 아이디는 네비게이션 링크의 data-target 아이디와 일치시킨다
	* 예제를 보면 이해가 쉽다

```HTML
	...
	<div class='navbar navbar-expand-lg bg-dark navbar-dark justify-content-center sticky-top'>
		<div class='navbar-header'>
			<a class='navbar-brand' href='#'>Bootstrap</a>
		</div>
		
		<button class='navbar-toggler' type='button' data-toggle='collapse' data-target='#collapsibleNavbar'>
			<span class='navbar-toggler-icon'></span>
		</button>
		
		<div class='collapse navbar-collapse' id='collapsibleNavbar'>
			<ul class='navbar-nav'>
				<li class='nav-item'>
					<a class='nav-link' href='#'>JSP</a>
				</li>
				<li class='nav-item'>
					<a class='nav-link' href='#'>Servlet</a>
				</li>
				<li class='nav-item'>
					<a class='nav-link' href='#'>Spring</a>
				</li>
				<li class='nav-item'>
					<a class='nav-link disabled' href='#'>Gift Page</a>
				</li>
			</ul>
		</div>
	</div>
	...
```

- 먼저 navbar navbar-expand-lg 로 반응형 범위를 지정하고 링크들을 대체할 버튼을 생성한다 (navbar-toggler)
- 타입은 버튼, data-toggle 방식은 collapse, 토글 대상은 #collapsibleNavbar 로 지정하고 버튼 내용으로 .navbar-toggler-icon 클래스를 추가한다
- 대체할 ul>li 링크들은 .collapse navbar-collapse 클래스로 감싸고 id 를 앞서 명시한 것과 같은 collapsibleNavbar 로 지정한다





















