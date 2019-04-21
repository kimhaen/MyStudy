
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190421-lightgray.svg?style=flat-square)

## 스터디 둘째날

### 자바 및 웹 기초 훑어보기

- 자바에서 [Annotation](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98) 이란 무엇을 의미하는가?
	* 자바 소스 코드에 추가하여 사용할 수 있는 메타데이터의 일종 (위키백과)
	* 클래스 파일에 임베디드되어 컴파일러에 의해 생성된 후 JVM 에 포함되어 작동
- 자바 형변환과 parsing 의 차이점?
	* 형변환 : 숫자끼리의 형변환. 작은 사이즈 => 큰 사이즈 변환은 자동으로 이루어지지만 그 역은 명시적으로 해줘야 한다
	* 파싱 : 타입 자체가 아예 다를 때 사용. 예를 들어, 숫자로 보이지만 실제로 문자열인 경우 숫자형으로 변환시키는 것을 의미한다
	* 이유 => 결국 로직보다 중요한 것은 데이터이다
- 자바스크립트 dom 및 document 의 의미
	* [DOM](https://developer.mozilla.org/ko/docs/DOM) (Document Object Model)
		- html 이나 xml 문서에서 각 객체들을 처리하기 위한 api
		- 문서의 구조적 형태를 제공하므로 자바스크립트로 컨트롤 가능
- 최상위 Throwable 하위구조 살펴보기 (Exception + err ~)

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

### 스프링 실습

#### DI 실습 (Phone)

* 인터페이스의 사용 이유?
	- 프로젝트 아키텍쳐가 큰 틀(자바, mvc), 화면 구성 등을 만들고 각 개발자에게 복사해서 나눠준다
	- 자바 파일의 경우, 이때 인터페이스를 만들어서 각 메서드들을 구현하도록 강제한다
	- DI 를 위해서 특정 모듈이 인터페이스 파라미터를 주입받도록 설계하면, 그것이 구현된 모든 구현체들을 의존성 낮게 주입받아 사용할 수 있다
	- 예를 들어 Phone 인터페이스를 구현한 모든 구현체들을 원하는 대로 주입받아 사용할 수 있는 것
* 빈 설정 파일 만들기
	- appServlet 밑의 servlet-context.xml 을 src/main/resources 밑에 복사
	- 이름을 phonectx.xml 로 변경하고, 네임스페이스를 'beans' 만 두고 모두 해제
	- 불필요한 태그들을 제거하고 최상위 'beans:bean' 을 'bean' 으로 변경
	- Phone 인터페이스를 구현한 FeaturePhone 의 빈을 등록한다

**phonectx.xml**
```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://www.springframework.org/schema/beans"
	xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id='phone' class='com.steveleejava.springstep2.DI.FeaturePhone' />
	
</beans>
```

* 빈을 로드해 사용하는 메인 자바 파일 만들기
	- 메인 메서드에서는, ApplicationContext 구현체를 사용하여 phonectx.xml 컨텍스트 설정파일을 로드하고,
	- 등록되어 있는 빈을 Phone 인터페이스로 주입 받아 사용하게 된다
	- phonectx.xml 설정파일에서 'phone' 이라는 아이디로 등록된 빈을 변경함으로써 그것을 주입받아 사용하는 모든 모듈에 적용할 수 있다
	- ctx 구현체는 마지막에 close() 해줘야 한다

**MainClass.java**
```JAVA
package com.steveleejava.springstep2.DI;

import org.springframework.context.support.AbstractApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class MainClass {

	public static void main(String[] args) {
		AbstractApplicationContext ctx = new GenericXmlApplicationContext("classpath: phonectx.xml");
		Phone phone = ctx.getBean("phone", Phone.class);
		phone.use();
		
		ctx.close();
	}

}
```

* 아이폰으로 변경한 후 결과화면 (콘솔)

```
INFO : org.springframework.beans.factory.xml.XmlBeanDefinitionReader - Loading XML bean definitions from class path resource [phonectx.xml]
INFO : org.springframework.context.support.GenericXmlApplicationContext - Refreshing org.springframework.context.support.GenericXmlApplicationContext@ba8a1dc: startup date [Sun Apr 21 13:10:29 KST 2019]; root of context hierarchy
아이폰입니다. 시리야!
INFO : org.springframework.context.support.GenericXmlApplicationContext - Closing org.springframework.context.support.GenericXmlApplicationContext@ba8a1dc: startup date [Sun Apr 21 13:10:29 KST 2019]; root of context hierarchy

```

#### AOP 실습

* AspectJ Weaver 라이브러리 의존성 받기
* ~.AOP 패키지 하위에 관련 파일 작성
	- Supernaturalpowers.java(i), Jiminsuper.java, SeolHyensuper.java 는 만들어진 파일 받을 것
	- 변신하고 스킬 쓰고(on, off) 그런거를 구현한 것임
	- 나머지 LogAop.java, MainTest.java 파일은 직접 만들 것
* 빈 설정파일 만들기
	- DI 와 같은 방식으로 aopctx.xml 파일을 만들고, AOP 실행을 위한 LogAop.java 파일 만들기
	- LogAop.java 에서 최상위 Throwable 을 사용하되, 내부 catch 를 사용하지 못하는 이유는 Object 객체를 반환하도록 설계되어 있기 때문이다.
	- catch 내부에서 반환할 Object 가 존재할 수 없다

**aopctx.xml**
```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns="http://www.springframework.org/schema/beans"
	xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
	
	<bean id='logaop' class='com.steveleejava.springstep2.AOP.LogAop' />
	
	<aop:config>
		<aop:aspect id='logger' ref='logaop'>
			<aop:pointcut id='pointc' expression='within(com.steveleejava.springstep2.AOP.*)'></aop:pointcut>
			<aop:around pointcut-ref='pointc' method='loggerAoptest'></aop:around>
		</aop:aspect>
	</aop:config>
	
	<bean id='' class='com.steveleejava.springstep2.AOP.SeolHyensuper' />
	
</beans>

```

**LogAop.java**
```JAVA
package com.steveleejava.springstep2.AOP;

import org.aspectj.lang.ProceedingJoinPoint;

public class LogAop {

	public Object loggerAoptest (ProceedingJoinPoint joinpoint) throws Throwable {
		String signature = joinpoint.getSignature().toString();
		System.out.println(signature + " is start");
		long st = System.currentTimeMillis();
		
		try {
			Thread.sleep(1000);
			Object obj = joinpoint.proceed();
			return obj;
		} finally {
			long et = System.currentTimeMillis();
			System.out.println(signature + " id end");
			System.out.println(signature + " 경과시간 : " + (et - st));
		}
	}
}

```
























