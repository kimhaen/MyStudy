
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190521-lightgray.svg?style=flat-square)

# Ajax 로 Json 데이터 전송하기 (with flask)

> 뭐 대충 form 형식 데이터는 serialize() 해서 그냥 보내면 된다

> 그러나 json 은..?

## Json 보내기

- 일단 일반 primitive 타입 데이터를 ajax 를 통해 보내려면
- json 형식의 객체에 담아 보내면 편리하다.
- 만약 'aaa' 라는 문자열과 정수 20 을 보낸다고 가정하면, 다음과 같이 구성할 수 있다.

```javascript
var sendData = {
    aaa: 'aaa',
    num: 20
}
```

- ajax 에서는 이 데이터를 보낼 때
- 전송 **dataType** 을 **'json'** 으로 지정하고
- **data** 자리에 **sendData** 를 넣어주면 된다.

```javascript
...

var sendData = {
    aaa: 'aaa',
    num: 20
}

...

    $.ajax({
        type: 'post',
        url: url,
        data: sendData,
        dataType: 'json',
        success: function(result) {
            ...
        }
    });
    
...
```

## flask 서버에서 받기

- 이런 식으로 전송된 json 데이터는 **key-value** 구조이기 때문에 다루기 쉽다.
- flask 서버는 전송된 post 방식의 파라미터를 크게 세 가지 방식으로 수용한다.
    1. request.args.get(key)
    2. request.form[key]
    3. request.values.get(key)
- 'GET' 방식으로 전달된 데이터는 args 모듈로,
- 'POST' 방식으로 전달된 데이터는 form 모듈로,
- 구분 없이 모든 데이터를 다룰 때는 values 모듈을 사용한다.
- 1, 2 번 방식은 명시적으로 지정해주기 때문에 성능상 낫지만 상호호환이 안 되고,
- 3 번 방식은 모두 검색하느라 다소 느리지만 구분 없이 사용 가능하다.

```python
...

@app.route('/getData')
def get_data():
    # aaa = request.args.get('aaa') # get 방식 전송 데이터라면
    aaa = request.form['aaa']
    num = request.values.get('num')
    print('출력결과 : ' + aaa + ',' + num)
    
...
```

```text
출력결과 : aaa, 20
```




