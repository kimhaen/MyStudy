
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190523-lightgray.svg?style=flat-square)

# Jinja2 기초와 Autoescaping

> Flask 는 확장 기능에 있어서 Jinja2 템플릿 엔진과 호환성이 좋으므로 가급적 Jinja2 를 함께 사용하는 것이 좋다.

> 그러나 Jinja2 의 autoescaping 설정과 summernote 에디터를 동시에 사용하면, 에디터로 입력한 정보가 이상하게 변환되는 현상이 발생했다. 물론 내가 이유를 몰라서 그런 거겠지만..

## Jinja2 기본설정

- 사용자가 임의로 설정하지 않는 이상 다음 설정이 존재한다.
    * Autoescaping(자동 변환) 기능이 모든 template 파일들(html, xml, xhtml 등)에 대해서 기본으로 활성화되어 있다.
    * 하나의 template 은 in/out 에 대한 autoescape 기능을 ```{% autoescape %}``` 태그를 활용해 제어할 수 있다.
    * Flask 는 기본적으로 Jinja2 context 를 통해서 전역 함수들과 헬퍼 함수들을 제공한다.

## Jinja2 의 기본 전역 변수 및 함수

- config : flask.config / 설정값을 가지고 있는 객체. imported templates 에서도 유효함.
- request : flask.request / 객체. 템플릿이 활성화된 컨텍스트에서 요청된 것이 아니라면 유효하지 않음.
- session : flask.session / 현 세션 객체. 똑같이 템플릿이 활성화된 컨텍스트에서만 유효.
- g : flask.g / request 에 한정되어진 전역 변수. 이것의 유효 컨텍스트도 마찬가지다.
- url_for(), get_flashed_messages() 등의 함수

## 표준 필터

- 필터는 '|' 기호와 함께 사용한다.
- tojson : JSON 표기법으로 주어진 객체를 변환시켜주는 필터이다.
- safe : 자동 활성화된 **autoescaping** 옵션을 안전하게? 비활성화시키는 필터이다.
- 사용자 지정 필터를 만들 수도 있음. 추후 공부해볼 것.

## HTML Escaping

- HTML 코드 중 '<', '>', '&', '"' 등의 특정한 특수문자들은 escape (변환) 될 필요가 있는데,
- 이는 코드 in/out 시 예기치 못한 동작결과를 불러올 수 있기 때문이다.
- 각 언어에서 정상적인 코드 동작을 위해 역슬래시(\\) 를 활용해 특수문자들을 escaping 해야만 하는 이유와 일맥상통하다.
- 나아가 safety escaping 이 이루어지지 않는다면 **xss** 와 같은 보안취약점 공격에 노출될 수 있다.

