
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190527-lightgray.svg?style=flat-square)

# Flask WTF 살펴보기

> 요약: WTF 는 안전하고 검증된 WTForm 을 제공함으로써 CSRF 취약점을 해결하거나, File upload 등의 부가적 form 기능 구현을 위한 모듈이다.

## CSRF?

- Cross-Site Request Forgery, 크로스사이트 요청 위조
- 사용자의 의지와 무관하게 공격자가 의도한 행동을 하여 특정 웹 페이지를 보안에 취약하게 하거나 수정, 삭제 등이 실행되도록 하는 공격방법.
- 상대적으로 난이도가 높지 않아 관리자 계정을 탈취하는 수단으로 사용된다.
- Flask 사용자는 Flask-WTF 확장을 통해 모든 폼을 이러한 공격으로부터 사전에 방어할 수 있다.

## Creating Forms

- Flask-WTF 는 당신의 Flask 어플리케이션에 결합할 수 있는 WTForms 를 제공한다.
```python
from flask_wtf import FlaskForm
from wtforms import StringField
from wtforms.validators import DataRequired

class MyForm(FlaskForm):
    name = StringField('name', validators=[DataRequired()])
```
- 이 과정에서 추가적으로, **a CSRF token hidden field** 가 자동으로 생성된다. 이것을 당신의 template 에 render 할 수 있다.
```html
<form method="POST" action="/">
    {{ form.csrf_token }}
    {{ form.name.label }} {{ form.name(size=20) }}
    <input type="submit" value="Go" />
</form>
```
- 만약 당신의 form 이 multiple hidden fields 를 가지고 있다면, 그것들을 하나의 블럭을 사용함으로써 렌더링할 수 있다.
```html
<form method="POST" action="/">
    {{ form.hidden_tag() }}
    {{ form.name.label }} {{ form.name(size=20) }}
    <input type="submit" value="Go" />
</form>
```

## Secure Form

- 아무런 환경설정 없이 **FlaskForm** 은 **scrf** 를 방어하는 **session secure form** 이 된다. (당신이 할 일은 없다)
- 그러나 만약 scrf protection 을 비활성화하고자 한다면 ```form = FlaskForm(scrf_enabled=False)``` 설정을 세팅하면 된다. (```WTF_CSRF_ENABLED = False``` 는 글로벌 범위)
- **csrf token** 을 생성하려면, Flask 어플리케이션의 **secret key** 를 활용하거나 **WTF_CSRF_SECRET_KEY = 'a random string'** 를 별도로 세팅한다. 

## Validating Forms

- 당신의 view handlers 에서 request 에 대해 유효성을 체크할 수 있다.
```python
...

@app.route('/submit', methods=['GET', 'POST'])
def submit():
    form = MyForm()
    if form.validate_on_submit():
        return redirect('/success')
    return render_template('submit.html', form=form)
    
...
```
- 주목할 점은 ```request.form``` 방식의 form 데이터를 Flask-WTF 클래스에 넘겨줄 필요가 없다는 것이다. (자동으로 로드됨)
- 그리고 편리한 ```validate_on_submit``` 은 그것이 **POST** 리퀘스트인지 그리고 유효한지를 체크한다.

## File Uploads

- 




