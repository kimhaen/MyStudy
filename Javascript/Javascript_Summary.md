![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190107-lightgray.svg?style=flat-square)

# 자바스크립트 시작!

참조 : [w3schools 자바스크립트](https://www.w3schools.com/js/default.asp)

* 공부방식

- w3schools 자바스크립트 과정을 번역하는 방식

### 개요

> 자바스크립트는 HTML 과 웹의 프로그래밍 언어입니다.</br>
자바스크립트는 배우기 쉽습니다.<br/>이 튜토리얼은 기초부터 발전까지 자바스크립트를 가르칩니다.

### 왜 자바스크립트를 배워야 할까요?

- 자바스크립트는 모든 웹 개발자가 꼭 배워야 할 세 가지 언어 중의 하나입니다. (HTML, CSS, JavaScript)
- HTML 은 웹 페이지의 콘텐츠를 정의합니다.
- CSS 는 웹 페이지의 레이아웃을 특정합니다.
- JavaScript 는 웹 페이지의 동작을 프로그래밍 합니다.

웹 페이지는 자바스크립트가 사용되는 유일한 플랫폼이 아닙니다. 많은 데스크탑, 서버 프로그램에서 자바스크립트가 사용됩니다. Node.js 가 가장 널리 알려져 있습니다.
MongoDB 와 CouchDB 같은 몇몇 데이터베이스에서 또한 자바스크립트가 프로그래밍 언어로써 사용됩니다.

### 예제로 학습하세요

- 우리의 "Try it Yourself!" 에디터로 모든 예제와 결과 화면을 변경할 수 있습니다.
- 예제를 통한 학습은 1000 단어를 읽는 것보다 낫습니다.
- 또한 설명들을 더욱 이해하기 쉽도록 합니다.
- 이 튜토리얼은 "Try it Yourself!" 예제들로 모든 설명들을 명확하게 보충합니다.
- 만약 당신이 모든 예제들을 작성해 본다면, 자바스크립트에 대한 아주 많은 것들을 단시간에 학습할 것입니다.

### 알고 있나요?

자바스크립트와 자바는 컨셉이나 디자인 면에서 완전히 서로 다른 언어입니다. 자바스크립트는 1995 년 Brendan Eich 에 의해 발명되었고, 1997 년에 ECMA 표준으로 채택되었습니다.
ECMA-262 는 표준의 공식 이름입니다. ECMAScript 는 자바스크립트 언어의 공식 이름입니다.

### 학습 속도

이 튜토리얼에서 학습 속도는 당신의 선택입니다. 모든 것은 당신에게 달렸습니다.<br/>
만약 어렵다면, 잠시 쉬었다가 다시 읽어 보십시오. 그리고 항상 "Try-it-Yourself" 예제들을 확실히 이해하도록 하십시오.

### 자바스크립트 레퍼런스

w3schools 는 모든 HTML 과 브라우저 객체들을 포함한 자바스크립트 레퍼런스를 완벽히 유지보수 합니다.<br/>
레퍼런스는 모든 정보, 방식, 이벤트의 예제를 포함하고, 최신 웹 표준에 따라 지속적으로 업데이트 됩니다.

[자바스크립트 레퍼런스](https://www.w3schools.com/jsref/)

### 자바스크립트 연습과 퀴즈

[자바스크립트 연습](https://www.w3schools.com/js/exercise_js.asp?filename=exercise_js_variables1)
[자바스크립트 퀴즈](https://www.w3schools.com/js/js_quiz.asp)

-------------------------------------

# 자바스크립트 소개

> 이 장에서는 자바스크립트가 할 수 있는 것들에 대한 예제를 설명합니다.

### 자바스크립트는 HTML 문장을 변경할 수 있습니다.

이를 위해 자주 사용되는 자바스크립트 HTML 메서드 중의 하나는 <strong>getElementById()</strong> 입니다.

```JAVASCRIPT
<script>
	<p id="demo">JavaScript can change HTML content.</p>

	<button type="button" onclick='document.getElementById("demo").innerHTML = "Hello JavaScript!"'>Click Me!</button>
</script>
```

이 코드는 ID 가 demo 인 컴포넌트의 내부 HTML 텍스트를 Hello JavaScript! 로 변경하는 예제입니다.
또한, 큰 따옴표가 아닌 작은 따옴표의 사용도 허용합니다.

### 자바스크립트는 HTML 속성값 변경도 가능합니다.

이 예제는 img 태그의 src 속성값을 변경합니다.


```JAVASCRIPT
<script>
	<button onclick="document.getElementById("myImage").src = 'pic_bulbon.gif'">Turn on the light</button>

	<img id="myImage" src="pic_bulboff.gif" style="width:100px;">

	<button onclick="document.getElementById("myImage").src = 'pic_bulboff.gif'">Turn off the light</button>
</script>
```

이 예제에서는 자바스크립트가 적용된 버튼을 클릭할 때마다 ID 가 myImage 인 img 태그의 src 속성값을 변경합니다.

### 자바스크립트는 HTML 스타일 (CSS) 변경 가능합니다.

HTML 요소 스타일의 변경은 HTML 속성에 여러 가지 영향을 미칩니다

```JAVASCRIPT
<p id='demo'>JavaScript can change the style of an HTML element.</p>

<button type='button' onclick='document.getElementById("demo").style.fontSize="35px"'>Click Me!</button>
```

### 자바스크립트는 HTML 요소들을 숨깁니다.

display 스타일을 변경함으로써 HTML 요소들을 숨길 수 있습니다.

~.style.display = 'none';

### 자바스크립트는 숨겨진 요소들을 보여줄 수 있습니다.

~.style.display = 'block';














