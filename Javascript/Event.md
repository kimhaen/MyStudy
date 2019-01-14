
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190114-lightgray.svg?style=flat-square)


## 이벤트

스크립트 내에서 이벤트 키워드는 무조건 소문자이다. 카멜 타입 그딴거 없음

### 이미지 앨범 중 특정 이미지에 마우스가 올라가면 이미지를 크게 보여줌

```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>album</title>
<style>
	#imgs {
		overflow: hidden;
	}
	.img {
		display: inline-block; 
		margin-left: 3px;
		border: 1px solid black;
	}
</style>
<script>
	function main_album () {
		var main_img = document.getElementById('main');
		var imgs = document.getElementsByClassName('img');
		for (var i = 0; i < imgs.length; i++) {
			imgs[i].onmouseover = function (ev) {
				main_img.src = ev.srcElement.src;
			}
			
		}
	}
</script>
</head>
<body>

	<div id='album'>
		<h1>Album</h1>
		<p id='info'>
			특정 이미지 위에 마우스가 올라가면 이미지를 크게 보여줌
		</p>
		<img src='../images/mountain.png' width='400px' height='300px' id='main'/><br/>
		<div id='images'>
			<img src='../images/baby.png' width='90px' height='50px' class='img'/>
			<img src='../images/camel.png' width='90px' height='50px' class='img'/>
			<img src='../images/lion.png' width='90px' height='50px' class='img'/>
			<img src='../images/damuni.jpg' width='90px' height='50px' class='img'/>
		</div>
	</div>
	<script>main_album()</script>

</body>
</html>
```

### 같은 클래스 그룹에 대한 포커싱 및 select

- 폼 태그에서 focus(), select() 하기 위한 그룹을 같은 클래스명으로 지정하고 script 에서 반복문과 조건문을 이용해 상황에 맞게 세팅되도록 한다

뷰 파일에서 클래스 그루핑 후,

```JAVASCRIPT
function mainInsert() {
    var frm = document.member;

    frm.id.focus();
    frm.id.select();

    // 다 입력한게 아니라면 엔터키 누르면 커서 이동
    var items = document.getElementsByClassName('item');
    for (var i = 0; i < items.length; i++) {
        items[i].onkeyup = function (ev) {
            if (ev.keyCode == 13) {
                var obj = null;
                switch (ev.srcElement.name) {
                    case 'id':
                        obj = frm.irum;
                        break;
                    case 'irum':
                        obj = frm.pwd;
                        break;
                    case 'pwd':
                        obj = frm.email;
                        break;
                    case 'email':
                        obj = frm.phone;
                        break;
                    case 'phone':
                        obj = frm.rdate;
                        break;
                }
                obj.focus();
                obj.select();
            }
        }
    }

...

}
```

## 자바스크립트의 객체

### Math 객체

- Math.random(), Math.round(x) x 반올림, Math.ceil(x) x 보다 크거나 같은 정수, Math.floor(x) x 보다 작거나 같은 정수
- 음수 영역에서 반올림이나 ceil, floor 시 잘 따져봐야 함. 헷갈릴 수 있음

### Date 객체

- 객체 생성 : new Date(), new Date(y, m, d, ho, mi, se)
- 주요 메서드 : getYear(), getMonth() 0~11 월까지 출력되므로 +1 해야함, getDate(), getDay() 0=일요일, getHours(), getMinutes(), getSeconds()

#### 시계 만들기 예제

```JAVASCRIPT
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Date OBJ Test</title>
<style>
	#dateObj > #dateField {
		width: 700px;
		height: 250px;
		font-size: 24pt;
		font-weight: bold;
		text-align: center;
		padding-top: 160px;
		background-color: #999999;
		border-radius: 5px;
	}
</style>
<script>
	var btnDate;
	var dateField;
	var week = ['일요일', '월요일', '화요일', '수요일', '목요일', '금요일', '토요일'];
	window.onload = function () {
		btnDate = document.getElementById('btnDate');
		dateField = document.getElementById('dateField');
		btnDate.onclick = createDate;
		setInterval(createDate, 1000);
	}
	
	function createDate () {
		var now = new Date();
		var str = '';
		str += now.getFullYear() + '년 ';
		str += now.getMonth() + 1 + '월 ';
		str += now.getDate() + '일 ';
		str += week[now.getDay()] + ' / '; // now.getDay() 의 반환값은 정수임(일요일==0)
		str += now.getHours() + '시 ';
		str += now.getMinutes() + '분 ';
		str += now.getSeconds() + '초 ';
		
		dateField.innerHTML = str;
	}
</script>
</head>
<body>

	<div id='dateObj'>
		<input type='button' id='btnDate' value='날 짜' />
		<div id='dateField'></div>
	</div>

</body>
</html>
```

