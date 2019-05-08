
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190508-lightgray.svg?style=flat-square)

# Python 에서 날짜와 시간 다루기

참조 : [파이썬에서 날짜와 시간 다루기 - 데이터사이언스스쿨](https://datascienceschool.net/view-notebook/465066ac92ef4da3b0aba32f76d9750a/)

- 파이썬에서 기본적으로 날짜와 시간 관련 자료형은 없지만 그것들을 위한 모듈은 존재한다.
- [datetime](https://docs.python.org/2/library/datetime.html), [dateutil](http://dateutil.readthedocs.org/en/latest/index.html), [pytz](http://pythonhosted.org/pytz/), [time](https://docs.python.org/2/library/time.html) 모듈이 그것이다.
- 사실 datetime 클래스의 datetime 은 날짜 및 시간을 위한 자료형이다. (datetime.datetime)

## datetime 패키지

- datetime 패키지에서는 동일한 이름의 datetime class, 날짜를 다루는 date class, 시간을 다루는 time class, 시간 구간 정보를 다루는 timedelta class 가 존재한다.
- 이러한 클래스에는 클래스 메서드가 정의되어 있기 때문에 별도의 인스턴스를 생성하지 않고도 import 후 바로 해당 메서드를 사용할 수 있다. ([클래스 메서드란?](https://www.programiz.com/python-programming/methods/built-in/classmethod) / [클래스 메서드와 스태틱 메서드 비교](https://hamait.tistory.com/635))

### datetime class

- datetime 클래스의 **now()** 메서드는 그냥 실행하면 **년/월/일/시/분/초/마이크로초** 로 구성된 현재 시각을 반환한다.
- 인자를 순서대로 입력하면 그것에 해당하는 정보가 datetime 포맷에 맞게 반환된다.
- **weekday()** 메서드는 요일을 인덱스 넘버로 반환한다. (0~6 까지 월~일)
- **strftime()** 메서드는 datetime 타입 정보를 주어진 포맷에 따른 string 타입으로 변환한다.
    * 년-월-일 시:분:초 >>> %Y-%m-%d %H:%M:%S
    * 요일(영어), 월(영어) >>> %A, %B
- **strptime()** 메서드는 날짜시간 문자열 정보를 입력한 포맷에 따라 해석하여 datetime.datetime 타입으로 반환한다.

```python
from datetime import date, time, datetime

d = datetime.now()
d2 = datetime(2019, 5, 8, 15, 9, 55, 731039)
print(d)
print(type(d))
print(type(str(d)))
print(d2)
print(d.weekday())
print(d.strftime('(%B %A) %Y-%m-%d %H:%M:%S'))
print(d.strptime('2019-05-08 15:13:01', '%Y-%m-%d %H:%M:%S'))
```

- 콘솔 출력값은 다음과 같다.

```text
2019-05-08 16:16:42.003382
<class 'datetime.datetime'>
<class 'str'>
2019-05-08 15:09:55.731039
2
(May Wednesday) 2019-05-08 16:16:42
2019-05-08 15:13:01

Process finished with exit code 0
```

## dateutil 패키지

- **dateutil.parser** 클래스의 **parse()** 메서드는 별도의 포맷 지정 없이 형식 문자열을 datetime.datetime 타입으로 변환한다.
- 다만, 월과 일이 모두 12보다 작은 숫자일 때는 먼저 나오는 숫자를 월로 나중에 나오는 숫자를 일로 판단한다.

```python
>>> parse('2016-04-16')
datetime.datetime(2016, 4, 16, 0, 0)
>>> parse("Apr 16, 2016 04:05:32 PM")
datetime.datetime(2016, 4, 16, 16, 5, 32)
>>> parse('6/7/2016')
datetime.datetime(2016, 6, 7, 0, 0)
```

