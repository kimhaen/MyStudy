
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190527-lightgray.svg?style=flat-square)

# Flask CORS 살펴보기

> 요약: CORS 는 크로스도메인 문제를 해결하기 위한 모듈이다.

## Flask-CORS (Cross Origin Resource Sharing)

> 참조 : [flask-cors docs](https://flask-cors.readthedocs.io/en/latest/)

- 모듈명은 Flask-CORS 로, 이것을 import 해서 사용하면 된다
- 그러나 그 이전에 왜 CORS 가 필요한지에 대해 생각해봐야 한다
    * W3C 규정상 웹 어플리케이션은 HTTP request 의 도메인이 자기 자신과 완전히 일치해야만 요청을 수용한다 (그렇지 않으면 애초에 요청 자체를 받아들이지 않음)
    * 이는 보안상의 이유가 가장 큰데, 예를 들어
        - **http://aaa.com** 도메인의 API 서버로부터 특정 데이터를 받아오기 위해 **http://bbb.com** 도메인의 웹앱 서버가 aaa 로 요청을 보낸다면, 요청의 내용과 상관 없이 원천적으로 차단된다.
        - aaa 서버의 입장에서 이것은 신뢰되지 않은 (식별되지 않은) 도메인으로부터의 요청으로 잠재적인 해킹의 가능성이 존재하기 때문이다.
        - 이 때 **Cross Origin Resource Sharin**, 즉 서로 다른 도메인 리소스를 공유하도록 하는 모듈을 사용하여 상호간에 식별이 가능하게 한다면 문제를 해결할 수 있다.
    * Flask-CORS 를 사용한다면 특정 도메인의 차단 여부를 사전에 설정해둘 수 있다.

**샘플은 다음과 같다**
```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

@app.route('/')
def welcome():
    return "Welcome!!"
```

- 특정 리소스를 CORS 로 지정하기
    * 특정 매핑 경로가 세팅된 dict 를 **resources option** 으로 전달함으로써, CORS 옵션을 지정할 수 있다.
    * 이는 특정 리소스(라우팅 소스)나 세분화된 origin level 요청에 대한 크로스도메인 공유가 가능하게 한다.
```python
...

app = Flask(__name__)
cors = CORS(app, resources={r"/api/*": {"origins": "*"}})

@app.route("/api/v1/users")
def list_users();
    return "user example"
    
...
```

- decorator 를 사용해 라우트 지정하기
    * 이번에는 라우트를 감싸는 매우 단순한 decorator 를 사용해 CORS 를 지정한다.
    * ```@cross_origin()``` 데코레이터가 그것인데, ```@app.route('/')``` 밑에 더함으로써 해당 라우트에 대한 CORS 를 제공한다.
```python
...

@app.route('/')
@cross_origin()
def welcome():
    return "Welcome!!"

...
```
- 더 자세한 내용은, [cors 문서](https://flask-cors.corydolphin.com/en/latest/api.html) 를 참조할 것


