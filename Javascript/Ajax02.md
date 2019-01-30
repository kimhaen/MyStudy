
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190130-lightgray.svg?style=flat-square)

# AJAX 02

## 서버로부터 요청 데이터 집합을 반환받을 때

- 개인용
	* 가장 최적화된 환경을 위해 적절한 알고리즘의 데이터 형식을 선택할 수 있다
	* 예를 들어 빠른 검색을 원한다면 인덱스를 갖는 list 방식이 좋고, 잦은 input/output 이 발생한다면 set 이나 map 구조를 고려한다
- 사업용
	* 기존 자료구조나 ResultSet 을 그대로 반환하는 것은 좋지 않다
	* 불특정 다수의 유저가 그러한 자료형을 직접 핸들링해야 할 수 있음
	* 그러므로 적절한 처리가 선행되는 xml 이나 json 형식으로 가공해서 반환하는 것이 좋다
- 설명
	* 웹 페이지에서 특정 데이터 처리결과가 필요할 때
	* 서버로부터 아예 새로운 정보를 request 객체에 받아 페이지 자체를 리로드하는 과정이 필요하다
	* 그러나 Ajax 방식을 활용한다면 전체적인 리로딩 없이 부분적으로 결과값을 받아 화면을 재구성할 수 있다
- 사업모델에 관해?
	* 기본적으로 사업의 이윤을 위해 합리적으로 생각할 수밖에 없다
	* 아무리 새롭고 편리한 기술이 있다고 해도 그런 효용성만으로 판단할 수는 없음
	* 안정성이나 발전가능성, 소요비용, 개발기간, 불필요한 오버스펙은 아닌지 등의 수많은 요소를 따져봐야 함
	* 같은 맥락으로 Ajax 가 아무리 효용성이 높아도 그것이 꼭 범용적으로 활용된다는 보장이 없다
	* 최근에는 오히려 Ajax 의 요소를 품고 있는 간편한 jQuery 를 사용하는 추세이다 (jQuery 는 정말 편하다고..)

## Ajax 이어서

- Ajax 의 핵심은 XMLHttpRequest 에 있다

### xml 요청 처리 수신하기

- 요청하는 페이지는 jsp 형식이지만, 실제 요청정보는 xml 형식이므로 코드 상단에 xml 을 의미하는 태그를 입력한다 (나머지 표현은 삭제)
- 그 밑에 jstl taglib 태그 입력 후 서버로 요청하고자 하는 정보에 대한 코드를 작성한다. 이 때 정보에 대한 태그는 적절하게 작성할 것
- 이하는 방명록에 대해 xml 형식으로 조회 요청하는 jsp 페이지 예제이다 (guestbook_xml.jsp)

guestbook_xml.jsp
```JSP
<?xml version='1.0' encoding='utf-8'?>
<%@page import="java.util.List"%>
<%@page import="com.myweb.guestbookDao.GuestBookDaoImpl"%>
<%@page import="com.myweb.guestbookDao.GuestBookDao"%>
<%@page import="com.myweb.guestbookBean.GuestBookVo"%>
<%@ taglib uri='http://java.sun.com/jsp/jstl/core' prefix='c' %>

<guestbook>
	<%
		GuestBookDao dao = new GuestBookDaoImpl();
		List<GuestBookVo> list = dao.list("", 1);
		request.setAttribute("list", list);
		response.setContentType("text/xml");
		response.setCharacterEncoding("utf-8");
	%>
	
	<c:forEach var='d' items='${list}'>
		<book>
			<serial>${d.serial }</serial>
			<id>${d.id }</id>
			<content>${d.content }</content>
			<mDate>${d.mDate }</mDate>
		</book>
	</c:forEach>
</guestbook>
```

- 이런 식으로 작성된 xml 소스를 DOM 구조라고 한다
- 서버로부터 데이터를 요청하는 스크립트와 그것을 반복출력하는 jstl 반복문, 임의의 태그로 구성된 데이터 요소로 구성된다
- 실제 로드된 페이지에서 소스를 찍어보면 다음과 같다

```XML
<?xml version='1.0' encoding='utf-8'?>






<guestbook>
	
	
	
		<book>
			<serial>55</serial>
			<id>hong</id>
			<content>하하하하</content>
			<mDate>2019-01-28 14:26:25</mDate>
		</book>
	
		<book>
			<serial>54</serial>
			<id>hong</id>
			<content>ppphhhhh</content>
			<mDate>2019-01-28 13:39:47</mDate>
		</book>
	
		<book>
			<serial>53</serial>
			<id>hong</id>
			<content>ppp</content>
			<mDate>2019-01-28 12:48:15</mDate>
		</book>
	
</guestbook>
```

