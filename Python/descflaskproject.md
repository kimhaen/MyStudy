
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

<a href='https://github.com/daesungRa/flask/blob/master/hello.py' about='_blank'>코드 보기</a>

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
- 
- 
- 
- 
- 
- 




