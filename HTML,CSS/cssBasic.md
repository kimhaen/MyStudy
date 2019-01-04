
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190104-lightgray.svg?style=flat-square)





# HTML 이어서

## 기본 태그

- textarea

```HTML
<textarea rows="5" cols="50">hihi~</textarea><br/>
```

- select, optgroup, option

	- 속성 multiple
	- optgroup, option ~
	- 이것은 잘 사용하지 않음. 웹 페이지에 어울리지 않기 때문
	- 일반적으로 select 와 같은 기능은 자바스크립트를 이용해 하나하나 만든다

- fieldset, legend

## 공간 분할 태그

- div, span, p

	- div 는 특정 공간을 차지하고, span 은 인라인 형식으로 쫙 나열된다(사실상 분할의 의미만 있음). p 태그는 문단 단위로 나눈다(상호간 공백 발생)
	- 특히 div 는 레이어 형식으로 각각 CSS 를 씌우며 작업할 때 많이 사용된다

## 의미론적 태그(Sementic Tag)

- 이것 자체로는 페이지 상의 효과를 나타내지는 못하지만,
- 이 태그들로 인해 각각의 컴포넌트가 특정한 의미를 내포하게 된다
- 이것은 브라우저가 코드를 해석할 때 모호하지 않도록 가이드를 제시한다
- header, nav, aside, section, article, footer




# CSS 선택자

> CSS 에서 가장 중요한 것은 선택자이다. 아무리 아름다운 디자인을 적용하고 싶어도 화면의 수많은 컴포넌트 중 디자인 적용 대상을 정확하게 짚어내지 못하면 소용 없기 때문이다.

- CSS 의 여러 속성들도 정확하게 이해하고 있어야 하겠지만,
- 그룹과 id, class 등의 선택자를 적절하게 사용하여 여러 계층의 레이아웃 중 정확한 대상을 골라내야 한다

## 대표적인 선택자 종류

- * : 전체 선택자
- 태그선택자 : body, h1, div 등 일반 태그를 지칭
- id : 특정 태그(하나만!)에 부여하여 그것만 지칭
- class : class 가 적용된 태그들의 그룹을 지정하여 해당 그룹 모두를 지칭
- 후손 선택자 : [선택자] [후손 선택자] { ... } >> 후손 선택자 내의 모든 동종(!) 컴포넌트들에 다 적용된다
- 자손 선택자 : [선택자] > [자손 선택자] >> 명시된 자손 선택자 하나만 적용된다 (직계자손만)
- 링크 선택자 : [:link] >> 특히, <strong>a:link</strong> 선택자를 많이 사용한다. 일반적인 링크는 밑줄이 보이는 등 보기 좋지 않은데, a:link 를 지정하고 css 를 입혀 조정할 수 있다
- 반응 선택자 : [선택자]:active, [선택자]:hover 등... >> active 는 선택 시, hover 는 마우스를 올리면 반응한다
- 그 외 수많은 선택자들이 있다

#### id 중복의 문제

스타일시트 상 id 중복은 크게 문제될것 없어 보이지만, 자바스크립트 상에서는 문제가 발생하므로 id 의 유일성은 지켜줘야 한다

## CSS 선택자 우선순위

<strong>태그에 직접적용 > id > class > 기본선택자</strong> 순이다. 전체 선택자의 우선순위가 가장 낮다

## CSS 적용 방법

- 외부 파일에 CSS 를 작성한 뒤 @import 또는 link 태그를 사용하여 적용한다 (가장 많이 사용하는 방식)
- 페이지의 head 태그 내 style 태그에 작성한다
- 특정 태그 자체에 style 속성을 직접 지정할 수도 있지만 이것은 그닥..

## 예시

```HTML
<style type="text/css">
	h1 {
		color: #34ff82;
	}
	h1.jemok {
		color: #827272;
	}
	h1#subject {
		color: #872532;
	}
	div#title {
		background-color: #E0E0F8;
		border: 2px solid #aaaaff;
		border-radius: 10px;
		font-weight: bold;
		color: #63717f;
		box-sizing: border-box;
		padding-left: 20px;
		padding-top: 10px;
		margin: 10px auto;
		box-shadow: 10px 10px 10px #aaaaff;
	}
</style>
```

결과는 다음과 같다

![css result](https://github.com/daesungRa/MyStudy/blob/master/imgs/cssEx01.PNG)

























