
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190508-lightgray.svg?style=flat-square)

# Flask - PyMongo - MongoDB 프로젝트 예제 분석

> [flask 예제 프로젝트 - fork](https://github.com/daesungRa/flask)

## 구조

- 루트 경로는 **flask/**
- **flask/hello.py**
    * 기본 웹앱 구동 모듈 (**flask/flask_template.py** 는 임시 테스트용인듯?)
- __flask/venv/*__
    * 가상환경 포함
- **flask/static/dist**
    * **summernote** 관련 api 나 스크립트, css 등 포함
- **flask/templates**
    * 각종 템플릿 파일 포함 (html)

## hello.py

<a href='https://github.com/daesungRa/flask/blob/master/hello.py' target='_blank'>코드 보기</a>

- import
    * **flask** : Flask, request, session, render_template, redirect, url_for, abort, make_response, request
    * **bson.objectid** : **ObjectId**
        - bson 은 몽고디비에 저장 시 변환되는 바이너리 파일 포맷
        - ObjectId 는 몽고디비에 저장되는 각 document 의 unique 아이디인 **_id** field 데이터를 변환하는 함수.
        - **_id** 필드는 별도 입력 필드값이 없다면 자동 생성된다.
    * **pymongo** : **MongoClient**
        - pymongo 는 몽고디비 커넥션 클라이언트 인스턴스를 생성하는 파이썬 기반 모듈
        - MongoClient 는 클라이언트 인스턴스를 생성하는 클래스
        - MongoClient 의 인자로 ```mongodb://[몽고디비 주소]:27017``` 혹은 ```mongodb://[아이디]:[비밀번호]@[몽고디비 주소]:27017``` 를 넘겨준다.
- app = Flask(\_\_name\_\_)
    * Flask 모듈을 통해 application 객체를 얻어온다.
    * **\_\_name__**
        - 파이썬은 인터프리터 언어로, 별도의 main 메서드 없이 level 0 코드(들여쓰기 없는 코드)를 순차적으로 실행해 나간다.
        - 내장 변수의 하나인 **\_\_name__** 은 현재 실행되는 **.py** 스크립트가 직접 실행되는 경우 **\_\_main__** 으로 설정된다.
- @app.route('경로', methods=['메서드 액션 타입'])
    * @app.route 어노테이션은 브라우저 request 요청 시 요청 url 을 매핑해주는 역할을 한다.
    * 예를 들어 브라우저에서 ```localhost:5000/hello``` url 요청을 **GET** 방식으로 보냈다면, **'/hello'** 경로와 **methods=['GET']** 타입이 매핑된 메서드가 실행될 것이다.
    * 만약 uri 중에 **~/<매개변수>** 타입의 정보가 포함되어 있다면, 사용자 입력에 따른 동적 매개변수가 url 과 함께 전송된다.
    * 이러한 매개변수 앞에 데이터 타입을 명시할 수도 있다. (ex. **~/<int: userId>**)
- 메서드 액션 타입 : GET, POST
    * GET 은 url 로만 요청받아 header 정보 내에 매개변수 및 요청정보가 포함되는 방식이고,
    * POST 는 header 로는 경로만 지정하고 body 부분에 동적 정보를 담아 요청하는 방식이다.
    * 일반적으로 GET 은 select 의 역할, post 는 insert 의 역할을 한다.
    * 그 외에 DELETE (삭제), PATCH (수정) 등의 메서드도 존재한다.
    * 웹앱에서는 이 요청 메서드 방식에 따라서 같은 함수 내에서 로직을 분기시키기도 한다.
        - 예를 들어, login 매핑에 GET 방식의 요청이 들어오면 login.html 템플릿을 반환하고, login 매핑에 POST 방식의 요청이 들어오면 login 로직을 수행한다.
- def hello_world(): ~
    * @app.route 에 의해 매핑된 **hello_world** 메서드이다. 요청 시 해당 내용이 실행된다.
- app.logger
    * [flask docs](http://flask.pocoo.org/docs/1.0/quickstart/#logging) 를 인용한다.
    
    > &nbsp;때때로 정확성을 요하는 데이터를 다뤄야 할 경우가 있는데, 대개는 지켜지지 않는다. 예를 들어 서버로 HTTP request 를 전송하는 client-side 코드가 있는데 그것이 명백하게 기형이었다.
    이것은 사용자가 임의로 조작한 데이터 혹은 client code failing 때문이었다. 대부분의 상황에서는 '400 Bad Request' 를 응답함으로써 별다른 문제가 없었지만,
    가끔씩 해당 응답이 이루어지지 않은 채 코드가 그대로 진행되어 버리기도 했다.<br/> &nbsp;이때 당신은 뭔가 냄새나는 상황에 대한 log 를 원할 것이다.
    이것이 logger 가 유용해지는 지점이고, 현 Flask 0.3 의 logger 는 당신이 사용할 수 있도록 사전 구성되었다.
    
    * log 를 사용한 예제는 다음과 같다.
    ```python
    app.logger.debug('A value for debugging')
    app.logger.warning('A warning occurred (%d apples)', 42)
    app.logger.error('An error occurred')
    ```
    * 위 코드에서의 logger 는 **is a standard logging Logger** 이므로, [the official logging documentation](https://docs.python.org/3/library/logging.html) 에서 더 많은 정보를 볼 수 있다.
- request, session 객체
    * reqeust 객체는 클라이언트 요청 시 유효하고 session 객체는 사용자 브라우저 접속만큼 유효하다. session 타임도 지정 가능. 
    * **request.method** 속성에는 요청 메서드 방식이 들어 있다.
    * **request.form['변수명']** 내부에는 form 리퀘스트가 포함하는 변수명에 해당하는 요청 데이터가 들어 있다.
    * **request.args.get('변수명')** 으로는 일반적인 요청 데이터를 가져온다.
    * 개발자가 flask 웹앱을 만들 때 session 을 사용하기 위해서는 **secret_key 지정이 필요**하다. 이것은 쿠키 상에 구현된다. (This is implemented on top of cookies for you and signs the cookies cryptographically.)
    * 이는 사용자가 쿠키 내부 내용을 볼 수 있다는 의미이지만 그렇다고(심지어 secret_key 를 알고 있다고 해도) 그것을 수정할 수는 없다.
    * 어떻게 좋은 비밀 키를 만들어내는가?
        - 가능한 한 랜덤값어야 한다.
        - 당신의 os 에는 암호화된 랜덤 generator 기반으로 가상 랜덤 데이터를 만드는 방법이 있다. (python 의 os 모듈 사용)
        - 예를 들어 ```$ python -c 'import os; print(os.urandom(16))``` 명령으로 암호화된 binary 데이터를 생성할 수 있다.
    * **session['속성명']** 내부에는 접속 사용자를 위한 여러 속성값을 저장할 수 있다. 대표적으로 로그인 세션 유지에 사용됨.
    * **session.pop('속성명', None)** 은 해당 속성을 제거하는 것(pop)
- app.errorhandler
- url_for()
- redirect()
- render_template('템플릿 파일명', 매개변수, 매개변수, ...)
- _id 와 ObjectId()

## flask_template.py (테스트용인듯)

<a href='https://github.com/daesungRa/flask/blob/master/flask_template.py' target='_blank'>코드 보기</a>

- return render_template('index.html', title="Flask Template Test", my_str="Hello Flask!", my_list=[x+1 for x in range(30)])
    * 'index.html' : 렌더링하는 템플릿 파일
    * title : 반환 정보 페이지의 타이틀
    * my_str : 그냥 인삿말?
    * my_list : response 테스트용 list. 0-29 총 30번 루핑하는 동안 각 인덱스에 +1 한 값을 list 타입으로 반환!

## venv/

## static/dist/

## templates/




