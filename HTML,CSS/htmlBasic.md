
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190103-lightgray.svg?style=flat-square)

# HTML 개요

- 군사용 통신수단이었던 인터넷
- 인터넷 기반의 웹 (팀 버너스 리)
- 현재는 자바스크립트와 크롬이 대세?
- 웹 언어의 특성
	- 태그명
	- 엔터키 인식 불가 : <br/>
	- 두 타 이상의 빈 공간 인식 불가 : &nbsp;
	- 대소문자 구별하지 않음
	- 큰따옴표, 작은따옴표 구별하지 않음
	- 태그는 항상 쌍을 이루어야 함(start-end). 그러나 몇몇 예외는 있음
	- 꺽쇠 사용에 유의 : '<' 는 &lt, '>' 는 &gt
	- 주석 : <!-- -->, jsp 영역에서는 // 또는 /* */ >> html 주석은 페이지 소스로 확인 가능하므로 보안 면에서 좋지 않다. jsp 주석은 소스에서 보이지 않음


## 툴

- 비주얼 스튜디오
- 어도비
- 구글 크롬 개발자 도구
- 이클립스로도 가능

등등등...


## 태그

### 정의
	- <!DOCTYPE html> : html5 선언문
	- <html lang="ko"> : html 만 써도 되지만, 추후 도움말을 작성하려면 국가 언어 코드를 입력해야 한다
	- head : 현 페이지에 지정하는 설정 작성 (meta, title, link, style, script ...)
	- body : 실제 사용자에게 보여지는 부분

### 기본
	- h1 ~ h6 : 제목 태그, 화면상 폰트 사이즈의 차이가 있다
	- b, i, small, sub, sup, ins(u), del : bold, italic, 작은글자, 아래첨자, 윗첨자, 밑줄글자, 삭제표시
	- font : 폰트의 형식, 크기, 색상 등을 지정. css 에서 설정하므로 사용되지 않는 태그이다

### 단락
	- p : paragraph, 문단 태그, 단과 단 사이에 빈 공백을 만든다
	- div : 문단 태그이지만, 그냥 단순한 구분자로 봐도 된다. 문단 간 공백을 만들지 않으며 웹 문서에서 레이어 기능으로 가장 많이 사용됨

### 참조
	- a : anchor 태그, 페이지 링크를 위해 가장 많이 사용하는 태그 중의 하나이다
	- <a href="#alpha">Move to alpha</a><br/> : 페이지 내 id 가 alpha 인 위치로 이동한다
	- <a href='https://www.google.co.kr'>www.google.com</a><br/> : 구글 홈페이지로 이동
	- <a href='paragraph.jsp' target='_blank'>내 페이지</a><br/> : 무조건 새 페이지에 지정 페이지 표현
	- <a href='http://192.168.0.7:8888/WebTest/html/paragraph.jsp#test1' target='_blank'>다른 페이지의 특정 아이디로 이동</a><br/>

### 목록
	- ul, ol : unordered list (type = 'circle | square | disc' (대소문자 구분 x)), ordered list (type = 'A | 1 | I' (대소문자 구분 x), start = N)
	- li : list
	- dl, dt, dd : 정의목록, 정의용어, 정의설명

### 기타
	- 스타일 시트, Ruby 태그 ...

### 테이블
	- 과거 웹 페이지 레이아웃은 테이블로도 작성했지만, 현재는 절대 안 된다. 아무도 안 쓴다
	- 테이블 태그는 테이블을 작성할때만 사용하면 된다. 그러나 그마저도 테이블 태그를 사용하지 않는 사람이 많다
	- 사용하지 않는 이유? : 컨텐츠 위치 변경이 상당히 어렵고(불가하고), 화면 표현까지의 렌더링이 가장 느리다 >> 반응형 웹에 부적합하다
	- table, tr, th, td : 테이블, 행, 제목열, 내용열
	- border, bgcolor : 테두리. px 로 굵기 지정, td(th)-table-tr 우선순위에 따라 적용된다
	- 병합
		- 열 병합형 테이블 : colspan, 인접한 td 또는 th 끼리 병합한다. 병합은 칸 수로 지정한다
		- 행 병합형 테이블 : rowspan, 인접한 상하 td 또는 th 끼리 병합한다. 병합은 칸 수로 지정

	- width, height, align : 페이지를 기준으로 가로, 높이, 위치를 의미한다. 테이블의 위치는 페이지 전체에 대한 것이고, 각 칸에서의 위치는 칸 내부 내용들에 대한 것이다.
		- width 80% 에 align center 를 주면 가로 80 퍼 폭에 중앙에 위치하는 테이블이 됨
	- cellpadding, cellspacing	: 셀 내부간격, 셀 외부(셀 간 혹은 셀과 바깥테두리 간)간격
	- colgroup : 각 컬럼에 따른 세로 행 전체를 지정한다. 여기서 bgcolor 를 주면 해당 열 전체에 적용됨

