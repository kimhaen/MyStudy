
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190523-lightgray.svg?style=flat-square)

# Request 객체, cookie 와 session 사용법 정리

> flask-board 프로젝트 회원관리 적용하기 위함

> [tutorialspoint 참조/번역](https://www.tutorialspoint.com/flask/flask_request_object.htm)

## Request Object

- client's web page 로부터의 데이터는 global request 객체로써 서버에 전송된다.
- request 데이터를 처리하려면, 우선 그것(request)이 Flask 모듈로부터 import 되어야 한다.
- request 객체의 주요한 속성들은 다음과 같다.
    * **Form** : 이것은 key-value 쌍 구조의 dictionary 객체이다. 각각은 form parameter 와 value 에 대응된다
    * **args** : URL 의 물음표 마크(?) 이후에 포함된 질의 문자열의 parse 된 content 이다. (URL 에 포함된 파라미터라는 의미)
    * **Cookies** : cookie 이름과 값을 가지고 있는 dict 타입 객체이다.
    * **files** : 업로드된 파일 데이터
    * **method** : 최근 request 메서드

## Cookies

- 쿠키는 클라이언트 컴퓨터의 텍스트 파일 폼 내에 저장된다.
- 이것의 목적은 클라이언트의 usage 와 연관된 데이터를 기억하거나 추적함으로써 더 나은 방문자 경험과 사이트 통계를 보장하는 것에 있다.
- Request 객체는 cookie 의 속성을 포함하고 있는데, 이는 클라이언트가 전송한 모든 cookie 변수-값 으로 이루어진 dict 객체이다.
- 추가적으로 쿠키는 자신의 만료 시간, site 의 경로나 도메인명을 포함한다.
- **Flask** 에서, cookie 는 response 객체를 set 한다.
    * view 함수의 return 값으로 response 객체를 얻기 위해 **make_response()** 함수를 사용한다.
    * 그렇게 얻어진 response 객체의 **set_cookie()** 함수로 cookie 값을 저장한다.
- cookie 값을 읽는 것은 쉬운데, request.cookies 속성의 **get()** 메서드를 사용하면 된다.

**넘어온 form 데이터를 활용해 해당 cookie 값이 저장된 response 객체 만들기**
```python
...

@app.route('/setcookie', methods=['POST'])
def setcookie():
    if request.method == 'POST':
        user = request.form['nm']
        
        resp = make_response(render_template('readcookie.html'))
        resp.set_cookie('userID', user)
        
        return resp

...
```

**request 객체로부터 cookie 값을 얻어와 반환하기**
```python
...

@app.route('/getcookie')
def getcookie():
    name = request.cookie.get('userID')
    return '<h1>welcome ' + name + '</h1>'

...
```

## Session

- Cookie 와 달리 Session 데이터는 서버에 저장된다.
- Session 은 client 의 로그인에서부터 로그아웃까지의 시간적 interval 이 존재한다.
- 이 Session 을 통해 유지되어야만 하는 데이터는 서버의 임시 directory 에 저장된다.
- 각 client 에 대응하는 하나의 session 은 **Session ID** 로써 할당되는데, 이 데이터는 cookie 의 최상단에 저장되고,
- 서버는 그것들을 암호화하여 등록한다. 이러한 암호화를 위해 Flask application 은 **SECRET_KEY** 로써 정의된 값이 필요하다.
- Session 객체 또한 세션 variables-values 에 대응되는 key-value 쌍 구조의 dict 객체이다.
- 예를 들어, 세션 변수로 'username'-'admin' 쌍을 세팅하려면 다음의 구문을 이용하면 된다.
```text
Session['username'] = 'admin'
```
- 그리고 세션 변수를 가져오려면 **pop()** 메서드를 사용한다.
```text
session.pop('username', None)
```
- 다음의 코드는 Flask 에서 session 이 동작하는 과정을 간략히 나타낸다.
```python
from flask import Flask, session, redirect, url_for, escape, request

app = Flask(__name__)
app.secret_key = 'any random string’

@app.route('/')
def index():
   if 'username' in session:
      username = session['username']
         return 'Logged in as ' + username + '<br>' + \
         "<b><a href = '/logout'>click here to log out</a></b>"
   return "You are not logged in <br><a href = '/login'></b>" + \
      "click here to log in</b></a>"

@app.route('/login', methods = ['GET', 'POST'])
def login():
   if request.method == 'POST':
      session['username'] = request.form['username']
      return redirect(url_for('index'))
   return '''
	
   <form action = "" method = "post">
      <p><input type = text name = username/></p>
      <p<<input type = submit value = Login/></p>
   </form>
	
   '''

@app.route('/logout')
def logout():
   # remove the username from the session if it is there
   session.pop('username', None)
   return redirect(url_for('index'))
```






