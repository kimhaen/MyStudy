![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190503-lightgray.svg?style=flat-square)

# 플라스크 application 환경설정

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

** 이하 참조 : [flask-docs](https://flask-docs-kr.readthedocs.io/ko/latest/config.html) **

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






