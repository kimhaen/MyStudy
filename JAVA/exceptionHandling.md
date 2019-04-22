![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190421-lightgray.svg?style=flat-square)

참조 : 자바의 정석(남궁 성)

# 예외 처리

- **프로그램 오류**(에러)에는 **'컴파일 에러'**, **'런타임 에러'**, **'논리적 에러'**가 존재하며, 런타임 에러 중에는 복구할 수 없는 심각한 **'에러(error)'**와 비교적 덜 심각한(혹은 복구 가능한) **'예외(exception)'**가 존재한다
- 예외는 프로그램 컴파일 시에는 문제가 없지만, **실행 도중 발생할 수 있는 잠재적 오류**를 의미한다. 프로그래머는 사전에 **이를 핸들링하는 코드**를 작성함으로써 프로그램의 비정상적 종료를 방지할 수 있다

## 예외 클래스의 계층구조

- 자바에서는 이러한 **error** 와 **exception** (런타임 오류) 을 클래스로 정의하였는데, **Object 클래스** 하위에 **Throwable 클래스**가 런타임 오류의 최상위 클래스이고, 그 하위 **Exception** 과 **Error 클래스**로 분기된다
- Throwable 클래스 하위 계층구조는 다음과 같다

![Throwable 하위 계층](https://github.com/daesungRa/MyStudy/blob/master/imgs/java/exception01.png)

- 세부적으로 Exception 클래스 하위 계층구조는 다음과 같다

![Exception 하위 계층](https://github.com/daesungRa/MyStudy/blob/master/imgs/java/exception02.jpg)

- 결과적으로 Exception 클래스 하위 자손은 두 그룹으로 나눌 수 있는데,

```
1. **Exception 클래스 직계자손 클래스들**, 즉 IOException, ClassNotFoundException 등 RuntimeException 계를 제외한 클래스들과
2. **RuntimeException 계 자손 클래스**들이 그것이다.
```

- RuntimeException 클래스들은 주로 프로그래머의 실수(프로그래밍 요소)에 의해서 발생될 수 있다(IndexOutOfBoundsException, NullPointerException, ClassCastException, ArithmeticException).
- Exception 클래스들은 외부의 영향으로 발생될 수 있다. 주로 사용자들의 행위에 의해서 발생된다(FileNotFoundException, ClassNotFoundException, DataFormatException).

## 예외 처리하기 (try-catch 문)

- 프로그래머가 예외를 처리하는 방법은 **try-catch 문**을 사용하는 것이며, **catch 문 내부에 예외 발생 시를 대비한 코드를 작성**한다. 목적은 비정상 종료를 막고 정상적인 실행상태를 유지하는 것이다.
- catch 부분의 매개변수에는 발생 가능한 특정 예외 클래스를 받거나, 예외의 최상위 클래스인 Exception 클래스를 받도록 한다.
- **try-catch 문은 중첩 가능**하지만, 각 매개변수의 변수명은 달라야 한다
- 처리되지 못한 예외가 발생한다면, JVM 의 **'예외처리기(UncaughtExceptionHandler)'** 가 받아서 원인을 화면에 출력한다.
- 예외처리 흐름
	* 예외 발생 시 : try 문 실행 중 에러 발생 시, 해당 에러에 부합하는 catch 문을 찾아 실행 후 다음 로직을 이어간다. 부합하는 catch 문이 없다면 처리되지 못함.
	* 예외 발생하지 않을 시 : try 문이 정상 실행된 후 catch 문은 건너뛰고 다음 로직을 이어간다.
- catch 블럭 상세설명
	* 예외가 발생하면, 발생한 예외에 해당하는 클래스의 인스턴스가 만들어진다.
	* 해당 인스턴스와 일치하는(혹은 부모 클래스의) 종류의 참조변수가 정의된 catch 블럭을 위에서부터 아래로 검사한다.
	* 검사는 instanceof 연산자를 사용한다. 검사결과가 true 인 catch 블럭을 찾는다면, 그 내부의 모든 코드가 실행된다.
- **printStackTrace()** 와 **getMessage()**
	* 이 두 메서드는 생성된 에러 인스턴스에 포함된 **에러내용 확인**을 위해 쓰인다. 이를 위해 해당 catch 블럭에서 정의된 참조변수를 통해 인스턴스에 접근한다.
	* **printStackTrace()** : 예외발생 당시의 Call Stack 에 있었던 메서드의 정보와 예외 메시지를 화면에 출력
	* **getMessage()** : 발생한 예외클래스의 인스턴스에 저장된 메시지를 화면에 출력
- 멀티 catch 블럭
	* 다중 catch 블럭이 존재하면서, 각각 중복되는 코드가 존재한다면 '|' 기호를 이용해 하나의 catch 블럭으로 합칠 수 있다.
	* JDK1.7 부터 사용가능하며, '|' 는 논리연산자가 아니라 기호이다.
	* 연결된 예외 클래스 타입은 상호간에 독립적이어야 한다. 즉 상속관계라면 어차피 상위클래스만 코딩하면 되므로, 컴파일 에러 발생.
	* 또한 하나의 블럭만 사용하기 때문에 발생한 예외가 구체적으로 어떤 예외클래스인지 알 수 없다. 공통 분모인 조상 예외 클래스에 선언된 멤버만 사용할 수 있다.
	* 그러나 instanceof 연산으로 어떤 클래스인지 확인한 후 그에 맞게 형변환 시키면 자손 메서드도 사용 가능하다

## 예외 발생시키기

- 예외 객체를 새로 할당해 'throw' 키워드를 활용하면 인위적으로 예외를 발생시킬 수 있다.

```java
class ExceptionEx9 {
	public static void main(String[] args) {
		try {
			Exception e = new Exception("예외 발생시킴!");
			throw e;
			// throw new Exception("예외 발생시킴!");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```











