
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190201-lightgray.svg?style=flat-square)

# 자바스크립트 에러 및 유의사항 모음

## 이벤트 함수

- 자바스크립트에서 이벤트를 핸들링하는 정해진 방법이 있다
- 다음 예시를 보자

```JAVASCRIPT

	// 1. 이벤트 핸들러 함수
	button.onclick = function () { execute(); }

	// 2. ???
	button.onclick = execute();

```

- 특정 이벤트에 대한 핸들링은 1 번 코드 문법과 같이 이벤트 핸들러 함수를 사용한다
- 2 번처럼 사용하면 그냥 execute() 함수의 결과를 button.onclick(?) 에 저장하는 단순문법이다
- 의도하는 바가 이벤트 핸들링이라면 1 번처럼 사용해야 한다




