
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190508-lightgray.svg?style=flat-square)

# Python 에서 JSON 다루는 방법

참조 : https://soooprmx.com/archives/6788 요약

- JSON 이란?
    * JavaScript Object Notation
    * Key-Value 쌍의 포맷으로 구조화된 정보를 인코딩하는 규격
    * 유연성 높은 XML 의 대안 > 용량 및 비용, 효율성 측면
    * JSON 포맷은 Python 의 dictionary 혹은 list 포맷과 일치한다. 이러한 포맷을 복합적으로 사용
    * JSON 데이터를 일반 텍스트 정보로 생각하기 쉬운데, 실제로는 **유니코드 텍스트** 정보로써 파일 고정 및 네트워크 전송 시 UTF-8 등의 유니코드로 인코딩되어 바이너리 데이터로 변환된다.
    * 즉, JSON 파일은 **UTF-8 등의 유니코드 인코딩을 통해 저장되는 바이너리 데이터**로 이해하는 쪽이 올바르다. (JSON 호환 타입 객체를 인코딩한 바이트 객체)

- 파이썬에서의 JSON 호환 객체
    * 정수 혹은 실수의 number 타입 (int / float)
    * 문자열
    * 모든 키가 문자열 타입이며, 모든 값은 JSON 호환 객체인 dict
    * 모든 원소가 JSON 호환 객체인 리스트

- 파이썬에서 JSON 호환 타입 객체를 JSON 데이터로 인코딩하는 함수
    * 인코딩된 JSON 파일 정보는 dict 처럼 조작하거나 특정 키를 통해 액세스할 수는 없다. 그래서 다음의 함수를 사용한다.
    * **dump/dumps** : JSON 호환 타입 객체를 JSON 데이터로 인코딩하는 함수
    * **load/loads** : JSON 데이터를 JSON 호환 타입 객체로 환원하는 함수
    * 각 함수에 's' 가 붙은 버전은 '문자열 기반' 으로 말 그대로 JSON 을 표현하는 문자열로 변환하거나 그 역을 수행한다.

- dump, load 샘플 예제

```python
import json

## read json file
with open('myobj.json', 'rb') as f:
    root = json.load(f)
    ...

## writh json file
with open('myobj2.json', 'wb') as f:
    json.dump(root, f)
```

## 값 (그대로) 인코딩 Feat. 비라틴계 문자열

- 특정 문자열을 유니코드로 인코딩해 JSON 으로 저장하는 과정에서, 비라틴 계열 문자를 읽을 수 있는 그대로 저장하고자 할 때가 있다. (원래는 유니코드 코드 포인트 값으로 바뀜)
- 이 때는 dump(), dumps() 함수의 파라미터로 ```ensure_ascii=False``` 값을 추가해야 한다.

```python
>>> json.dumps('{"name": "안녕이다"}')                       
'"{\\"name\\": \\"\\uc548\\ub155\\uc774\\ub2e4\\"}"' 


>>> json.dumps('{"name": "안녕이다"}', ensure_ascii=False)   
'"{\\"name\\": \\"안녕이다\\"}"'
```

## Flask 에서 JSON

- Flask 에서는 ```jsonify()``` 라는 유틸리티 함수가 있어서 dict 나 list 를 활용해 JSON 타입의 HTTP Response 를 쉽게 생성해준다.
- 만약 여기서도 비라틴계 문자열을 그대로 반환하고 싶다면 (프론트 단에 그냥 반환해도 어차피 본래 문자열로 변환하는 작업이 필요하다)
- dumps 함수와 ```ensure_ascii=False``` 인자를 활용해 생성한 유니코드 JSON 데이터를 사용하거나,
- app 차원에서 ```JSON_AS_ASCII``` 환경변수를 ```False``` 로 세팅해주면 된다.
- 다음은 두 가지 방법을 활용한 예제이다.

```python
from flask import Flask, make_response, jsonify
import json

app = Flask(__name__)

data = [
            {"_id":1, "name":"rara", "comment":"안녕하세요1~"},
            {"_id":2, "name":"kim", "comment":"안녕하세요2~"},
            {"_id":3, "name":"hong", "comment":"안녕하세요3~"}
        ]

@app.route("/jsonify/test", methods=['GET'])
def jsonifyTest():
    print('before encode json : ' + str(data) + ' << 이건 그냥 문자열 리스트+딕셔너리')
    print('encoded json data : ' + json.dumps(data) + ' << 이건 json 데이터로 인코딩된 결과')
    print('encoded json data : ' + json.dumps(data, indent=4) + ' << 한줄로 보기 불편하니 indent=4 옵션을 넣어줌. 4 는 tab 크기인 4 칸을 의미')
    jsondata = json.dumps(data, ensure_ascii=False, indent=4)
    print('encoded json data : ' + jsondata + ' << 인코딩되면서 길게 변환되었던 한글 문자열을 보는 그대로 인코딩한 결과. ensure_ascii=False 인자를 넘겨줌!')
    print('decode encoded json data : ' + str(json.loads(jsondata)) + ' << 인코딩했던 json 데이터를 디코딩한 결과. 다시 리스트+딕셔너리 형식으로 변환됨')
    return jsonify(data)

@app.route("/jsonify/intoutf8", methods=['GET'])
def intoutf8():
    encodeddata = json.dumps(data, ensure_ascii=False, indent=4)
    print('인코딩된 데이터(ensure_ascii=False, indent=4) : ' + str(encodeddata))
    response = make_response(encodeddata)
    response.headers['Content-Type'] = 'application/json'
    return response

if __name__ == '__main__':
    app.config['JSON_AS_ASCII'] = False
    app.run(debug=True)
```

