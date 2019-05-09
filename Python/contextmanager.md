
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190509-lightgray.svg?style=flat-square)

참조 : [book.pythontips.com-context manager](http://book.pythontips.com/en/latest/context_managers.html) / [velog 블로그 - 파이썬 context manager 정리](https://velog.io/@sjquant/%ED%8C%8C%EC%9D%B4%EC%8D%AC-context-manager-with%EA%B5%AC%EB%AC%B8%EC%9C%BC%EB%A1%9C-%EC%95%88%EC%A0%84%ED%95%9C-%EB%A6%AC%EC%86%8C%EC%8A%A4-%EA%B4%80%EB%A6%AC%EB%A5%BC-%ED%95%98%EC%9E%90.)

# Context Manager 와 with 구문

> 어떤 컴퓨팅 환경에서건 특정 리소스(resource) 를 사용(acquire) 하고 그것을 해제(release) 해주지 않으면 resource 누수(leak) 현상이 발생한다.

- 리소스를 최종적으로 close 해주는 라인이 존재한다 하더라도 중간 로직에 exception 이 발생한다면 close 는 실행되지 않을 것이다.
- 일반적인 문법에서는 try-finally 구문을 통해 무조건적으로 close 코드가 실행되도록 조치할 수 있지만(python 도 마찬가지),
- 파이썬에서는 context manager 와 with 구문을 통해 보다 명시적이고(시간적으로) 간결하게 해당 로직을 구현할 수 있다.

## context manager

- context manager 는 with 구문에서 쓰일 수 있는 일종의 객체 타입인데, 직접적으로 사용할 수는 없고 프로토콜에 따라 이것이 구현된 특정 클래스를 작성할 수 있다.
- file 객체나 database connection 객체와 같은 리소스 활용 클래스 기반 객체가 그 예이다.
- 포인트는 해당 리소스의 allocate and release 가 적시에 이루어지도록 할 수 있다는 점에 있다. (precisely when you want to)

## with 구문

- context manager 가 구현된 클래스 인스턴스를 with 구문에서 사용하면,
- 진입 시 ```__enter__``` 메서드가 호출되고 빠져나갈 때 ```__exit__``` 메서드가 호출된다.
- 이 모든 과정은 자동으로 이루어진다.

## context manager 구현하기

- 대표적으로 context manager 클래스는 ```__enter__``` 와 ```__exit__``` 메서드로 이루어져 있다. 다음 예제를 보자.

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base

class SQLAlchemyDBConnection(object):
    """SQLAlchemy database connection"""
    def __init__(self, connection_string):
        self.connection_string = connection_string
        self.session = None
    
    # with 구문 진입 시 db 와 connection 하고,
    # ORM 을 위한 session 을 만든다.
    def __enter__(self):
        engine = create_engine(self.connection_string)
        
        if engine is not None:
            self.session = sessionmaker(bind=engine)
            return self
        else:
            raise IOError("Cannot access Database")
    
    # with 구문을 빠져나오기 전 session 을 종료한다.
    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.session is not None:
            self.session.close()

connection_string = '커넥션 주소!'
Base = declarative_base() # 베이스 매핑 클래스. Customer 매핑 클래스로 구현 가능. 추후 더 살펴볼 것.
with SQLAlchemyDBConnection(connection_string=connection_string) as db:
    customer = db.session.query(Base).filter_by(id=123).one()
    print(customer.name)
```

- 위 코드는 context manager 로써 SQLAlchemy orm 을 활용한 예제이다.
- 해당 db connection 클래스를 구현하여 이것을 with 구문에서 인스턴스화할 때 ```__enter__``` 메서드가, 빠져나갈 때 ```__exit__``` 메서드가 자동 호출되도록 만들었다.
- 최초 인스턴스화 시에는 ```__init__``` 메서드가 호출되어 engine 및 session 을 생성하기 위한 참조변수를 선언한다.

## generator 와 decorator 로 context manager 구현하기

- 파이썬 모듈인 **contextlib** 을 활용하면 보다 간편하게 context manager 를 구현할 수 있다.
- 이 때는 class 대신에 generator 함수로 구현 가능하다.

```python
from contextlib import contextmanager

@contextmanager
def open_file(name):
    f = open(name, 'w')
    yield f
    f.close()
```

- 이 방식은 보다 직관적이다. 그러나 generator 와 yield, decorator 에 대한 제반 지식이 필요하다.
    * 간단히 설명하자면, **yield** 는 **generator** 에서 사용하는 키워드로,
    * return 키워드의 경우는 해당 값을 반환하고 함수를 종료해 버리지만
    * generator 내부의 yield 키워드는 해당 값 반환 후 다시 함수 내부로 되돌아온다.
    * yield 는 값을 내보내는 것뿐만 아니라 넣는 것도 가능하다.
    * **즉, 여러 번에 걸쳐 함수 입출력이 가능하게 된다!**
    * 그렇다면 이 기능을 어디에 사용할까?
        - 결론적으로 대용량 자료를 처리하는 함수가 필요할때 사용한다.
        - 예를 들어 파이썬에서 range() 함수는 내부적으로 입력된 수 만큼의 사이즈를 갖는 list 가 생성되는데,
        - 만약 대용량의 데이터라면 그만큼 무거운 list 가 만들어질 것이다.
        - 이 때는 한 번에 모든 데이터를 올려서 for-range 처리하지 말고,
        - yield generator 를 이용해서 한 줄씩 순차적으로 처리되도록 하면 시스템 부하를 그만큼 줄일 수 있다.
        - **실제로 Flask 웹앱에서 대용량 파일 전송, Sphinx 확장 개발 등에 사용된다.**
    * **decorator** 는 말 그대로 목적 함수를 꾸며주는? 역할을 한다. wrapping 이라고 부르는데
    * 여러 함수에 반복적으로 작성해야 할 로직 내용이 있다면 그것들을 일일히 코딩하는 것이 아니라
    * decorator 를 만들어 놓고 그것으로 감싸기만 하면 되는 것이다.
    * 목적 함수를 감싸는 역할이기 때문에 함수 중간에 끼어드는 것은 불가능하다.









