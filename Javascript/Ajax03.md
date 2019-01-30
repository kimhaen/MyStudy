
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190130-lightgray.svg?style=flat-square)

# AJAX 03

## DOM 개요

- Document Object Model
- DOM 은 문서의 각 태그들을 트리 형식으로 구조화한 것이다
- DOM API 는 이러한 트리 구조를 메모리에 매핑한 후 여기에 접근할 수 있도록 만들어진 API 를 의미한다
- 부모.자식.자식. ... 순으로 세대별로 접근이 가능하단 소리다
- 네이버의 검색자동추천 기능도 DOM 구조를 이용해 요소를 집어내 ajax 로 동적으로 구현한다

## DOM 장단점

- DOM 구조화의 가장 큰 장점은 페이지의 거의 모든 태그 및 요소들에 계층적으로 명료하게 접근할 수 있다는 점이다
- 각 데이터를 정확히 뽑아서 가져올 수 있는 만큼, 페이지 리로드 없이 해당 내용을 정확하게 변경/삭제 할 수도 있다
- 그러므로 보안상의 이유라면 스크립트 변수 선언 시 전역보다는 지역 개념으로 활용해서 예상치 못한 변경을 최소화하는 것이 좋다
- DOM 의 가장 큰 단점(?) 은 구조를 이해하고 정확히 핸들링하기 쉽지 않다는 점이다

## DOM 구조

- Document : 전체 문서 (페이지)
- Element : 각 태그들
- Text : 문자열 데이터
- CDataSection : xml 문서의 CData 영역의 문자열 값을 저장

## 주요 프로퍼티

- DOM 각 요소 수준에 따라 지정할 수 있는 프로퍼티가 존재한다

### Document Interface

- Element 타입의 documentElement : 문서의 루트 노드를 나타낸다
- html 문서에서 document.documentElement.nodeName 을 스크립트로 실행하면 루트 엘리먼트인 'HTML' 이 표시된다
- 주요 함수
	* NodeList getElementsByTagName('태그명') : 지정 태그명에 해당하는 태그들을 포함하는 NodeList 배열을 반환
	* Element getElementById('id') : id 에 해당하는 태그를 구한다 (한개)
	* NodeList getElementsByClassName('태그명') : 지정된 클래스명을 NodeList 로 반환한다

```JSP
<body>

	<h3>getElementsByClassName</h3>

	<div id='data'>
		<label><input type='checkbox' value='A' class='alpha'>A</label>
		<label><input type='checkbox' value='B' class='alpha'>B</label>
		<label><input type='checkbox' value='C' class='alpha'>C</label>
		<label><input type='checkbox' value='D' class='alpha'>D</label>
	</div>

	<script>
		var data = document.getElementById('data');
		var nodes = data.getElementsByClassName('alpha');

		for(i = 0 ; i<nodes.length ; i++){
			document.write(i + ":" + nodes[i].value + '<br/>');
		}

	</script>
	</body>
</html>
```

- 위 코드에서 그냥 document.getElementsByClassName('alpha') 메서드는 페이지 전체에서 클래스가 알파인 요소를 선택한다
- document.getElementById('data') 후 클래스명을 지정하면 아이디가 data 인 div 요소 내부의 alpha 클래스만 선택하게 된다
- getElementsByTagName 의 결과값이 NodeList 임을 인지할 것

[예제]interface_doc.jsp
```JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Interface Document Page</title>
</head>
<body>

	<div id='domDocumentTest'>
		<h2>Interface Document Page</h2>
		<form name='frm'>
			<input type='text' name='irum' value='hong' id='id_irum' class='class_irum' /><br/>
			<input type='search' name='address' value='종로3가' id='id_address' class='class_address' /><br/>
			<input type='color' name='hair' value='#ff0000' id='id_hair' class='class_hair' /><br/>
			<p/>
			<input type='button' id='btn' value='확 인' name='btn' class='class_btn' />
		</form>
		
		<h3>Result Area</h3>
		<div id='result'></div>
	</div>
	<script>
		var btn = document.getElementById('btn');
		btn.onclick = function () {
			var doc = document.getElementById('domDocumentTest');
			var result = document.getElementById('result');
			var str = '';
			
			// 모든 input 태그
			var input = doc.getElementsByTagName('input');
			str = '<li> number of input tag : ' + input.length + '</li>';
			
			for (var i = 0; i < input.length; i++) {
				str += '<ul>';
				str += '<li> type : ' + input[i].type + '</li>';
				str += '<li> name : ' + input[i].name + '</li>';
				str += '<li> value : ' + input[i].value + '</li>';
				str += '<li> id : ' + input[i].id + '</li>';
				str += '<li> class : ' + input[i].className + '</li>';
				str += '</ul>';
			}
			
			result.innerHTML = str;
		}
	</script>

</body>
</html>
```

