
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190510-lightgray.svg?style=flat-square)

# 파이썬 자료형 정리 - list, set, tuple, dictionary

참조 : [파이썬 - 기본을 갈고닦자!](https://wikidocs.net/book/1553)

## 배열과 유사한 - list

- index 단위의 순서가 있는 mutable 한 객체의 집합. 객체 추가 및 삭제도 가능함.
- 인덱싱은 마지막 인덱스부터 참조하는 음수 방식도 허용함. (단, -1 이 첫 번째 인덱스)
- 여러 객체 타입을 함께 저장 가능. (string, int, boolean 등)
- list 선언
    * 임의의 참조변수에 리스트 타입 선언은 list() 혹은 [] 로 가능.
- list 원소 추가
    * ```.append(값)``` 는 뒤에 하나씩 추가, ```.insert(인덱스, 값)``` 는 원하는 인덱스에 하나씩 추가, ```.extend(추가할리스트)``` 는 뒤에 리스트 추가.
    * list 간 '+' 연산으로 합치는 것이 가능. (\_\_add__ 실행)
- list 원소 삭제
    * ```del list[인덱스]``` 혹은 ```list.remove(인덱스)``` 로 해당 인덱스의 원소 삭제.
- string 을 list 로 변환할 수 있다. 그 결과는 각 문자 하나가 list 원소 하나로 됨.
    * string.split() 은 string 을 list 로 변환한 결과와 같다.

```python
# list 선언, 여러 타입 데이터 추가, list 더하기 연산
>>> a = [] # a = list()
>>> a.append(1)
>>> a.append(3)
>>> a.append(5)
>>> a.append(7)
>>> a = a + ['one', 'two', 'three']
>>> a = a + [True, False,]
>>> a
[1, 3, 5, 7, 'one', 'two', 'three', True, False]

# string 을 list 로 변환
>>> str = '안녕하세요'
>>> strList = list(str)
>>> strList
['안', '녕', '하', '세', '요']
```

- **list 비교**
    * ```==``` 로 비교한다면 리스트 내부의 값을 비교하고, ```is``` 키워드로 비교한다면 객체 자체가 동일한지 비교한다.
    * 내부 값이 같아도 객체 자체는 다를 수 있다. (할당된 주소가 다름)
        - 객체 주소를 알고 싶다면 **id(객체주소)** 함수를 호출하면 된다
- **list slicing**
    * 사용법은 ```리스트변수[시작인덱스:종료인덱스:step]``` 이다.
    * 시작인덱스부터 종료인덱스 이전 인덱스까지 포함한다.
    * 이것들은 생략 가능한데, 시작인덱스가 생략되었다면 처음인덱스부터 종료인덱스까지를 의미하고
    * 종료인덱스가 생략되었다면 시작인덱스부터 끝까지를 의미한다.
    * step 은 기본 1 이다.
- **list sort**
    * ```list.reverse()``` 는 거꾸로 뒤집는다.
    * ```list.sort()``` 는 기본 오름차순 정렬, ```list.sort(reverse=True)``` 는 내림차순 정렬
    * ```list.sort(key=len)``` 은 정렬하되 원소의 길이에 따라 오름차순으로 정렬된다.
    * 원본은 변형하지 않고 정렬된 결과를 반환하려면?
        - ```sorted(list)``` 혹은 ```reversed(list)``` 하면 된다.
        - reversed 의 경우 iterable 한 객체를 반환하기 때문에 list 로의 변환이 추가적으로 필요하다.
- **shallow copy**(얕은 복사) 와 **deep copy**(깊은 복사)
    * 얕은 복사 : 특정 리스트에 대한 참조변수는 그 리스트의 객체주소값을 저장하고 있기 때문에, 해당 참조변수를 다른 변수에 대입하는 식으로 얕은복사를 수행한다면 동일한 주소값을 갖게 된다.
    * 깊은 복사 : 단순 대입이 아니라 ```list명.copy()``` 혹은 ```copy.deepcopy(list명)``` 함수를 호출하면 깊은 복사가 이루어진다. 즉, 동일한 값을 갖지만 서로 다른 객체 주소값을 갖는 것이다.
    ```python
    import copy;

    def shallowCopy():
        print("=== 얕은복사 시작 ===");
        a = dict([["first", 1], ["second", 2], ["third", 3]]);
        b = a;
        b["fourth"] = 4;
        print("a(" + str(id(a)) + ")", ":", a);
        print("b(" + str(id(b)) + ")", ":", b);
        print("=== 얕은복사 종료 ===");
    
    def deepCopy():
        print("=== 깊은복사 시작 ===");
        a = dict([["first", 1], ["second", 2], ["third", 3]]);
        b = a.copy();
        b["fourth"] = 4;
        print("a(" + str(id(a)) + ")", ":", a);
        print("b(" + str(id(b)) + ")", ":", b);
        print("=== 깊은복사 종료 ===");
    
    def deepCopy2():
        print("=== 깊은복사 시작 ===");
        a = dict([["first", 1], ["second", 2], ["third", 3]]);
        b = copy.deepcopy(a);
        b["fourth"] = 4;
        print("a(" + str(id(a)) + ")", ":", a);
        print("b(" + str(id(b)) + ")", ":", b);
        print("=== 깊은복사 종료 ===");
    
    def copyTest():
        while True:
            print("1. 얕은복사\n2. 깊은복사\n3. 종료");
            flag = (input(" >> "));
    
            if flag == '1':
                shallowCopy();
            elif flag == '2':
                deepCopy();
            elif flag == '3':
                print("프로그램을 종료합니다...");
                break;
            else:
                print("잘못된 입력입니다.");
    
    copyTest();
    ```
    * 결과
    ```
    1. 얕은복사
    2. 깊은복사
    3. 종료
     >> 1
    === 얕은복사 시작 ===
    a(7701152) : {'first': 1, 'second': 2, 'third': 3, 'fourth': 4}
    b(7701152) : {'first': 1, 'second': 2, 'third': 3, 'fourth': 4}
    === 얕은복사 종료 ===
    1. 얕은복사
    2. 깊은복사
    3. 종료
     >> 2
    === 깊은복사 시작 ===
    a(7815216) : {'first': 1, 'second': 2, 'third': 3}
    b(7701152) : {'first': 1, 'second': 2, 'third': 3, 'fourth': 4}
    === 깊은복사 종료 ===
    ```
- 그냥 참고로, 마지막 원소에 콤마를 붙여도 상관은 없다.

## 단일 원소 집합인 - set, tuple

- set 은 중괄호 **{}**, tuple 은 소괄호 **()** 형태로 만들어진 단일 원소 집합이다.
- 이들도 마찬가지로 여러 타입의 원소를 함께 저장할 수 있다.
- **set**
    * set 은 순서가 없이 하나의 원소에 하나의 자료형이 들어가는 집합 형식이다.
    * 각 원소는 **unique** 하며 **mutable** 한 특성을 갖는다.
    * 중복된 값은 자동으로 중복 제거된다.
    * set 의 선언 : dict 와 같이 중괄호를 사용하기 때문에 선언 시 명시적으로 set 생성자를 활용해야 한다. (**set()**)
    * 특정 원소 존재유무를 알기 위해 ```in``` 과 ```not in``` 키워드로 boolean 타입의 결과를 얻는다.
    * ```add(), update(), remove() discard()``` 등의 함수를 사용한다.
- **tuple**
    * tuple 은 순서가 있으면서 immutable 한 특성을 갖는다. (변경이 불가능)
    * 원소가 하나만 있다면 튜플이 되지 않지만, 원소 뒤에 콤마를 찍는다면 tuple 형태를 유지할 수 있다.
    * tuple 선언 및 초기화 시 꼭 소괄호 () 를 입력하지 않아도 된다.
    * tuple 을 선언하며 list 를 인자로 넘겨주면 그 내용에 맞는 tuple 이 생성된다.
    * tuple 로 해체할당이 가능하다. 이것을 이용해 두 변수의 값을 바꿀 때 매개체인 temp 변수 없이 곧바로 가능하다. 관련 내용은 더 찾아볼 것!

## key-value 매핑 자료형의 대표주자 - dictionary

- **key** 는 **immutable** 하고, **value** 는 **mutable** 하다.
    * key 에 mutable 한 객체를 넣을 수 없다. 추가 혹은 제거만 가능하다.
    ```python
    # mutable 예
    >>> a = { {1, 3}: 5, {3,5}: 3}     #set 사용 에러
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: unhashable type: 'set'
    >>> a = {[1,3]: 5, [3,5]: 3}     #list 사용 에러
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: unhashable type: 'list'
    >>> a = { {"a":1}: 5, "abc": 3}     #dict 사용 에러
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: unhashable type: 'dict'
    ```
    * 중복된 key 가 존재한다면 나중 값이 이전 값에 덮어씌워진다.
    ```python
    >>> {"a" : 1, "a":2}
    {'a': 2}
    ```
- 기본 형태는 ```{'a': 1, 'b': 2, 'c': 3}``` 와 같다.
- 순서가 없기 때문에 특정 원소에 접근하려면 key 를 활용해야 한다.
    * key 로 접근하여 mutable 한 value 를 수정할 수 있다.
- list 속에 list 나 tuple, tuple 속에 list 나 tuple 을 원소로 넣는다면 dict() 생성자로 dictionary 자료형으로 변형할 수 있다.
- dictionary 의 반복문
    * for 문과 사용하면 key 가 할당된다.
    * 이때, 꼭 순서대로 할당된다는 보장은 없다.
    * ```dict.values()``` 를 사용하면 값이 할당되고, ```dict.items()``` 를 사용하면 key 와 value 모두 할당된다.
    ```python
    >>> for val in a.values():
    ...     print(val)
    ... 
    [1, 2, 3]
    20
    15
    30    
  
    >>> for key, val in a.items():
    ...     print("key = {key}, value={value}".format(key=key,value=val))
    ... 
    key = alice, value=[1, 2, 3]
    key = bob, value=20
    key = tony, value=15
    key = suzy, value=30
    ```
- dictionary 읽기
    * dict 는 여러 자료형을 key-value 형식으로 저장하기 때문에 데이터가 조금만 많아져도 읽기 복잡해진다.
    * 이때 `pprint` 모듈을 사용하면 읽기 쉽게 표현해준다.
    ```python
    >>> from pprint import pprint as pp
    >>> a = {'alice': [1, 2, 3], 'bob': 20, 'tony': 15, 'suzy': 30,"dodo": [1,3,5,7], "mario": "pitch"}
    >>> print(a)
    {'alice': [1, 2, 3], 'bob': 20, 'tony': 15, 'suzy': 30, 'dodo': [1, 3, 5, 7], 'mario': 'pitch'}
    >>> pp(a)
    {'alice': [1, 2, 3],
     'bob': 20,
     'dodo': [1, 3, 5, 7],
     'mario': 'pitch',
     'suzy': 30,
     'tony': 15}
    ```



