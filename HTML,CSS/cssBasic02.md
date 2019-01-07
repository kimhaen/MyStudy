
## CSS 이어서

- class 명을 띄어쓰기를 기준으로 여러 개를 쓴다면, 그만큼 세부적으로 분류되는 것이다
- 사이즈나 화소 단위 : pt, px, em, ... (모바일에서는 px 사용하지 않음)
- 폰트 사이즈 : 전체 절대픽셀을 지정하고, 각 부분에서는 상대크기로 세분화한다 (전체 12px; 상대 3.0em, 1.5em, ...)
- 색상 적용 : 키워드를 통해 적용할 수도 있지만, 그것보다는 HEX 코드 단위나 RGB 색상 단위로 세밀하게 사용할 것
	- RGBA, HSLA 의 A 는 투명도를 의미한다 (투명0.0 ~ 1.0불투명)
- 이미지나 파일 불러오기 : URL 방식을 사용한다
- display 속성 (주로 사용하는 것)
	- none(나타나지 않음, 자리를 차지하지도 않음)
	- block(div 와 같은 속성, 무조건 줄바꿈)
	- inline(span 과 같은 속성, 가로세로 사이즈 적용되지 않음, 딱 자기 크기만큼만)
	- inline-block(블럭이긴 블럭인데 인라인으로 자리를 잡음, 다만 블럭과 블럭 사이에 유격이 발생함)
	- 유격을 없이 배치하려면 float:left 같은 속성을 사용
- visibility: hidden;
	- display:none; 처럼 화면상에 나타나지는 않지만, 자리를 차지한다는 차이점이 있다
	- display:none; 은 아예 화면에서 사라지므로 다른 컴포넌트들이 그만큼 재배치되지만, visibility:hidden; 은 사라져도 공간은 차지하므로 재배치되는 일은 없다. (다만 그만큼 공백이 발생)
- opacity 속성
	- 태그의 투명도를 조절하는 속성이다 (0.0 ~ 1.0)
	- opacity: 0.2; 는 div 내부 모든 것들을 20% 로 투명화하고, background-color: rgba(0,0,0,0.2); 는 말 그대로 배경만 20% 로 투명화한다
- box 속성
	- width, height, padding, border, margin 속성들을 의미한다. 각각 동서남북 조절도 가능하다
	- box-sizing 속성은 width 와 height 속성에 대해서 박스 사이징을 지정한다. 예를 들어 가로 세로를 100px 씩 주고 border 10px 씩 준 두 div 에 대해서,
		- box-sizing:content-box; 를 적용한 div 는 결과적으로 110px, 110px 이 되고
		- box-sizing:border-box; 를 적용한 div 는 border 까지 포함한 100px, 100px 이 된다
	- border-radius: 20px; 테두리 각 모서리에 적용되는 원의 반지름이 20px 이 되도록 한다는 의미
- background-image 속성
	- background-image:url(['URL']); 방식으로 적용된다. url 뒤에 콤마 후 또다른 url 을 입력하면 배경이 중첩 적용된다. (왼쪽에 입력한 이미지가 앞으로 나옴)
	- background-size:[넓이]% [높이]%; 로 적용된다. (혹은 px 로)
	- background-repeat (x축, y축), background-attachment, background-position
- font 속성
	- font-size, font-family : 폰트는 사용자 컴퓨터에 설치되어 있지 않다면 깨져서 나올 수 있다. 해결법으로는 여러 폰트를 사전지정 해놓거나 cdn 방식으로 다운받도록 하는 것이다
	- font-style, font-weight, text-decoration, line-heignt (컴포넌트 내 텍스트의 위치를 조정, 일반적으로 중앙정렬 시 사용)

### 위치 속성

- 웹 브라우저에서 좌표 체계는 좌상단을 기점으로 x 축 양의 방향, y 축 음의 방향으로 진행한다. 우리의 일반관념과 다소 다르기에 계산식이 복잡하게 느껴질 수 있다
- 절대 좌표와 상대 좌표
	- 절대 위치는 x, y 축을 지정하고, 상대 위치는 요소를 입력한 순서를 통해 상대적으로 위치를 지정한다.
	- fixed, absolute (절대 좌표) / static, relative (상대 좌표)
- position 속성
	- position:relative; 상대영역. 주변 컴포넌트에 맞게 위치가 변경된다
	- position:absolute;
		- 부모가 relative, 충분한 height 를 가지고 있다면, absoute 인 컴포넌트는 부모 기준으로 지정된 포지션에 위치한다. 부모가 이동하면 그에 따라 같이 이동한다
		- 그런데 만약 부모가 relative 가 아니거나 부모가 아예 없다면 페이지에 따라 포지셔닝 된다
		- position:absolute; 에 대한 위치 지정은 top, right, bottom, left 로 지정한다
		- left, top 위치 지정으로 div 레이어를 만들 수 있다
