![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190421-lightgray.svg?style=flat-square)

참조 : 자바의 정석(남궁 성)

# 예외처리

- **프로그램 오류(에러)**에는 **'컴파일 에러'**, **'런타임 에러'**, **'논리적 에러'**가 존재하며, 런타임 에러 중에는 복구할 수 없는 심각한 **'에러(error)'**와 비교적 덜 심각한(혹은 복구 가능한) **'예외(exception)'**가 존재한다
- 예외는 프로그램 컴파일 시에는 문제가 없지만, **실행 도중 발생할 수 있는 잠재적 오류**를 의미한다. 프로그래머는 사전에 **이를 핸들링하는 코드**를 작성함으로써 프로그램의 비정상적 종료를 방지할 수 있다

## 예외 클래스의 계층구조

- 자바에서는 이러한 **error** 와 **exception** (런타임 오류) 을 클래스로 정의하였는데, **Object 클래스** 하위에 **Throwable 클래스**가 런타임 오류의 최상위 클래스이고, 그 하위 **Exception** 과 **Error 클래스**로 분기된다
- Throwable 클래스 하위 계층구조는 다음과 같다

![Throwable 하위 계층](https://github.com/daesungRa/MyStudy/blob/master/imgs/java/exception01.PNG)

- 세부적으로 Exception 클래스 하위 계층구조는 다음과 같다

![Exception 하위 계층](https://github.com/daesungRa/MyStudy/blob/master/imgs/java/exception02.jpg)

- 결과적으로 Exception 클래스 하위 자손은 두 그룹으로 나눌 수 있는데,
	* **Exception 클래스 직계 자손들**, 즉 IOException, ClassNotFoundException 등 RuntimeException 계를 제외한 클래스들과
	* **RuntimeException 계 자손 클래스**들이 그것이다.













