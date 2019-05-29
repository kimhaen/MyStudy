
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190529-lightgray.svg?style=flat-square)

# Message Flashing

> 참조 : [Flask:Message-Flashing (수정/번역)](http://flask.pocoo.org/docs/1.0/patterns/flashing/)

- 좋은 application 과 좋은 user interface 는 적절한 feedback 에 좌우된다.
- 만약 user 가 충분한 feedback 을 받지 않는다면, 그들의 그 application 에 대한 경험은 좋지 않게 끝날 것이다.
- **Flask** 는 사용자에게 feedback 을 반환하기 위해 **flashing system** 이라는 매우 간단한 방법을 제공한다.
- **flashing system** 은 최종적인 request 에서 메시지를 record 하는 것을 근본적으로 가능하게 하며, 다음 request 에 access 할 수 있도록 한다.
- 다음은 layout template 에 포함된 일반적인 예시이다.
- 다만 기억할 점은 브라우저나 (가끔) 웹서버가 쿠키 사이즈를 제한한다는 것인데,
- 이 때는 flashing messages 가 session cookie 에 있어서 너무 크기(too large) 때문에 메시지 flashing 이 암묵적으로 실패할 수 있다.

## Flashing With Categories

- 기본 flashing 에 더해서 category 까지 포함하는 과정을 설명한다

다음은 full example 이다.
```python
from flask import Flask, flash, redirect, render_template, \
    request, url_for

app = Flask(__name__)
app.secret_key = b'SECRET KEY'

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    error = None
    if request.method == 'POST':
        if request.form['username'] != 'admin' or \
                request.form['password'] != 'secret':
            error = 'Invalid credentials'
        else:
            flash('You were successfully logged in', 'success')
            return redirect(url_for('index'))
    return render_template('login.html', error=error)
```

- category 포함을 위해 **flash()** 함수의 두 번째 매개변수로 **'success'** 를 넘겨준다. 이것을 **category_filter** 라고 한다.
- category 에는 ```error, info, warning 이 포함된다```

a template of ```layout.html``` which does the magic:
```html
<!DOCTYPE HTML>
<title>My Application</title>
{% with messages = get_flashed_messages(with_categories=true) %}
    {% if messages %}
        {% for category, message in messages %}
            <div class="alert alert-{{ category }}">{{ message }}</div>
        {% endfor %}
    {% endif %}
{% endwith %}
{% block body %}{% endblock %}
```

- view 함수에서 category 를 포함시켜 flashing 했다면,
- ```get_flashed_messages()``` 함수로 flash 메시지 그룹을 가져올 때 **with_categories=true** 인자를 넘겨준다.
- 그룹을 담은 messages 로부터 각 메시지를 가져올 때는, category, message 변수에 담아 활용하면 된다.
- 브라우저 상에서는 각 category 에 맞는 배경과 형태로 message 가 표현된다.

a template of ```index.html``` which inherits from ```layout.html```:
```html
{% extends "layout.html" %}
{% block body %}
    <h1>Overview</h1>
    <p>Do you want to <a href="{{ url_for('login') }}">log in?</a>
{% endblock %}
```

- login 실패 시에는 error 메시지와 함께 ```login.html``` 로 렌더링 된다.

a template of ```login.html``` which also inherits from ```layout.html```:
```html
{% extends "layout.html" %}
{% block body %}
    <h1>Login</h1>
    {% if error %}
        <p class=error><strong>Error:</strong>{{ error }}</p>
    {% endif %}
    <form method=post>
        <dl>
            <dt>Username:
            <dd><input type="text" name="username" value="{{ request.form.username }}" />
            <dt>Password:
            <dd><input type="password" name="password" />
        </dl>
        <p/>
        <input type="submit" value="Login">
    </form>
{% endblock %}
```

## Filtering Flash Messages

- 각 메시지가 저장된 category 리스트를 전달할 때, ```get_flashed_messages()``` 함수의 인자로 ```category_filter=['CATEGORY NAME']``` 를 넘겨주면
- 해당 카테고리에 부합하는 메시지와 category 만 필터링 된다.

```html
{% with errors = get_flashed_messages(category_filter=["error"]) %}
{% if errors %}
    <div class="alert-message block-message error">
        <a class="close" href="#">×</a>
        <ul>
            {%- for msg in errors %}
            <li>{{ msg }}</li>
            {% endfor -%}
        </ul>
    </div>
{% endif %}
{% endwith %}
```




