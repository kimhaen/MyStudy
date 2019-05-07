![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190503-lightgray.svg?style=flat-square)

# Flask overview

## Flask and Django

- Flask 는 파이썬 기반의 경량 웹 프레임워크이다.
- 같은 파이썬 기반의 Django framework 도 존재한다.
- 플라스크는 최소한의 뼈대로부터 출발하는 경량이면서 자유도 높은 모듈이고, 쟝고는 기본적인 구조가 갖춰진 상대적으로 자유도가 낮은 모듈이다.
- 둘은 장단점이 있으니, 필요에 따라 다르게 사용하면 된다.

## 개요

- 플라스크는 Werkzeug WSGI(Web Server Gateway Interface) 툴킷과 Jinja2 template engine 으로 구성된다(python 기반).
- 벡자이크는 웹서버가 웹앱과 상호작용하는 방식 및 request 에 따른 처리과정을 정의하는 인터페이스이며,
- Jinja2 는 html, css 등과 사용되는 템플릿 엔진이다. jstl 과 같이 간결한 문법으로 작성 가능하며, flask 기반으로 운용된다. 물론 독립적으로도 사용 가능하다.

## Flask 프로젝트 기본 구조

- 완벽하게 정해진 구조는 없지만, 일반적으로 다음과 같은 형태로 많이 사용한다.

![플라스크 프로젝트 기본구조](https://github.com/daesungRa/MyStudy/blob/master/imgs/python/flaskstructure01.JPG)

- 루트 프로젝트 디렉토리 하위에 웹앱 구동파일(flask/hello.py)과 static, templates 디렉토리가 위치한다.
- 웹앱 구동파일은 프로젝트 외부에 둬도 되는데, 이 때는 해당 패키지에 대한 __init__.py 파일을 만들어 모듈 방식으로 import 하도록 한다. (import 한 모듈로부터 원하는 어플리케이션 객체를 얻어와 run 하는 방식으로 사용).
- static 에는 각종 모듈 및 라이브러리가 존재하고, templates 에는 정적 템플릿 파일들(html 등)이 존재한다.
- venv 가상환경은 포함시켜도 되고 아니면 각 로컬에서(협업한다면) 개별적으로 구성해서 활용해도 된다(호환성만 맞는다면).
- MVC 프로젝트라면 루트 경로 하위에 controller, model 등을 두면 되고, 규모가 커짐에 따라 리소스 파일(db, 환경변수 등)이 필요하다면 별도로 디렉토리를 만들어 일관성 있게 활용하면 된다. (그만큼 자유도가 높다)
- 그 외 필요한 구성에 대해서는 팀별 약속에 따라 만들어가면 된다.

# Flask application 환경설정

```python
from flask import Flask

app = Flask(__name__)
# app 에 대한 환경 설정 추가
## 직접 추가하기(dictionary 형식)
## 외부 설정파일로 추가하기

if __name__ == '__main__':
	app.run()
```

- 일반적인 flask app 은 위와 같은 방식으로 인스턴스 생성 후 구동된다.
- 주석 부분의 설명처럼, app 에는 디버그 모드, 테스트 모드, 비밀 키 등 여러 종류의 환경설정을 추가할 수 있다.

이하 참조 : [flask-docs](https://flask-docs-kr.readthedocs.io/ko/latest/config.html)

## 설정 다루기

- 설정값들은 하드코딩(작은 규모의 앱) 혹은 설정 객체(config object)를 사용함으로써 다룰 수 있다.
- 후자의 경우는 Flask 객체의 config 속성을 컨트롤하는 방식으로 가능하다.
- 이 객체를 통해 특정 설정값을 저장할 수 있고, Flask 확장 플러그인들도 자신의 설정값을 저장할 수 있다.
- 내가 만든 application 의 설정값들도 마찬가지이다.

## 설정 기초연습

- config 는 실제로 dictionary 의 서브클래스이며, 다른 dict 처럼 다음과 같이 수정 가능하다.

```python
app = Flask(__name__)
app.config['DEBUG'] = True
```

- 확정된 설정값들은 또한 Flask 객체로 전달될 수 있으며, 그 객체를 통해 설정값들을 읽거나 쓸 수 있다.

```python
app.debug = True
```

- 한 번에 다수의 키(key)들을 업데이트하기 위해서는 dict.update() 함수를 사용할 수 있다.

```python
app.config.update(
	DEBUG=True,
	SECRET_KEY='...'
	...
)
```

## 내장된 고유 설정값들

- DEBUG, TESTING, SECRET_KEY, SESSION_COOKIE_NAME, SESSION_COOKIE_PATH, LOGGER_NAME, SERVER_NAME, APPLICATION_ROOT 등등
- 설정 키(key)는 오직 대문자로만 구성되어야 한다.
- 다음을 참조한다
[flask 내장 설정값](https://flask-docs-kr.readthedocs.io/ko/latest/config.html#id3)

## 외부 파일을 통하여 설정하기

- app 의 모든 설정정보를 외부 파일에 모아 관리하면 더 유용할 것이다. 사후 유지보수 측면에서도 유리하다.

```python
app = Flask(__name__)
app.config.from_object('yourapplication.default_settings')
app.config.from_envvar('YOURAPPLICATION_SETTINGS')
```

- 'yourapplication.default_settings' 는 파이썬 오브젝트 모듈이고, 'YOURAPPLICATION_SETTNGS' 파일의 내용으로 덮어씌운다.






