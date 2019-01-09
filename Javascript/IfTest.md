
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190109-lightgray.svg?style=flat-square)


### 주의할 점

- 자바스크립트는 인터프리터 언어이기 때문에 스크립트의 선후관계가 중요하다.<br/>
즉, 한 라인씩 해석되고 실행되기 때문에 객체가 생성되기도 전에 해당 객체를 호출하는 스크립트를 짠다면 NullPointer 에러가 발생한다
- 이전에는 클라이언트 단에서 생성된 정보를 정제되지 않은 채 서버 단으로 전송해 처리했지만, 현재 웬만한 처리는 클라이언트에서 모두 수행하고 그 결과값만 서버로 전송한다.<br/>
특별한 보안상의 이유가 없다면, 혹은 적절히 통제된 상황이라면 발달된 클라이언트 컴퓨터 자원을 사용하지 않을 이유가 없기 때문이다.

# 조건문

> 자바의 조건문과 거의 흡사하다. (switch, 반복문도 마찬가지) 다만 HTML, CSS 와 복합적으로 사용해야 하므로 흐름을 고려해야 한다

## 조건문 실습예제


**바디 부분**
```HTML
...
<body>

	<div id='ifTest03'>
		<h1>조건문을 연습합니다</h1>
		<h2>이름, 키, 몸무게를 입력하세요</h2>
		
		<input type='text' id='name' placeholder='이름 입력...' /><br/>
		<input type='text' id='height' placeholder='키 입력...' /><br/>
		<input type='text' id='weight' placeholder='몸무게 입력...' /><br/>
		<input type='button' id='btn01' value='결과' /><br/><br/>
		<div id='result01'>
			결과 : 
		</div>
		
		<br/><br/>
		
		<h2>국영수 점수를 입력하세요</h2>
		
		<input type='text' id='kor' size='10' placeholder='국어 점수 입력...' /><br/>
		<input type='text' id='eng' size='10' placeholder='영어 점수 입력...' /><br/>
		<input type='text' id='mat' size='10' placeholder='수학 점수 입력...' /><br/>
		<button type='button' id='btn02'>결과</button><br/><br/>
		<input type='text' id='result02'  size='25' placeholder=' >> ' disabled/><br/>
		<div id='hapbul'>
			결과 : 
		</div>
		
		<br/><br/>
		
		<div id='content'>
			<h1>Login</h1><br/>
			<div id='container'>
				<input type='text' id='id' placeholder='Plz input ID...' /><br/>
				<input type='text' id='pwd' placeholder='Plz input Password...' /><br/>
				<img src='../images/id.png' id='imgID' />
				<img src='../images/pwd.png' id='imgPWD' />
			</div><br/>
			<button type='button' id='btnAlert'>login</button><br/>
		</div>
		
		<br/><br/>
		
		<div id='getPrice'>
			<h1>단가 계산</h1>
			
			<label>제품명: </label>
			<select id='select' onChange='onChange01()'>
				<option value='' selected disabled>제품 선택</option>
				<option value='shoose'>신발</option>
				<option value='bag'>가방</option>
				<option value='watch'>시계</option>
			</select><br/>
			<label>단가: </label>
			<input type='text' id='price' disabled/><br/>
			<!-- <input type='text' id='price999' value='20000' readonly/><br/> -->
			<label>수량: </label>
			<input type='text' id='number' /><span> << 제품 선택 후 수량을 입력하세요</span><br/><br/>
			<button type='button' id='btn03'>결과</button><br/><br/>
			<label>금액: </label>
			<input type='text' id='total' disabled/><br/>
			<label>기타: </label>
			<input type='text' id='gift' disabled/><br/><br/>
			<label>결과: </label>
			<textarea rows='10' cols='40' id='result03' ></textarea>
		</div>
		
	</div>

</body>
```