- z-index 속성
	- style 태그 내 각 선택자 이름 뒤에 z-index 우선순위를 적용하면 우선순위가 높은(숫자가 큰) 컴포넌트가 레이어 앞에 위치한다
- overflow 속성
	- 부모의 영역을 벗어나는 컴포넌트에 대한 속성지정
	- hidden, scroll 키워드
- float 속성
	- left, right > 왼쪽부터 붙이기 오른쪽부터 붙이기
	- 보통 이미지 태그에 적용하는데, 원래는 텍스트와 분리되었던 이미지가 텍스트 위에 부유(?)하게 된다.
	- 예를 들어 float:left; 라면 이미지가 왼쪽에 붙고 그 다음 상단부터 텍스트가 배치된다

### position 실습

1. position.jsp

position:fixed 컴포넌트 만들기
position:absolute 버튼 4 개를 격자 형식으로 만들되, 부모에 대한 절대위치로 지정한다
position:relative 버튼 4 개를 격자 형식으롬 만들되, 각 컴포넌트 간의 상대 위치로 만든다

2. span 태그로 구성된 메뉴 바 만들기


position.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Position</title>
<style>
	* {
		width: 50px;
		height: 50px;
		font-size: 9pt;
	}
	
	
	#button-fixed {
		width: 150px;
		height: 150px;
		border: 1px solid black;
		display: block;
		position: fixed;
		top: 350px;
		right: 0;
	}
	#button-fixed button[type=button] {
		position: fixed;
		top: 400px;
		right: 50px;
	}
	
	
	#button-absolute {
		width: 300px;
		height: 300px;
		border: 1px solid black;
		position: relative;
		top: 100px;
	}
	#button-absolute #button1 {
		position: absolute;
		top: 100px;
		left: 100px;
	}
	#button-absolute #button2 {
		position: absolute;
		top: 100px;
		left: 155px;
	}
	#button-absolute #button3 {
		position: absolute;
		top: 155px;
		left: 100px;
	}
	#button-absolute #button4 {
		position: absolute;
		top: 155px;
		left: 155px;
	}
	
	
	#button-relative {
		width: 300px;
		height: 300px;
		border: 1px solid black;
		position: relative;
		top: 150px;
	}
	#button-relative #button1 {
		position: relative;
		top: 100px;
		left: 100px;
	}
	#button-relative #button2 {
		position: relative;
		top: 100px;
		left: 100px;
	}
	#button-relative #button3 {
		position: relative;
		top: 153px;
		right: 8px;
	}
	#button-relative #button4 {
		position: relative;
		top: 153px;
		right: 9px;
	}
</style>
</head>
<body>

	<div id='button-absolute'>
		<button type='button' id='button1'>버튼1</button>
		<button type='button' id='button2'>버튼2</button>
		<button type='button' id='button3'>버튼3</button>
		<button type='button' id='button4'>버튼4</button>
	</div>
	<div id='button-relative'>
		<button type='button' id='button1'>버튼1</button>
		<button type='button' id='button2'>버튼2</button>
		<button type='button' id='button3'>버튼3</button>
		<button type='button' id='button4'>버튼4</button>
	</div>
	<div id='button-fixed'>
		<button type='button'>버튼!</button>
	</div>
	
	
	
	
	
	<div style='position: absolute; top: 1500px; right: 0;'>
		hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>
		hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>
		hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>
		hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>hihi<br/>	
	</div>

</body>
</html>
```


position2.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Position2</title>
<style>
	#position2 {
		width: 700px;
		margin: 100px auto;
	}
	#position2 > #menu {
		overflow: hidden;
		height: 60px;
		margin-bottom: 20px;
	}
	#position2 > #menu > #item {
		float: right;
	}
	#position2 > #menu > #item > span {
		padding: 5px;
		width: 70px;
		display: block;
		background: : #333333;
		color: #ffff00;
		text-align: center;
		float: left;
		box-shadow: 0 3px 4px #aaaaaa;
	}
	#position2 > #menu > #item > span:hover {
		background: #000;
		color: #fff;
		cursor: pointer;
	}
	#position2 > #menu > #item > span:first-child {
		border-radius: 15px 0 0 15px;
	}
	#position2 > #menu > #item > span:last-child {
		border-radius: 0 15px 15px 0;
	}
</style>
</head>
<body>

	<div id='position2'>
		<h2>타!이!틀!</h2>
		<div id='menu'>
			<div id='item'>
				<span>HTML</span>
				<span>CSS3</span>
				<span>JAVA</span>
				<span>DB</span>
				<span>JavaScript</span>
			</div>
		</div>
		<div id='contents'>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
			컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트 컨텐트<br/>
		</div>
	</div>

</body>
</html>
```