### 이미지
	- img : <img src='../images/baby.png' alt='baby' title='baby'/>
	- 웹에서 이미지 파일을 다룰 때는, 경로지정이 중요하고, 원본 파일과 썸네일 파일 생성 작업도 중요하다
		- 파일 경로 설정하기 : 상대경로 혹은 절대경로로 지정한다. 나중에 배포 시 경로가 틀어질 수 있으므로 감안해서 지정해야 한다

	- placehold.it 사이트 : 페이지 시안에서 아직 정해지지 않은 이미지의 위치를 지정할 때 사용할 수 있음
		- <img src="http://placehold.it/300x200" />

	- css style 태그 적용하기
		- head 태그 내부에 다음과 같은 css 코드를 작성하고,

```HTML
<style>
	div#descLion {
		background-color: #E0E6F8;
	}
	img#lion {
		float: left;
		height: 200px;
	}
</style>
```

		- body 태그 내부에 실제 img 와 div 를 적용하면

```HTML
<p>
	<h3>이미지와 문장들의 배치관계</h3>
	<img id='lion' src='../images/lion.png'/>
	<div id='descLion'>
		<p>The standard Lorem Ipsum passage, used since the 1500s</p>
		There are many variations of passages of Lorem Ipsum available, but the majority have suffered alteration in some form, by injected humour, or randomised words which don't look even slightly believable. If you are going to use a passage of Lorem Ipsum, you need to be sure there isn't anything embarrassing hidden in the middle of text. All the Lorem Ipsum generators on the Internet tend to repeat predefined chunks as necessary, making this the first true generator on the Internet. It uses a dictionary of over 200 Latin words, combined with a handful of model sentence structures, to generate Lorem Ipsum which looks reasonable. The generated Lorem Ipsum is therefore always free from repetition, injected humour, or non-characteristic words etc.
		It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout. The point of using Lorem Ipsum is that it has a more-or-less normal distribution of letters, as opposed to using 'Content here, content here', making it look like readable English. Many desktop publishing packages and web page editors now use Lorem Ipsum as their default model text, and a search for 'lorem ipsum' will uncover many web sites still in their infancy. Various versions have evolved over the years, sometimes by accident, sometimes on purpose (injected humour and the like).
	</div>
</p>
```

		- 이미지는 왼쪽, 텍스트 div 는 오른쪽에 적절히 배치된다


## 입력 태그

### label

- 라벨로 감싼 모든 요소는 하나의 요소로 취급된다. 예를 들어 라디오 버튼과 그냥 일반문자를 라벨로 포함시키면 같은 라디오 버튼으로 취급됨

### form

- 다른 페이지로 정보를 전송하기 위한 태그. 이 태그 내에서 입력된 데이터를 지정된 페이지로 전송한다.
- 텍스트박스에 id, pwd, name 을 입력받아 form_result.jsp 페이지로 전송하는 부분

```HTML
<form action='form_result.jsp' method='post' name='frm'>
	<input type='text' name='id'/>
	<input type='text' name='pwd'/>
	<input type='text' name='name'/>
	<input type='submit' value='전송'/>
</form>
```

### input 태그의 type 속성

- text, password, submit, file, radio, checkbox, hidden, ...
- (HTML5) color, date, number, range, search, ...

### input 태그의 기타 속성

- name, value, placeholder, min, max, step, ...


<br/>등등 매우 많으니 그때그때 잘 활용해볼 것!