- 실행 결과는 다음과 같다.

**console 출력 결과**
```text

## call jsonifytest()
before encode json : [{'_id': 1, 'name': 'rara', 'comment': '안녕하세요1~'}, {'_id': 2, 'name': 'kim', 'comment': '안녕하세요2~'}, {'_id': 3, 'name': 'hong', 'comment': '안녕하세요3~'}] << 이건 그냥 문자열 리스트+딕셔너리
encoded json data : [{"_id": 1, "name": "rara", "comment": "\uc548\ub155\ud558\uc138\uc6941~"}, {"_id": 2, "name": "kim", "comment": "\uc548\ub155\ud558\uc138\uc6942~"}, {"_id": 3, "name": "hong", "comment": "\uc548\ub155\ud558\uc138\uc6943~"}] << 이건 json 데이터로 인코딩된 결과
encoded json data : [
    {
        "_id": 1,
        "name": "rara",
        "comment": "\uc548\ub155\ud558\uc138\uc6941~"
    },
    {
        "_id": 2,
        "name": "kim",
        "comment": "\uc548\ub155\ud558\uc138\uc6942~"
    },
    {
        "_id": 3,
        "name": "hong",
        "comment": "\uc548\ub155\ud558\uc138\uc6943~"
    }
] << 한줄로 보기 불편하니 indent=4 옵션을 넣어줌. 4 는 tab 크기인 4 칸을 의미
encoded json data : [
    {
        "_id": 1,
        "name": "rara",
        "comment": "안녕하세요1~"
    },
    {
        "_id": 2,
        "name": "kim",
        "comment": "안녕하세요2~"
    },
    {
        "_id": 3,
        "name": "hong",
        "comment": "안녕하세요3~"
    }
] << 인코딩되면서 길게 변환되었던 한글 문자열을 보는 그대로 인코딩한 결과. ensure_ascii=False 인자를 넘겨줌!
decode encoded json data : [{'_id': 1, 'name': 'rara', 'comment': '안녕하세요1~'}, {'_id': 2, 'name': 'kim', 'comment': '안녕하세요2~'}, {'_id': 3, 'name': 'hong', 'comment': '안녕하세요3~'}] << 인코딩했던 json 데이터를 디코딩한 결과. 다시 리스트+딕셔너리 형식으로 변환됨



## call intoutf8()
인코딩된 데이터(ensure_ascii=False, indent=4) : [
    {
        "_id": 1,
        "name": "rara",
        "comment": "안녕하세요1~"
    },
    {
        "_id": 2,
        "name": "kim",
        "comment": "안녕하세요2~"
    },
    {
        "_id": 3,
        "name": "hong",
        "comment": "안녕하세요3~"
    }
]


```

**browser 출력 결과**
```text

## call jsonifytest()
[
  {
    "_id": 1, 
    "comment": "안녕하세요1~", 
    "name": "rara"
  }, 
  {
    "_id": 2, 
    "comment": "안녕하세요2~", 
    "name": "kim"
  }, 
  {
    "_id": 3, 
    "comment": "안녕하세요3~", 
    "name": "hong"
  }
]


## call intoutf8() 
[ { "_id": 1, "name": "rara", "comment": "안녕하세요1~" }, { "_id": 2, "name": "kim", "comment": "안녕하세요2~" }, { "_id": 3, "name": "hong", "comment": "안녕하세요3~" } ]


```

- ```app.config['JSON_AS_ASCII'] = False``` 설정으로 인해 브라우저에서는 항상 문자 그대로 출력된다.
- ```jsonify()``` 의 리턴 결과는 들여쓰기, 줄바꿈 및 정렬이 자동으로 이루어지는 반면, dumps() 로 인코딩된 JSON 데이터는 아무리 정렬했다고 해도 웹앱 리턴 시 한 줄로 그냥 출력된다. 

## json.dumps() 와 flask.jsonify() 의 차이점은??

- dumps() 의 결과 반환되는 데이터는 정말 단순히 JSON 형식으로 인코딩된 결과이다.
- 그러나 jsonify() 의 반환 데이터는 flask.Response() 객체이며, 그 안에 매개변수 인코딩 결과(JSON)가 담겨있는 형태이다.
- 또한 response 객체의 header 에는 JSON 데이터를 다루기 위한 ```content-type='application/json'``` 속성값이 설정되어 있다.
- [(stackoverflow) json.dumps vs flask.jsonify](https://stackoverflow.com/questions/7907596/json-dumps-vs-flask-jsonify)



