
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-1905010-lightgray.svg?style=flat-square)

# 파이썬 OOP 개념, 클래스, 상속, 매직 메서드 정리

> 파이썬에서는 OOP 개념이 어떻게 구현될까? 클래스 및 상속 등은??

## package, class, 상속

- 파이썬에서 외부 모듈을 import 하기 위해서는 해당 패키지나 모듈에 초기화 세팅을 위한 **\_\_init__.py** 파일이 필요하다.
    * 만약 패키지라면, 이것을 import 하는 외부 프로그램에서 해당 패키지가 포함하는 여러 파일들을 사용할 수 있도록 하기 위해 \_\_init__.py 에 사용할 파일 목록을 등록시켜야 한다.
    * 예를 들어 character 패키지에 Babarian, Amazon, Paladin 클래스 파일들이 존재할 때, 각각을 명시적으로 import 하는 것이 아니라면 character 패키지의 \_\_init__.py 파일에서는 세 클래스 파일에 대한 등록이 이루어져야 한다.
    ```python
    # 해당 패키지 내 모든 모듈(*)로써 지칭될 모듈명을 변수에 정의
    __all__ = ["Babarian", "Amazon", "Paladin"];
    ```
- 클래스도 마찬가지로 import 후 인스턴스화 하거나 상속을 해야 하므로 \_\_init__ 메서드가 필요하다. (이는 생성자를 말하는 것이다.)
- **\_\_init__(self)**
    * 작성하고자 하는 클래스의 생성자. 이곳에 생성 시 필요한 로직을 작성한다.
    * 별다른 로직이 없다면 pass 키워드로 작성만 해놓으면 된다.
    * 첫 번째 매개변수로 **self** 객체를 받는데, 이것은 인스턴스화 하고자 하는 해당 클래스 자신을 의미한다. (사용자가 인지할 수는 없지만 내부적으로 인스턴스화가 진행되고 init 이 호출되는 듯?)
    * 반환형은 사실상 없다. 굳이 말하자면 **None** 이 반환된다.
    * 만약 최상위 클래스인 object 클래스를 상속받는다면, \_\_init__() 메서드의 작성은 메서드 재정의 (override) 가 될 것이다.
- 상속 (inheritance)
    * 파이썬에서 클래스 상속 시에는 해당 클래스를 import 해야 한다.
    * 자바에서는 extends 키워드를 사용했지만, 파이썬에서는 상속받는 클래스의 매개변수로 부모 클래스를 전달받는다. (자바 클래스는 매개변수를 받지 않음)
    * 이후 구현하는 자식 클래스는 부모 클래스의 필드와 메서드를 사용하거나 상속받을 수 있다.
- 접근제한자
    * 파이썬에서 **접근제한자**의 경우 언더바(_) 를 사용하는데, 접두사로 언더바 한 개라면 **protected**, 두 개라면 **private** 이다. (접미사로 언더바는 한개까지만 허용)
    * 만약 접미사에 언더바가 두개 이상이라면 **public** 이 된다.
    * 예를 들어 **\_\_name** 은 **private** 변수를 의미하지만, **\_\_name\_\_** 은 단순히 내장 변수를 의미한다.
- 다형성 (polymorphism)
    * Overload
        - 파이썬에서는 매개변수로 가변인자를 적용할 수 있다. asterisk (*) 가 그것인데, 용례를 보면 다음과 같다.
        ```python
        class Test:
            def add(self, *num):
                total = 0
                 
                if num and type(num[0] is int):           
                    for item in num:
                        total += item               
                return total
             
        test = Test()
        print(test.add(10, 20))
        print(test.add(10, 20, 30))  
        ```
        - 자바에서처럼 매개변수 개수나 타입으로 구분하고자 한다면, 나중에 정의된 동일 이름의 메서드가 이전 것을 덮어쓰게 될 것이다. (파이썬은 인터프리터 언어이다)
    * Override
        - 부모 클래스를 넘겨받는 방식으로 상속된 자식 클래스라면 동일 이름의 메서드를 구현함으로써 재정의가 가능하다.
## Magic Method

- [python-course : magic method](https://www.python-course.eu/python3_magic_methods.php) (번역)
```text
소위 말하는 'magic methods' 는 마법과는 관련이 없다.
그것은 '__init__' 과 같이 names 를 double underscore 가 앞뒤로 붙어있게 변형함으로써 만들어지는 특별한 메서드다. 이러한 것을 뭐라고 불러야할지 명확하지는 않다.
어쨌든 '__init__' 메서드의 마법은 무엇일까? 그 답은, '그것을 직접적으로 호출하지 않아도 되는' 것에 있다.
호출은 뒷편에서 이루어진다. 예를 들어,
당신이 A 라는 클래스의 인스턴스 x 를 "x = A()" 방식으로 만든다고 할때 파이썬은 __new__ 와 __init__ 메서드를 필수적으로 호출한다.
우리는 __call__ 메서드를 살펴볼 것인데, 그것은 많은 언어들이 가지고 있지 않은 파이썬만의 기능을 가지고 있다.
__call__ 메서드는 인스턴스가 함수처럼 작동하는 클래스를 작성할 수 있도록 한다. 그러한 클래스의 함수와 인스턴스들은 'callables' 로 불린다.

우리는 'operator overloading(연산자 오버로딩)' 이라는 컨셉을 많이 접해왔는데, 숫자 값을 더하는 것뿐 아니라 문자열 및 list 도 더할 수 있는 'plus sign (+)' 이 대표적이다.
```

```python
>>> 4 + 5
9
>>> 3.8 + 9
12.8
>>> "Peter" + " " + "Pan"
'Peter Pan'
>>> [3,6,8] + [7,11,13]
[3, 6, 8, 7, 11, 13]
```

```text
이것은 심지어 당신의 클래스의 목적에 맞게 다른 연산자들을 그렇게 했던 것처럼 '+' 연산자 또한 오버로드가 가능하게 한다. (사용자 정의가 가능하다는 뜻)
이를 위해 당신은 'underlying mechanism' 을 이해할 필요가 있다.
앞서 언급한 것을 예로 들면, '+' 기호에 맞는 magic method 는 '__add__' 메서드이다. (마찬가지로 '-' 기호에 맞는 것은 '__sub__' 메서드이다.)

메카니즘은 다음과 같다.
만약 "x + y" 연산을 수행할 때 'x' 가 클래스 'K' 의 인스턴스라면, python 은 클래스 'K' 의 정의를 확인한다.
'K' 가 __add__ 메서드를 가지고 있다면, 그것은 이제 "x.__add__(y)" 형태로 호출된다. 만약 가지고 있지 않다면 에러 메시지가 발생한다.
``` 

대략적으로 정의된 '+' 연산자가 없다는 의미
```python
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'K' and 'K'
```

- 지금까지의 설명이 magic method 에 대한 전부이며, '+' 연산자 뿐만 아니라 다른 사례들도 살펴보며 익혀나가면 된다.
- 다른 사례들은 참조문서의 중반부에 있다 > [참조문서](https://www.python-course.eu/python3_magic_methods.php)




