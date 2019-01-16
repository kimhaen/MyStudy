
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190116-lightgray.svg?style=flat-square)

## HTML 텍스트 박스의 value 에 띄어쓰기를 포함한 문자열이 제대로 삽입되지 않는 경우?

```HTML
...
<%
	String str = "안녕 하세요, 반갑 습니 다";
%>

<div>
	<input type='text' value=<%=str %> />
</div>
...
```

위와 같은 상황에서 브라우저에 페이지 로드를 하면 텍스트 박스에 "안녕" 까지만 삽입된다
이때 value="[삽입 문자열]" 와 같이 삽입 문자열 자체를 따옴표로 감싸주면 잘 출력된다

```HTML
...
<%
	String str = "안녕 하세요, 반갑 습니 다";
%>

<div>
	<input type='text' value='<%=str %>' />
</div>
...
```

출력 결과

```
안녕 하세요, 반갑 습니 다
```