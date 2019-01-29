
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190129-lightgray.svg?style=flat-square)

# AJAX

## 특징

- 비동기 방식의 자바스크립트와 XML (Asynchronous JavaScript XML)
- Ajax 는 별도 프로그램의 설치가 필요하지 않다 (ActiveX 와 같은 플러그인 프로그램)
- XMLHttpRequest 객체를 사용해 서버와 통신한다
- XMLHttpRequest 객체는 보안상의 이유로 같은 도메인의 URL 에만 접속 가능
- 기존 웹 방식과의 비교
	* 기존 : 반드시 현재 화면 갱신
	* Ajax : 바꾸거나 새롭게 내용을 추가할 위치만 변경 가능 (화면 새로고침 하지 않는다)

## 구성 요소

- XMLHttpRequest : 웹 서버와 클라이언트 간 통신 담당
- DOM : 문서의 구조를 나타낸다
- CSS : UI 와 관계된 부분 담당
- JavaScript : 이벤트 발생시킴, XMLHttpRequest 제어, 요청 및 수신 정보에 대한 주요 기능 처리
- 처리 절차
	* 이벤트 발생 > DOM > XMLHttpRequest > 서버 응답 > XMLHttpReqeust > DOM > 화면

## XMLHttpRequest 객체란?

- Ajax 가 서버와 데이터 통신할 때 사용하는 객체
- 지금은 웹 표준에 포함된다
- 거의 모든 웹 브라우저가 지원, w3c 표준안을 따른다고 보면 됨

## 주요 레퍼런스

### XMLHttpRequest 객체 지정하기 (객체 생성)

```JAVASCRIPT
xhr = new XMLHttpRequest();
```

### open()

- 객체를 생성하면 open() 메서드로 요청 설정을 초기화하고 (xhr.open(method, url, 비동기방식 여부, [사용자이름, 암호]);)
- xhr.send() 메서드로 전송한다. post 방식일 경우 이 메서드에 파라미터를 지정할 수 있다
- 이는 form 태그의 구성과 유사하다

```JAVASCRIPT
xhr = new XMLHttpRequest();
xhr.open("POST","/test.jsp");
xhr.send("data=itdocument");
```

### status | statusText

- HTTP 요청 결과의 상태 코드를 저장하고 있는 프로퍼티

```JAVASCRIPT
var value = xhr.status;
text = xhr.statusText;
```

- 200, 403, 404, 500
- 200 이 정상처리완료

### readyState

- 요청 처리에 대한 수신 결과를 나타내는 프로퍼티

```JAVASCRIPT
var value = xhr.readyState
```

- 0, 1, 2, 3, 4
- 4 번은 COMPLETED 상태를 나타낸다 (주로 이것을 사용함)

### 이벤트 - readyState 가 바뀌었다면? (4 번, 정상처리 완료 시)

- onreadystatechange 이벤트가 발생한다

```JAVASCRIPT
xhr.onreadystatechange = function () { ... }
```

- 이벤트 발생 시 콜백 함수나 이벤트 핸들러 함수를 작성하여 처리 결과를 화면의 해당 위치에 갱신한다

### 이벤트 처리결과

- responseXML, responseText, responseURL 로 확인한다
- onreadystatechange 이벤트 이후
- 서버에서 요청 처리가 완료되고 결과를 완전히 수신했다면
- responseXML, responseText, responseURL 중 요청 페이지에 해당하는 방식을 활용해 화면에 표현한다

```JAVASCRIPT
if (xhr.readyState == 4 && xhr.status == 200) {
	var data = xhr.responseText;
	data = data.replace(/\n/g, '<br/>');
	var result = document.getElementById('result');
	result.innerHTML = data;
}
```

- 이때 페이지 리로딩 하지 않고도 부분적으로만 요청 처리가 수행됨을 주목할 것!

### Ajax 테스트 페이지 코드

```JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Load File Page</title>
<script>
	function get (id) {
		return document.getElementById(id);
	}
	function mainLoadFile () {
		var btnText = get('btnText');
		var btnHtml = get('btnHtml');
		var btnJsp = get('btnJsp');
		var btnXml = get('btnXml');
		var btnJson = get('btnJson');
		
		var xhr = new XMLHttpRequest();
		
		btnText.onclick = function () {
			xhr.open('get', '/views/ajax/test.txt');
			xhr.send();
			xhr.onreadystatechange = function () {
				if (xhr.readyState == 4 && xhr.status == 200) {
					var data = xhr.responseText;
					data = data.replace(/\n/g, '<br/>');
					var result = get('result');
					result.innerHTML = data;
				}
			}
		}
		btnHtml.onclick = function () {
			xhr.open('get', '/views/ajax/test.html');
			xhr.send();
			xhr.onreadystatechange = function () {
				if (xhr.readyState == 4 && xhr.status == 200) {
					var data = xhr.responseText;
					var result = get('result');
					result.innerHTML = data;
				}
			}
		}
		btnJsp.onclick = function () {
			xhr.open('get', '/views/ajax/test.jsp');
			xhr.send();
			xhr.onreadystatechange = function () {
				if (xhr.readyState == 4 && xhr.status == 200) {
					var data = xhr.responseText;
					var result = get('result');
					result.innerHTML = data;
				}
			}
		}
	}
</script>
</head>
<body>

	<div id='loadFile'>
		<h2>Load File Page</h2>
		<p>XMLHttpRequest 를 통해 text, html, jsp, xml, json 타입의 문서를 읽어 result 영역에 표시해 본다</p>
		
		<div id='btnZone'>
			<input type='button' value='TEXT' id='btnText' />
			<input type='button' value='HTML' id='btnHtml' />
			<input type='button' value='JSP' id='btnJsp' />
		</div>
		<br/>
		<div id='result'>
			읽어들인 문서가 표시될 곳
		</div>
	</div>
	
	<script>mainLoadFile();</script>

</body>
</html>
```

- 각 버튼을 클릭하면 자바스크립트 함수를 활용하여 해당되는 요청을 처리 후 결과를 수신받아 이상이 없다면 result 영역에 표현한다