**스크립트 부분**
```JAVASCRIPT
...
<script>
	window.onload = function () { // 대소문자 구분 함. 주의할 것!
		/*
		* 이름, 키, 몸무게 - div 출력
		*/
		var name = document.getElementById('name');
		var height = document.getElementById('height');
		var weight = document.getElementById('weight');
		var result01 = document.getElementById('result01');
		var btn01 = document.getElementById('btn01');
		
		btn01.onclick = function () {
			if (Number(height.value) > 200 || Number(weight.value) > 150) {
				result01.innerHTML = '결과 : ' + name.value + ' 님은 탑승 불가';
			} else {
				result01.innerHTML = '결과 : ' + name.value + ' 님은 탑승 가능';
			}
		}
		
		/*
		* 국영수 - input 출력
		*/
		var kor = document.getElementById('kor');
		var eng = document.getElementById('eng');
		var mat = document.getElementById('mat');
		var sum; var avg;
		var result02 = document.getElementById('result02');
		var hapbul = document.getElementById('hapbul');
		var btn02 = document.getElementById('btn02');
		
		btn02.onclick = function () {
			sum = Number(kor.value) + Number(eng.value) + Number(mat.value);
			// avg = parseInt(sum / 3);
			// avg = Math.round(sum / 3);
			avg = sum / 3;
			avg = avg.toFixed(2);
			result02.value = ' >> 총점 : ' + sum + ' / 평균 : ' + avg;
			
			// 총점이 60 이상이고 과락이 없는 경우 합격
			if (avg >= 60 && Number(kor.value) >= 40 && Number(eng.value) >= 40 && Number(mat.value) >= 40) {
				hapbul.innerHTML = '결과 : 합격입니다!'
			} else {
				hapbul.innerHTML = '결과 : 불합격입니다'
			}
		}
		
		/*
		* 로그인 - alert 출력
		*/
		var id = document.getElementById('id');
		var pwd = document.getElementById('pwd');
		var btnAlert = document.getElementById('btnAlert');
		
		btnAlert.onclick = function () {
			if (id.value == 'hong' && pwd.value == '1111') {
				alert('로그인 되었습니다');
			} else {
				alert('아이디나 암호를 확인하세요');
			}
		}
		
		/*
		* 판매 - textarea 출력
		*/
		var selectedValue = document.getElementById('select');
		var price = document.getElementById('price');
		var number = document.getElementById('number');
		var total = document.getElementById('total');
		var gift = document.getElementById('gift');
		var result03 = document.getElementById('result03');
		var btn03 = document.getElementById('btn03');
		
		
		
		btn03.onclick = function () {
			total.value = Number(price.value) * Number(number.value);
			if (Number(total.value) >= 10000) {
				gift.value = '사은품 증정';
			} else {
				gift.value = '사은품 없음. 잘 가세요~';
			}
			result03.value = '제품명 : ' + select.value + '\n'
							+ '단가 : ' + price.value + '\n'
							+ '수량 : ' + number.value + '\n'
							+ '금액 : ' + total.value + '\n'
							+ '기타 : ' + gift.value + '\n';
		}
	}
	
	function onChange01 () {
		selectedValue = document.getElementById('select').options[document.getElementById('select').selectedIndex].value;
		if (selectedValue == 'shoose') {
			price.value = 3000;
		} else if (selectedValue == 'bag') {
			price.value = 5000;
		} else if (selectedValue == 'watch') {
			price.value = 7500;
		}
	}
</script>
...
```


**스타일 부분**
```CSS
<style>
	#ifTest03 {
		
	}
	
	/*
	* login
	*/
	#ifTest03 #content {
		width: 100%;
		background-color: #32444c;
		border-radius: 10px;
		text-align: center;
		padding: 80px 0 150px 0;
		font-family: 'Anton';
	}
	#ifTest03 #content > * {
		display: inline-block;
	}
	#ifTest03 #content #container {
		width: 250px;
		height: 100px;
		position: relative;
	}
	#ifTest03 #content #container input[type=text] {
		width: 200px;
		background-color: #455d77;
		padding: 15px 3px 15px 35px;
		margin-bottom: 5px;
		color: black;
		border: none;
		border-radius: 5px;
	}
	#ifTest03 #content #container img {
		width: 15px;
		height: 15px;
		position: absolute;
	}
	#ifTest03 #content #container #imgID {
		left: 17px;
		top: 16px;
	}
	#ifTest03 #content #container img {
		left: 17px;
		top: 65px;
	}
	#ifTest03 #content button[type=button] {
		background: #aaaaaa;
		padding: 5px 20px 5px 20px;
		margin-top: 20px;
		border: none;
		border-radius: 5px;
	}
	
	/*
	* getPrice
	*/
	#ifTest03 #getPrice {
		font-family: 'Song Myung';
		background-color: #575b79;
		border-radius: 10px;
		padding: 10px 0 30px 20px;
	}
</style>
```

<br/><br/>
**결과화면**

![JS01](https://github.com/daesungRa/MyStudy/blob/master/imgs/JS01.PNG)
![JS02](https://github.com/daesungRa/MyStudy/blob/master/imgs/JS02.PNG)
![JS03](https://github.com/daesungRa/MyStudy/blob/master/imgs/JS03.PNG)













