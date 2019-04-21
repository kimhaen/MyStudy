
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190421-lightgray.svg?style=flat-square)

## 스터디 둘째날

### 자바 및 웹 기초 훑어보기

- 자바에서 [Annotation](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98){: target="_blank"} 이란 무엇을 의미하는가?
	* 자바 소스 코드에 추가하여 사용할 수 있는 메타데이터의 일종 (위키백과)
	* 클래스 파일에 임베디드되어 컴파일러에 의해 생성된 후 JVM 에 포함되어 작동
- 자바 형변환과 parsing 의 차이점?
	* 형변환 : 숫자끼리의 형변환. 작은 사이즈 => 큰 사이즈 변환은 자동으로 이루어지지만 그 역은 명시적으로 해줘야 한다
	* 파싱 : 타입 자체가 아예 다를 때 사용. 예를 들어, 숫자로 보이지만 실제로 문자열인 경우 숫자형으로 변환시키는 것을 의미한다
	* 이유 => 결국 로직보다 중요한 것은 데이터이다
- 자바스크립트 dom 및 document 의 의미
	* [DOM](https://developer.mozilla.org/ko/docs/DOM){: target="_blank"} (Document Object Model)
		- html 이나 xml 문서에서 각 객체들을 처리하기 위한 api
		- 문서의 구조적 형태를 제공하므로 자바스크립트로 컨트롤 가능

### 스프링 시작!

- 메이븐
	* api 자동 등록? 자동 의존성 완성?
	* 본래 목적은 자동 배포에 있었다. maven => build 후 배포
	* dependancy 기능은 그 다음 문제였음
	* 그러나 의존성에 대한 부분이 워낙 강력해서 그렇게 이해하는 경우가 많다 (그래도 괜찮다).
	* 같은 라이브러리에 대해 버전 업을 한다면, 오래된 버전은 자동적으로 삭제된다.
		- 메이븐을 사용하지 않는다면, 수동으로 삭제해서 충돌하지 않도록 해야 한다
		- 혹은 메이븐과 별도의 API 를 동시에 사용하는데 같은 라이브러리 충돌이 일어날 가능성이 있다면,
		- build path 의 order and export 에서 우선순위를 적절히 조정해야 한다
- JDBC 로드 (mariaDB)
	* 원격 mariaDB 서버에 접속하기 위해 메이븐으로 적절한 버전의 JDBC api 를 로드한다
- DI (Dependency Injection)
	* 개념적 어려움이 있다면, 일단 몸으로 익힌 후 다시 접근하면 이해가 쉬워진다
	* 각 모듈 간의 의존성을 최소화하여, 추후 설계 및 모듈 정의가 변경되더라도 그 영향도가 최소화되도록 하는 것이 목적이다
	* 이것을 위해 개발적인 측면에서는 xml 설정 등 해야 할 작업이 더 많아지지만, 나중에 유지보수 측면에서는 굉장히 생산성이 좋다
- AOP
	* 반복되는 로직에 대해 중복 코드를 없애기 위해 해당 로직을 공통적으로 작성해서 그것이 요구되는 각 관점(Aspect)에서 실행시키는 것을 의미
	* 로그인 체크나 금융쪽 트랜잭션 컨트롤(commit and rollback), 보안 인증 시에 주로 사용된다






















