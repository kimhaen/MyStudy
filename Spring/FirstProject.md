
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190110-lightgray.svg?style=flat-square)

# 스프링 프로젝트 구성해보기

> 앞서 살펴본 것처럼 스프링 프로젝트는 IoC 컨테이너가 필요한 객체를 의존 관계에 따라 생성하고, 그것을 필요로 하는 Main 객체에 의존성 주입하는 방식으로 진행된다
또한, 개발자가 개발에만 집중할 수 있도록 자바 파일의 의존관계 및 필요한 모든 라이브러리에 대한 세팅 및 빌드를 대신 해주는 Maven 을 사용한다

## IoC 컨테이너 환경설정

- applicationContext.xml
- 환경설정을 위한 xml 파일이다. 이름은 다르게 할 수도 있지만, 공동작업을 위해 일반적으로 많이 사용하는 방식으로 정한다
- 이 IoC 컨테이너 xml 파일에서 생성 및 관리할 모든 객체들에 대한 정보를 세팅한다
- 여기서 생성 및 DI 되는 객체들은 기본적으로 싱글톤 패턴으로 관리되며 (scope="singleton", 생략), 범위 설정에 의해 프로토타입으로 정의할 수도 있다 (scope="prototype")
	- 기본값 싱글톤 패턴으로 관리되는 이유는 시스템 부하를 최소화하기 위해서이다.
	- 모든 클라이언트로부터 매 호출 시마다 객체를 새로 생성해야 한다면 부하가 커지지 않겠는가?
	- 즉, 서버에서 대량의 request 를 처리하기 위해 사용하는 방식이 싱글톤이다

### 싱글톤 패턴 주의할 점

- 일반적으로 static 키워드를 사용하는 전역 필드는 조심해서 사용해야 함. 모듈 간 결합도를 높일 여지가 있기 때문이다
- 싱글톤은 하나의 객체를 모두가 공유하고 한 번 생성되면 같은 컨테이너 내에서 재생성이 불가능하기 때문에 수정 및 테스트가 어렵다는 단점이 있다
- 그러나 스프링의 IoC 컨테이너는 적절하게 설계된 싱글톤 레지스트리이므로, 사용자 입장에서는 이러한 단점들을 크게 고려하지 않아도 된다 > 접근제한자 및 키워드 사용에 자유로움

## 실습

1. 기본 빈 클래스 정의 >> FirstProjectBean.java (src/main/java/spring.myFirstProject.FirstProjectBean.java)

```JAVA
package spring.myFirstProject;

import

public class FirstProjectBean () {

	private String id;
	private String pwd;

	public FirstProjectBean () { }
	public FirstProjectBean (String id, String pwd) {
		this.id = id;
		this.pwd = pwd;
	}

	public void setId (String id) {
		this.id = id;
	}
	public String getId () {
		return id;
	}
	public void setPwd (String pwd) {
		this.pwd = pwd;
	}
	public String getPwd () {
		return pwd;
	}

}
```

먼저 id 와 pwd 를 필드로 가지는 bean 클래스를 정의했다.<br/>
의존성을 고려하지 않았을 때는 메인 클래스에서 import 후 그냥 객체를 생성해 그 파일에서만 사용했지만 (의존성 높음),<br/>
IoC 컨테이너를 활용한 DI 방식을 사용한다면 재사용 가능한 싱글톤 객체를 주입받아 사용하게 된다<br/>
다음은 컨테이너 설정 파일 예제이다

2. IoC 컨테이너 환경설정 파일 예제 >> applicationContext.xml (src/main/resources/applicationContext.xml)

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="firstProjectBean01" class="spring.myFirstProject.FirstProjectBean">
		<property name="id">hong</property>
		<property name="pwd">1111</property>
	</bean>
</beans>
```

위 xml 코드에서 실제 객체를 생성하는 설정은 bean 태그이다. (지정한 class 의 JAVA Bean 생성, id 는 사용자가 임의 지정)<br/>
firstProjectBean01 라는 객체를 spring.myFirstProject.FirstProjectBean 자바 클래스 기반으로 생성한 것인데,<br/>
컨테이너가 싱글톤으로 관리하므로 같은 객체를 하나 이상 생성할 수 없다.<br/>
프로젝트를 실행하는 main 메서드에서는 이제부터 원하는 클래스를 직접 import 해 객체생성하여 사용하지 않고,<br/>
IoC 컨테이너를 생성 후 xml 파일에 지정된 이미 만들어진 객체를 주입받는다<br/>

3. IoC 컨테이너에 생성된 객체를 주입받아 확인해보기 >> MainClass.java (src/main/java/spring.myFirstProject.MainClass.java)

```JAVA
package spring.myFirstProject;

import org.springframework.context.support.GenericXmlApplicationContext;

public class MainClass {

	public static void main(String[] args){

		/*
		* 기존의 의존성 높은 방식
		*/
		// FirstProjectBean firstProjectBean01 = new FirstProjectBean();

		/*
		* IoC 컨테이너를 생성하여 필요한 객체를 주입받는 방식
		*/
		// 어플리케이션 컨테이너 객체를 위에서 생성한 컨텍스트 xml 파일에 맞춰서 생성 (이 xml 파일은 내가 설정한 개수만큼 콤마 이후로 추가할 수 있음)
		GenericXmlApplicationContext context = new GenericXmlApplicationContext("classpath:applicationContext.xml");
		// 컨테이너로부터 원하는 이름(위에서 객체 생성하면서 임의로 지정한 id)의 객체를 주입받는다
		FirstProjectBean firstProjectBean01 = context.getBean("firstProjectBean01", FirstProjectBean.class);
		FirstProjectBean firstProjectBean02 = context.getBean("firstProjectBean01", FirstProjectBean.class);

		// 주입받은 객체 데이터 확인
		for (int i = 0; i < 4; i++) {
			System.out.println("firstProjectBean01 의 id : " + firstProjectBean01.getId());
			System.out.println("firstProjectBean01 의 pwd : " + firstProjectBean01.getPwd());

			System.out.println("firstProjectBean02 의 id : " + firstProjectBean02.getId());
			System.out.println("firstProjectBean02 의 pwd : " + firstProjectBean02.getPwd());
		}
	}

}
```

메인 메서드에서는 먼저 IoC 컨테이너를 앞서 설정한 applicationContext.xml 기반으로 생성하고 그것으로부터 사전에 생성된 객체를 주입받는다.<br/>
거듭 말하듯이 컨테이너에서 하나의 bean 클래스는 기본적으로 싱글톤으로 관리되므로, 위 예제에서처럼 여러 객체 변수에 빈 객체를  담는다고 해도 궁극적으로 참조하는 빈 객체는 모두 동일하다.


참조: [전자정부 - IoC Container](http://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte2:fdl:ioc_container)