### Node Interface

- String nodeName : 노드의 이름
- String nodeValue : 노드의 값
- unsigned short nodeType : 노드의 타입
- Node parentNode : 부모 노드
- NodeList childNodes : 자식 노드(들)
- Node firstChild : 첫 번째 자식 노드
- Node lastChild : 마지막 자식 노드
- Node previousSibling : 자기 자신의 이전 노드(좌측)
- Node nextSibling : 자기 자신의 이후 노드(우측)
- Document ownerDocument : 자신을 포함하고 있는 Document 객체

#### nodeName 과 nodeValue 의 비교

- Document 노드는 이름은 있으나(#document) 특정 값이 없음
- Element 노드는 지정 태그명을 의미하며 특정 값이 없음
- Text 노드는 #text 라는 이름이 있고 문자열을 값으로 갖는다

#### 노드 타입 (nodeType)

- 1 번인 ELEMENT_NODE 가 가장 많이 사용된다. 일반적인 태그들은 Element 로 분류되기 때문이다
- 2 번은 ATTRIBUTE_NODE, 12 번까지 있으며 추후 필요시 찾아볼 것

#### 노드에 접근하기

- length : NodeList 의 경우 리스트의 길이를 반환
- item(n) : NodeList 에서 n 번째 노드를 나타냄

#### 노드의 속성, 타입, 접근방식을 활용한 예제

interface_node.jsp
```JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Interface Node Page</title>
<!-- <link rel='stylesheet' href='https://maxcdn.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css' />
<script src='https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js'></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.6/umd/popper.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.2.1/js/bootstrap.min.js"></script> -->
</head>
<body>

	<div id='domNodeTest'>
		<h2>Interface Node Page</h2>
		
		<div id='main' style='border: 1px solid black; margin: 0 18px 9px 0; padding-left: 9px;'>
			<section>
				<h2>Title111</h2>
				<p>Content111</p>
			</section>
			<section>
				<h2>Title222</h2>
				<p>Content222</p>
			</section>
		</div>
		
		<input type='button' id='btn' value='그대로 확 인' />
		<input type='button' id='btnEle' value='실제 요소만 확 인' />
		<h3>Result Area</h3>
		<div id='result'></div>
	</div>
	
	<script>
		var btn = document.getElementById('btn');
		var result = document.getElementById('result');
		btn.onclick = function () {
			var main = document.getElementById('main');
			var childs = main.childNodes;
			
			var str = '<li> [그대로 확인]  number of nodes in main div : ' + childs.length + '</li>';
			str += '<ul>';
			for (var i = 0; i < childs.length; i++) {
				var node = childs.item(i);
				str += '<li> ' + (i + 1) + 'st node : ' + node.nodeName + '</li>'
			}
			str += '</ul>';
			
			result.innerHTML = str;
		}
		
		btnEle.onclick = function () {
			var main = document.getElementById('main');
			var childs = main.childNodes;
			
			var cnt = 0;
			for (var i = 0; i < childs.length; i++) {
				var node = childs.item(i);
				if (node.nodeType == 1) cnt++;
			}
			var str = '<li> [실제 요소만 확인]  number of element nodes in main div : ' + cnt + '</li>';
			str += '<div>';
			for (var i = 0; i < childs.length; i++) {
				var node = childs.item(i);
				if (node.nodeType != 1) continue;
				str += '<p> ' + node.nodeName + (i + 1) + '</p>';
				var sectionChilds = node.childNodes;
				
				for (var j = 0; j < sectionChilds.length; j++) {
					var childNode = sectionChilds.item(j);
					if (childNode.nodeType != 1) continue;
					str += '<ul><li> ' + (j + 1) + 'st node : ' + childNode.nodeName + '</li>'
					str += '<li> ' + (j + 1) + "st node's first value : " + childNode.firstChild.nodeValue + '</li></ul>';
				}
			}
			str += '</div>';
			
			result.innerHTML = str;
		}
	</script>

</body>
</html>
```

- div id='main' 노드 내부에는 화면상에 두 개의 자식 노드가 존재하나, 사이사이 공백에 공백을 의미하는 #Text 타입의 노드가 존재해서 총 5 개가 된다
- 그러므로 스크립트 반복 시 해당 노드의 타입이 무엇인가에 대한 필터링 로직이 필요하다 (nodeType)
- [실제 요소만 확인] 스크립트에서 필터링이 적용되었으며,
- 또 각 section 노드의 자식 노드로 #Text 타입의 실제 문자열들이 존재하므로, 그것들에 대한 루핑 및 필터링을 수행한다

### Element Interface

- Element 인터페이스는 Node 인터페이스를 상속받아 만들어졌으며 일반적으로 element 나 html tag 로 불려지는 요소들의 속성을 제어할 수 있다