- 일면 데이터 태그만 존재하는 것처럼 보이지만, 태그와 태그 사이에 개행과 같이 보이지 않는 텍스트 태그도 존재한다
- 어쨌든 이 xml 데이터를 가지고 결과값을 DOM 방식으로 화면에 표현한다

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
		var btnXml = get('btnXml');
		var xhr = new XMLHttpRequest();
		
		btnXml.onclick = function () {
			xhr.open('get', '/views/ajax/guestbook_xml.jsp');
			xhr.send();
			xhr.onreadystatechange = function () {
				if (xhr.readyState == 4 && xhr.status == 200) {
					var data = xhr.responseXML;
					var result = get('result');
					var list = data.getElementsByTagName('book'); // 추후 parser 를 사용하면 편하다
					var str = '';
					for (var i = 0; i < list.length; i++) {
						var book = list.item(i);
						var serial = getElement(book, 'serial');
						var id = getElement(book, 'id');
						var content = getElement(book, 'content');
						var mDate = getElement(book, 'mDate');
						
						str += '<ul>';
						str += '<li> Serial : ' + serial + '</li>';
						str += '<li> ID : ' + id + '</li>';
						str += '<li> Content : ' + content + '</li>';
						str += '<li> mDate : ' + mDate + '</li>';
						str += '</ul>';
					}
					
					result.innerHTML = str;
				}
			}
		}
		
		function getElement (obj, item) {
			return obj.getElementsByTagName(item).item(0).firstChild.nodeValue;
		}
	}
</script>
</head>
<body>

	<div id='loadFile'>
		<h2>Load File Page</h2>
		<p>XMLHttpRequest 를 통해 text, html, jsp, xml, json 타입의 문서를 읽어 result 영역에 표시해 본다</p>
		
		<div id='btnZone'>
			<input type='button' value='XML' id='btnXml' />
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

- xhr.responseXML 로 xml 페이지를 가져와 원하는 태그명(임의로 지정한)에 해당하는 list 배열을 뽑아와 그 길이만큼 요소를 반복출력한다
- 리스트 배열의 각 요소에도 세부적인 요소들이 추가적으로 있으므로, 별도의 사용자 정의 메서드(getElement)로 파싱하여 값을 가져온다

### JSON 요청 처리 수신하기

- 기본 자료구조
	* 1. 배열 : [요소 1, 요소 2, 요소 3, ...] 
	* 2. map : {'key':'value', 'key':'value', ...}
	* 3. 배열 + map : [{ }, { }, { }, ...]
	* 4. map + 배열 : {'key':[ ], 'key':[ ], 'key':[ ], ...}
	* 5. 배열 + 배열 : [[1행], [2행], [3행], ...]
	* 6. map + map : {'key':{ }, 'key':{ }, 'key':{ }, ...}
- 위 자료구조 중 4 번과 6 번을 많이 사용한다
- json 데이터를 요청하는 jsp 페이지에서는 모든 html 코드를 싹다 지운다.
- 즉, json 정보만 표현하기 위해 브라우저 소스 상에 찍히는 나머지 요소들은 다 지운다. 페이지로부터 json 문자열만 읽어오기 위함
- 물론 jQuery 를 이용해서 json 에 필요한 부분만 읽게 할수도 있으나, 그냥 지워버리는게 편하다
- json 요청 정보는 위 자료구조 중 하나를 택해서 일목요연하게 표현한다

guestbook_json.jsp
```JSP
<%@page import="com.myweb.guestbookBean.GuestBookVo"%>
<%@page import="java.util.List"%>
<%@page import="com.myweb.guestbookDao.GuestBookDaoImpl"%>
<%@page import="com.myweb.guestbookDao.GuestBookDao"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri='http://java.sun.com/jsp/jstl/core' prefix='c' %>

<%
	GuestBookDao dao = new GuestBookDaoImpl();
	List<GuestBookVo> list = dao.list("", 1);
	request.setAttribute("list", list);
%>
[
		{
			"serial"	:"${list[0].serial }",
			"id"		:"${list[0].id }",
			"content"	:"${list[0].content }",
			"mDate"		:"${list[0].mDate }"
		}
		
	<c:forEach var='d' begin='1' items='${list }'>
		, {
			"serial"	:"${d.serial }",
			"id"		:"${d.id }",
			"content"	:"${d.content }",
			"mDate"		:"${d.mDate }"
		}
	</c:forEach>
]
```

- 이 데이터는 브라우저 상에서 json 형식에 맞는 하나의 문자열로 표현된다

```
[ { "serial"	:"55", "id"	:"hong", "content"	:"하하하하", "mDate"	:"2019-01-28 14:26:25" } ,
{ "serial"	:"54", "id"	:"hong", "content" :"ppphhhhh", "mDate"	:"2019-01-28 13:39:47" } ,
{ "serial"	:"53", "id"	:"hong", "content"	:"ppp", "mDate"	:"2019-01-28 12:48:15" } ]
```

- 이것을 요청 페이지로 전송해 JSON.parse(문자열) 메서드로 파싱하고 배열 크기에 맞게 반복출력하면 된다

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
		var btnJson = get('btnJson');
		var xhr = new XMLHttpRequest();
		
		btnJson.onclick = function () {
			xhr.open('get', '/views/ajax/guestbook_json.jsp');
			xhr.send();
			xhr.onreadystatechange = function () {
				if (xhr.readyState == 4 && xhr.status == 200) {
					var data = xhr.responseText;
					var result = get('result');
					var list = JSON.parse(data);
					var str = '';
					
					for (var i = 0; i < list.length; i++) {
						str += '<ul>';
						str += '<li> Serial : ' + list[i].serial + '</li>';
						str += '<li> ID : ' + list[i].id + '</li>';
						str += '<li> Content : ' + list[i].content + '</li>';
						str += '<li> mDate : ' + list[i].mDate + '</li>';
						str += '</ul>'
					}
					
					result.innerHTML = str;
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
			<input type='button' value='JSON' id='btnJson' />
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




