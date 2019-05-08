![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190507-lightgray.svg?style=flat-square)

# 플라스크 프로젝트 시작하기

> Python 3.7 / Flask / MongoDB / PyCharm / PyMongo / summernote editor 등등

- [Flask 공식 도큐먼트](http://flask.pocoo.org/docs/1.0/)와 [MongoDB 연동 예제](https://api.mongodb.com/python/current/)들을 참조한다
- 기본 베이스는 과장님이 만든 [flask 예제 프로젝트 - fork](https://github.com/daesungRa/flask)
- 포커스는 flask 프로젝트 구성 및 요청 처리과정, pymongo 를 통해 원격지 mongodb 서버에 접근하여 데이터를 관리하는 방식을 알아가는 것에 있다.

## Flask 기본 개요

- **"micro" ??** : 하나의 py 파일에 모든 웹앱 요소를 때려박는다거나 기능적으로 부족한 부분이 있다는 의미가 아니라, "**Flask aims to keep the core simple but extensible**" 임을 의미한다.
- 환경 설정 및 컨벤션에 대한 내용은 [이전 글](https://github.com/daesungRa/MyStudy/blob/master/Python/flaskconfig.md)을 참조한다.
- 그렇다고 꼭 컨벤션에 얽매일 필요는 없다. 본인의 코딩 스킬이 향상되었다면, 프로젝트를 자유롭게 디자인해볼 수도 있는 것이다.

## MongoDB 기본 개요

- 몽고디비는 기존의 RDBMS 의 비용 및 확장성 문제 보완을 위해 만들어진 대표적인 NoSQL 이다.
- 스키마 구조가 없기 때문에 초대량의 데이터를 매우 빠른 속도로 처리할 수 있고, 자유로운 확장 및 마이그레이션이 가능하다. 일반적으로 master-slave 구조로 3 개 이상의 데이터서버를 구축해 상호보완적으로 운용한다.
- 그러나 데이터 수정이 잦고 복잡한 쿼리가 필요하거나 안정적이며 일관성 있는 데이터 처리가 요구될 때는 불리한 측면이 있다. 그리고 빅데이터를 다루는 만큼 하드웨어적인 디스크 성능이 중요하고, 부분적인 데이터 유실 가능성도 존재한다.
- 기본 단위는 document, 즉 문서이며 테이블에 해당하는 개념은 collection 이다.
- 이 collection 내에 원하는 방식의 JSON(혹은 BSON) 데이터를 저장 및 조회한다.
- python 프로젝트에서 MongoDB 를 사용하기 위해서는 디비 서버로부터 클라이언트 커넥션을 얻어오기 위한 **PyMongo 모듈**이 필요하다.

#### PyMongo 활용예제 ([pymongo docs](https://api.mongodb.com/python/current/index.html))

- PyMongo 는 몽고디비를 활용할 수 있는 도구를 포함한 파이썬 기반 배포 모듈이다. (MongoDB 의 설치 및 구성방법은 [MongoDB Tutorial](https://docs.mongodb.com/manual/tutorial/) 을 참조한다.)
- 설치
    * 별도의 bson 패키지는 설치하지 말 것. 이미 pymongo 에는 고유의 bson 패키지가 포함되어 있다.
    * 원하는 venv 에서 pip 모듈을 이용해 pymongo 를 install 한다. 업그레이드 시에는 --upgrade 옵션을 추가한다.
- 사용 (post 데이터 활용예제)
    * 먼저 설치한 pymongo 모듈의 MongoClient 를 import 한다. MongoClient 는 지정한 경로의 몽고디비의 인스턴스를 생성하는 모듈이다.
    * MongoClient 로부터 인스턴스를 생성하고(접속주소, 포트번호 등), 인스턴스로부터 사용할 database 객체를 얻어온 후 최종적으로 원하는 collection 객체를 얻어온다.
    * 기억할 점은, collection 객체를 얻어오기까지의 코드는 디비 서버에서 아무런 동작도 하지 않은 상태라는 것인데, 이후 첫 번째 document 를 insert 하는 과정에서 collection, database 객체가 최초 생성된다.
    * 하나의 post 를 등록하기 위해 기존의 JSON 방식으로 데이터를 만들고 insert 를 수행한다. 이 과정에서 JSON 데이터는 자동적으로 BSON(binary) 타입으로 convert 된다. (특히 native python 타입)
    * 지금까지의 샘플 코드는 다음과 같다.

```python
from flask import Flask, render_template
from pymongo import MongoClient
import datetime

app = Flask(__name__)

@app.route("/samplePost", methods=['GET'])
def samplePost():
    # client = MongoClient('localhost', 27017)
    client = MongoClient('mongodb://localhost:27017')
    db = client.test_database
    collection = db.posts
    
    post = {
                "author":"Mike",
                "text":"My first blog post!",
                "tags":["mongodb", "python", "pymongo"],
                "date":datetime.datetime.utcnow()
            }
    
    collection.insert_one(post)
    client.close()
    
    return "post 등록 완료!"

@app.route("/postList", methods=['GET'])
def postList():
    # client = MongoClient('localhost', 27017)
    client = MongoClient('mongodb://localhost:27017')
    db = client.test_database
    collection = db.posts
    results = collection.find()
    client.close()
    
    return render_template("posts.html", data=results)

if __name__ == '__main__':
    app.run(debug=True)
```

- 위 샘플코드에서는 기본적으로 insert 와 find 기능을 통해 post 를 등록하고 조회한다.
- find 는 등록된 모든 데이터들을 조회해 리스트 형식으로 반환한다.
- 참고로, JSON 데이터 정렬해주는 사이트 : http://json.parser.online.fr/

#### pymongo 확장된 활용

- **Return the _id Field**
    * MongoDB 에서 Field 는 Column 을 의미한다. (document 는 row 를 의미함)
    * insert_one 메서드를 실행하면 결과에 따라 특정 반환값을 리턴하는데, InsertOneResult 객체가 그것이다.
    * InsertOneResult 객체는 **inserted_id** 라는 속성을 가지고 있는데 이것은 도큐먼트 insert 시 자동으로 생성되는 **_id 필드**의 값이다.
    * _id 필드는 insert 작업 시 별다른 specify 가 없다면 MongoDB 에서 unique 특성을 지닌 id 값을 자동으로 생성해 각 도큐먼트와 함께 저장한다.
    * 이것은 구분자 역할을 하는데, 일반적으로 힙 메모리 주소값을 갖는다. (ex. 5b1910482ddb101b7042fcd7)
    * 여기서 꼭 기억할 점은, _id 필드의 값이 **binary 타입**이라는 것인데, 이것은 string 타입과 다르므로 주의해서 사용할 것.
    * **_id**의 값을 특정해서 저장할 수도 있는데, 컬렉션에 ```[{"_id":1, "text":"hihi"}, {"_id":2, "text":"hello"}, ...]``` 와 같은 식으로 저장하면 된다.
    * 다음은 위 코드에서 inser_one 의 리턴값을 반환하는 로직을 추가한 것이다.

```python
...

    x = collection.insert_one(post)
    client.close()
    
    return render_template("insertedid.html", id=x.inserted_id)

...
``` 

- **Getting a Single Document With find_one()**
    * find 메서드는 컬렉션에 저장된 모든 정보를 리턴한다.
    * find_one 메서드는 인자로 입력한 정보(key-value) 에 해당하는 도큐먼트를 리턴한다.
    * 매칭되는 도큐먼트가 없다면 아무 값도 리턴하지 않는다.
    * 만약 ```result = collention.find_one({"author":"Mike"})``` 명령을 실행한다면 이에 해당하는 document 가 리턴되는 식이다.
    * 인자로 **_id** 컬럼 값을 넘겨줘도 동일한 document 를 리턴한다.
    * 일반적으로 입력하는 데이터 타입은 string 이므로, 실제 저장된 객체 주소 타입(binary)과 매칭시키기 위하여 ```ObjectId()``` 메서드를 꼭 활용해야 한다! **> 문자열 값을 객체 아이디 형식으로 변환해줌**
    * 아래 예제에서는 url 변수로 받은 문자열 형식의 **post_id** 를 **ObjectId()** 함수를 통해 **binary 형식의 _id 컬럼**에 맞게 변환하여 그에 맞는 하나의 도큐먼트(_id 는 unique 임)를 조회한다.

```python
...

@app.route("/findone/<post_id>", methods=['POST'])
def findoneResult(post_id):
    ...

    doc = collection.find_one({"_id":ObjectId(post_id)})
    client.close()
    
    return render_template("findoneresult.html", doc=doc)

...
```

- **Insert Multiple Documents**
    * 둘 이상의 documents 를 insert 하기 위하여 **insert_many()** 함수를 사용할 수 있다.
    * 이것의 첫 번째 매개변수는 **list 형식**이고, 그 안에 여러 개의 document 가 존재하여 insert 시 차례로 등록된다.
    * insert 후 반환객체는 **inserted_ids** 정보를 담고 있으며, 이것은 등록된 각 도큐먼트에 대응되는 **ObjectId 형식의 _id 값 리스트**이다.
    * 다음 예제에서는 주목할 점이 하나 있는데, **collection 에 한 번에 insert 되는 여러 도큐먼드들의 스키마가 동일하지 않다는 것**이다.
    * new_posts 리스트의 0번, 1번 인덱스 도큐먼트는 title, tags 필드가 서로 존재하지 않지만, 모두 한 번에 등록되었다.
    * 이것이 MongoDB 의 Schema-Free 특성이다.

```python
>>> new_posts = [{"author": "Mike",
...               "text": "Another post!",
...               "tags": ["bulk", "insert"],
...               "date": datetime.datetime(2009, 11, 12, 11, 14)},
...              {"author": "Eliot",
...               "title": "MongoDB is fun",
...               "text": "and pretty easy too!",
...               "date": datetime.datetime(2009, 11, 10, 10, 45)}]
>>> result = posts.insert_many(new_posts)
>>> result.inserted_ids
[ObjectId('...'), ObjectId('...')]
```

- **Quering for More Than One Document**
    * 앞서 find() 함수는 (별다른 매개변수가 없다면) collection 에 존재하는 모든 도큐먼트 리스트를 조회한다고 설명했다.
    * 내용을 좀더 추가하자면, find() 함수의 리턴 데이터는 커서(Cursor) 인스턴스인데,
    * 이 커서 객체는 iterate 반복자를 통해 원하는 대로 컨트롤할 수 있다.
    * 또한 find() 함수에 특정 조회조건을 인자로 투입하면(key-value) 그것에 대응하는 도큐먼트들만 리스트 객체로 반환한다.

```python
>>> for post in posts.find():
...   pprint.pprint(post)
...
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'mongodb', u'python', u'pymongo'],
 u'text': u'My first blog post!'}
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'bulk', u'insert'],
 u'text': u'Another post!'}
{u'_id': ObjectId('...'),
 u'author': u'Eliot',
 u'date': datetime.datetime(...),
 u'text': u'and pretty easy too!',
 u'title': u'MongoDB is fun'}
```

```python
>>> for post in posts.find({"author": "Mike"}):
...   pprint.pprint(post)
...
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'mongodb', u'python', u'pymongo'],
 u'text': u'My first blog post!'}
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'bulk', u'insert'],
 u'text': u'Another post!'}
```

- **Counting**
    * 선택한 collection 에 대해 ```count_documents({})``` 함수를 실행하면 몇 개의 도큐먼트가 존재하는지 알 수 있다.
    * 같은 방식으로 조회조건 인자를 투입시키면 그것에 해당하는 결과 카운트만 반환한다.

```python
>>> posts.count_documents({})
3
>>> posts.count_documents({"author": "Mike"})
2
```

- **Range Queries**
    * MongoDB 에서도 RDBMS 와 마찬가지로 범주(Range)에 따른 향상된 조건조회를 하고자 할 때가 있다.
    * find() 조회 조건에 **$lt** 또는 **$gt** 와 같은 비교키워드를 활용하거나 sort() 작업을 추가할 수 있다.

```python
from pymongo import MongoClient
import datetime

client = MongoClient('mongodb://localhost:27017')
db = client.test_database
collection = db.posts

d = datetime.datetime.now() # datetime 타입의 객체 반환

for post in collection.find({"date":{"$lt":d}}).sort("author"):
    print(str(post))
```

- **Indexing**
    * 자동으로 생성되는 **_id** 외에, collection.create_index() 함수로 선택한 collection 에 대한 index field 를 생성할 수 있다.
    * 이때 인자로 ```unique=True``` 값을 넘겨줘야 해당 속성이 적용된다.
    * 이후 도큐먼트 데이터를 투입할 때 생성한 인덱스에 해당하는 값도 포함시키면 된다.
    * unique 특성이기 때문에 중복된 index 값을 투입시키고자 한다면, ```DuplicateKeyError``` 가 발생한다.
    * [The MongoDB documentation on indexes](https://docs.mongodb.com/manual/indexes/)

```python
>>> result = db.profiles.create_index([('user_id', pymongo.ASCENDING)],
...                                   unique=True)

>>> user_profiles = [
...     {'user_id': 211, 'name': 'Luke'},
...     {'user_id': 212, 'name': 'Ziltoid'}]
>>> result = db.profiles.insert_many(user_profiles)

>>> new_profile = {'user_id': 213, 'name': 'Drew'}
>>> duplicate_profile = {'user_id': 212, 'name': 'Tommy'}
>>> result = db.profiles.insert_one(new_profile)  # This is fine.
>>> result = db.profiles.insert_one(duplicate_profile)
Traceback (most recent call last):
DuplicateKeyError: E11000 duplicate key error index: test_database.profiles.$user_id_1 dup key: { : 212 }
```

#### Python 에서 취급되는 Unicode Strings 에 대한 간단설명

- Python 코드를 통해 몽고디비 서버로부터 얻어온 각 string 데이터들은 뭔가 약간 생김새가 다르다. (e.g. u'Mike' instead of 'Mike')
- 앞서 설명했듯 몽고디비는 **BSON format** 의 데이터를 저장하는데, 이것은 UTF-8 으로 인코딩된 것이므로 PyMongo 는 저장된 모든 데이터가 **UTF-8 하에서만 유효하다는 것**을 보장해야만 한다.
- Regular string (<type 'str'>) 이 디비에 저장될 때는 바로 UTF-8 인코딩이 이루어지고 유효성이 보장된 다음 저장되는 반면, Unicode strings (<type 'unicode'>) 는 반대로 저장된 BSON 문자열에서 UTF-8 으로 인코딩이 먼저 된 이후에 반환된다.
- 그러므로 저장된 BSON 데이터를 조회하는 PyMongo 입장에서는 바이너리 데이터에서 UTF-8 으로 변환된 데이터라는 사실을 적시함으로써 안정성을 보장한다.
- [you can read more about Python unicode strings here](https://docs.python.org/3/howto/unicode.html) 

## 프로젝트 설명

- 분량 관계상 [다음 글](https://github.com/daesungRa/MyStudy/blob/master/Python/descflaskproject.md)에서 다루기로 합시다 ㅎㅎ