### 윈도우 객체 - opener

- 브라우저에서 새 창을 실행하면, 새 창 입장에서 자신을 open 시킨 부모 창을 opener 라 지칭한다
- 윈도우 메서드 : alert(), close(), confirm(), prompt(), ...
- 윈도우 이벤트 : onblur, ondragdrop, onfocus, onload, onmove, onresize, onunload, ...

#### 윈도우 창 끼리 값을 전달하는 예제

우편번호 검색을 위해 새 창을 열고, 새 창은 선택된 값을 opener 에게 전달해야 한다. (검색은 외부 API 를 사용하면 됨)

windowMain.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>winMain</title>
<script>
	function windowMain () {
		var frm = document.frm;
		var win;
		frm.btnZipCode.onclick = function () {
			win = window.open('windowSub.jsp', 'win', 'width: 150px; height: 30px; toolbar=no');
		}
	}
	function func() {
		alert("우편번호를 검색하였습니다.");
	}
</script>
</head>
<body>

	<div id='winMain'>
		<h1>window main</h1>
		<p id='info'>
			서브창을 열고 서브창에서 메인창의 함수나 기타 요소 접근
		</p>
		<form name='frm'>
			<label>우편번호</label>
			<input type='text' size='6' name='zip_code' disabled />
			<input type='button' value='우편번호' name='btnZipCode' /><br/>
			<label>주소</label>
			<input type='text' size='50' name='address' />
		</form>
	</div>
	
	<script>windowMain()</script>

	<!-- <form name='f1'>
		<input type='text' name='irum1' value='abc' />
		<input type='button' id='btnOpen' value='open' />
		<input type='button' id='btnClose' value='close' />
	</form> -->

</body>
</html>
```

windowSub.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>winSub</title>
<style>
	#winSub #sel {
		width: 100px;
	}
</style>
<script>
	function windowSub () {
		var sel = document.getElementById('sel');
		var parent = window.opener;
		sel.ondblclick = function () {
			var index = sel.selectedIndex;
			var code = sel.options[index].value;
			var address = sel.options[index].text;
			
			parent.document.frm.zip_code.value = code;
			parent.document.frm.address.value = address;
			
			parent.func();
			
			self.close();
		}
	}
</script>
</head>
<body>

	<div id='winSub'>
		<h1>window sub</h1>
		<p>
			우편번호 검색
		</p>
		
		<select id='sel' size='10'>
			<option value='00-000'>주소 선택</option>
			<option value='11-111'>종로구</option>
			<option value='11-222'>은평구</option>
			<option value='11-333'>가양구</option>
			<option value='11-444'>인한구</option>
			<option value='11-555'>송정구</option>
		</select>
	</div>
	
	<script>windowSub()</script>

	<!-- <form name='f2'>
		<input type='text' name='irum2' value='def' />
		<input type='button' name='btnOk' value='O K' />
	</form> -->

</body>
</html>
```

#### 입력된 두 수의 합계를 서브창을 통해 계산하여 출력

windowMain02.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>window main 02</title>
<script>
	function winMain02 () {
		var frm = document.frm;
		var win;
		frm.btnSum.onclick = function () {
			win = window.open('windowSub02.jsp', 'win', 'width: 200px; height: 50px;, toolbar=no');
			
			win.onload = function () {
				frm.result.value = win.compute(frm.firstNum.value, frm.secondNum.value);
				win.close();
			}
		}
	}
	
	function func () {
		alert('더하기 완료!');
	}
</script>
</head>
<body>

	<div id='winMain02'>
		<h1>메인창과 서브창 실습</h1>
		<p>
			메인창에서 서브창을 실행하고 두 수 를 전달, 서브창은 두 수의 합을 계산하여 메인창에 표시
		</p>
		
		<form name='frm'>
			<label>첫 번째 수 입력 : </label>
			<input type='text' name='firstNum' /><br/>
			<label>두 번째 수 입력 : </label>
			<input type='text' name='secondNum' /><br/><br/>
			<label>결과 : </label>
			<input type='text' name='result' disabled /><br/>
			<input type='button' name='btnSum' value='합 계 창 열기' />
		</form>
	</div>
	
	<script>winMain02()</script>

</body>
</html>
```

windowSub02.jsp
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Window Sub 02</title>
<script>
	function compute (firstNum, secondNum) {
		return Number(firstNum) + Number(secondNum);
	}
</script>
</head>
<body>

</body>
</html>
```

### Dialogue 상자

- alert(), confirm(), prompt('메시지', '초기값')

### document 객체

### location 객체

### nevigator 객체

### history 객체

### 등등 여러 객체가 있으니 꾸준히 공부할 것


