> 이하는 [Jinja2-Template Designer Document](http://jinja.pocoo.org/docs/2.10/templates/#working-with-automatic-escaping) 번역이다.

- 템플릿으로부터 HTML 을 생성할 때, HTML 자체에 **영향을 미칠 수 있는 문자**가 변수에 포함되어 있을 수 있다.
- jinja 는 수동으로 각 변수를 변환하거나, 자동으로 모든 변수를 변환하도록(by default) 할 수 있는데,
- 무엇이 사용될지는 application configuration 에 달려 있다.
- 기본 환경은 ```no automatic escaping``` 상태인데, 그 이유는
    * Jinja 가 HTML 에 포함되지 않는 것으로 알려진 변수들조차도 변환함으로써 엄청난 성능저하를 불러올 수 있고
    * 변수의 안정성과 관련한 정보는 (escaping 과정에서) 매우 깨지기 쉽기 때문이다. 결국, 특정 변수가 안전-비안전 상태로 double-escaped 될수 있다.
- **Manual Escaping** 하기 (수동 변환)
    * 
- **Automatic Escaping** 하기 (자동 변환)
    * 

## Autoescaping 과 summernote 에디터 (개인적인 요약)

- Autoescaping 설정은 자동으로 특수 문자들을 변환시키는 옵션이다. 이것이 활성화되어 있다고 가정할 때,
- 서버로부터 받아온 HTML 템플릿 자체에 포함된 특수문자는 HTML 코드 자체이므로 자동 변환할 일이 전혀 없지만(어차피 로드됨),
- jinja2 엔진에 의해 명시적으로 렌더링 되는 특정 데이터에 포함된 특수문자는 자동으로 escaping 함으로써 예기치 못한 동작이 일어나지 않도록 할 수 있다.
- 만약 autoescaping 이 비활성화 상태이거나, 어쨌든 escaping 이 일어나지 않는 환경에서는 이러한 특수문자들은 그대로 투입되었다가 그대로 출력된다.
- xss 공격과의 관계
    * xss(Cross-Site Scripting) 공격은 해커가 공격 목적으로 특정 사이트의 게시글을 작성할 때,
    * 사용자로써 입력 가능한 text 나 textarea 필드 등에 특정 스크립트 코드를 삽입함으로써
    * 해당 글을 열람하는 모든 사용자의 브라우저해서 삽입한 스크립트가 실행되도록 하는 웹사이트 취약점 공격이다.
    * 이는 스크립트를 구성하는 특수문자들이 **escaping** 되지 않은 채 그대로 in/out 되기 때문에 발생하는데,
    * autoescaping 이 활성화 상태라면 이러한 특수문자들이 in/out 시 자동으로 escaping 됨으로써 임의로 삽입한 스크립트가 제대로 실행되지 않게 된다.
    * 그 결과 더 이상 스크립트 코드가 아니라, 사용자가 입력한 일반적인 게시글이 된다.
- 결국, autoescaping 설정이 ensure 상태일 때에는 사용자가 입력하는 모든 특수문자들은 자동으로 escaping 되어 디비에 저장되었다가 다시 변환되어 출력된다.
- summernote 에디터와의 관계
    * 다음은 에디터 자체에 관한 설명이다.
    * 에디터를 사용하는 이유는, 게시글을 등록하는 일반 사용자는 HTML 코드 문법을 모르기 때문에 자동으로 개행, 강조 등의 코드들을 입혀줘야 하기 때문이다.
    * 이렇게 자동으로 입혀진 코드들은 앞서 언급한 xss 공격의 수단이 될 수 없으므로(임의로 삽입할 수 없음), 에디터 내부적으로 별도의 escaping 이 진행되지 않는다.
    * 그러나 자동으로 입혀지는 코드를 제외한 사용자가 입력하는 메인 정보들은 에디터 내부적으로 자동 escaping 한다.
    * 이는 역시 xss 취약점 공격을 대비하기 위함으로 보이는데,
    * summernote 에디터 상에서 사용자가 임의로 입력하는 특수문자들은 에디터가 자동으로 escaping 하여 서버로 전송한다.
- Autoescaping 설정과 editor 와의 관계
    * 공통적으로 xss 와 같은 공격에 대비하기 위해 autoescaping 과 editor 모두는 각각 자동으로 escaping 을 수행한다.
    * 이 둘을 함께 사용한다는 것은 결과적으로 사용자가 입력한 특수문자를 두 번 중복으로 escaping 함을 의미한다.
    * 또한 summernote 에디터에 의해 자동으로 입혀지는 코드들은 jinja 입장에서는 사용자가 삽입한 특수문자로 판단되어 한 번의 escaping 이 수행된다.
    * 결과적으로 jinja 의 autoescaping 설정에 의해서 사용자 입력 메인 정보는 두 번의, 에디터 자체적인 자동 코드들은 한 번의 escaping 이 일어나게 되는데,
    * 해당 게시글 정보는 xss 공격에는 안전할지 모르나, 모든 특수문자들이 escaping 된 상태에서 아무런 기능도 하지 못하고 그대로 출력되는 것을 볼 수 있다.
    * 결국 escaping 은 전체적으로 한 번만 수행되는 것이 타당하므로, 에디터를 사용해야하는 부분에서는 autoescaping 옵션을 꺼야 한다.

## 정리, 활용

- 정리하자면, 정상적인 html 코드 동작과 xss 와 같은 공격 대비를 위해 특수문자에 대한 escaping 은 한 회 필요하다.
- jinja2 의 autoescaping 옵션과 summernote 에디터는 각각 자동 변환 기능을 수행하므로 둘을 같이 사용할 경우에는 중복 escaping 문제가 발생한다.
- 최상의 방법은 에디터 외적인 보안을 위해 전체적인 autoescaping 은 활성화해 놓고,
- 에디터를 사용하는 특정 부분에 대해서만 HTML 문자열의 특수문자들을 보존하거나 autoescaping 을 비활성화하는 것이다. (에디터 자체 escaping 기능 활용)
- 문제해결을 위한 방법에는 총 세 가지가 있다.
    * python 코드 단에서 특수문자가 포함된 HTML 문자열을 **Markup** 객체로 wrapping 하는 방법. 권장되는 방법. (추후 살펴볼 것)
    * 특수문자를 보존하고자 하는 HTML 문자열에 대해서만 **safe** 필터 적용하기. 현재 사용 방법. (ex. {{ result.content|safe }})
    * autoescaping 자체를 비활성화하기. 이를 위해서는 {% autoescape false %}{% endautoescape %} 블럭을 사용한다. 권장되지 않음.




