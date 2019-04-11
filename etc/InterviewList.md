
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190401-lightgray.svg?style=flat-square)

# 자바 웹개발자 면접질문 리스트

## 목차

1. [자바 기초](#Basic)
2. [자바 심화](#Intensive)
3. [웹기술 전반](#Web)
4. [JSP / Servlet](#JSP/Servlet)
5. [Spring](#Spring)
6. [Oracle, MySql](#Database)
7. [프론트 기술](#Front)
8. [알고리즘](#Algorithm)
9. [프로젝트 설명](#Project)
10. [컴공 기초](#Computer)
11. [리눅스](#Linux)
12. [네트워크](#Network)
13. [기타](#Other)

## Basic
[목차로](#목차)

- 자바의 특징
	* 선 마이크로시스템즈에서 95년 **제임스 고슬링**을 필두로 만들어짐. 2010년 오라클에 인수됨
	* 대표적인 객체지향언어. 그러나 완벽한 객체지향은 아니다. **Primitive 타입(기본형)은 객체로 취급하지 않기 때문**이다.
	* 기본형 데이터를 객체로 취급하기 위해 **Wrapper 클래스를 활용**한다. 재사용과 형변환을 위해 활용. 오토박싱과 오토언박싱 적용됨
	* [JVM](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EA%B0%80%EC%83%81_%EB%A8%B8%EC%8B%A0), 즉 자바 가상 머신을 활용해 운영체제에 독립적
	* JVM 은 JRE(자바 런타임)에 포함되어 실행하고자 하는 운영체제에 간편설치 가능
	* JDK 는 자바 개발 도구이며, JVM 은 개발된 코드를 클래스 파일(byte)로 변환해 직접 실행한다.
	* 정확히는 javac 컴파일러가 바이트 코드로 변환하고 java classloader 가 JVM 에 적재한다
	* [GC](https://d2.naver.com/helloworld/1329), 즉 가비지 컬렉션은 참조되지 않는 메모리(HIP) 영역을 우선순위에 따라 회수하는 작업을 수행하며, FULL GC 는 stop-the-world 방식(전체 실행 중지)으로 이루어지기 때문에 성능을 고려한다면 이를 GC 튜닝이 필요하다. 그렇다고 stop-the-world 를 아예 안할 수는 없다
	* JVM 이라는 매개가 존재하기 때문에 언어 자체의 무게감이나 실행속도의 측면에서 상대적으로 느리지만, 일반적인 영역에서 체감할 정도는 아니다
	* 이식성, 호환성이 높고 C 계열과 문법상 유사해 진입장벽이 낮고 대중적
	* 비교적 하이 레벨 언어기 때문에 생산성이 높다
	* C 계열 언어와의 차이점 : C 는 포인터 등을 활용한 로우 레벨 언어로, 객체지향 개념을 포함하는 C++ 은 디자인적 측면에서 굉장히 복잡해지는 결과가 나타났으나, 자바는 애초에 이를 과감히 포기하고 GC 를 내장한 하이 레벨(상대적) 언어로 복잡성을 해소하였다.
- OOP 란?
	* 객체지향프로그래밍은 각 데이터를 특정 기준에 따라 유사성을 지닌 객체(Object)로 묶어 각 객체 간 상호작용을 통해 작업을 수행한다
	* 유연하고 변경이 용이(재사용성, 변경 가능성) > 대규모 소프트웨어 개발에 용이
	* 상속으로 인해 코드의 재사용성, 생산성이 높다
	* 보다 직관적인 코드
	* 지나친 객체화는 현실을 제대로 반영하지 못하기도 한다
	* 캡상추다
		- 캡슐화 : 관련된 데이터와 동작을 하나의 객체로 묶는 것
		- 상속 : 자손 클래스가 부모 클래스의 데이터와 동작을 그대로 사용 가능. 수정 및 추가적인 확장도 가능. 자바에서 다중상속은 불가하나, 인터페이스의 다중 구현(implements)은 가능하다
		- 추상화 : 불필요한 정보는 숨기고 중요한 정보 혹은 동작의 결과를 표현함으로써 외부에서 보기에 간단하게 사용 가능한 모듈로 만드는 것. 정보은닉과 접근 제어 특성과 연관됨
		- 다형성 : 같은 동작을 여러 방식으로 표현하는 것. 자식 클래스는 Override 기능으로 부모의 메서드를 각각 다른 방식으로 재정의 가능하다
	* 소프트웨어 공학 관점에서 응집력을 높이고 결합력을 줄이는 방향성을 갖는다
- 자바(JVM) 메모리 영역
- 클래스와 객체, 인스턴스
- Call by reference, Call by value
	* Call by reference : 주소값으로 호출, 주로 할당된 객체의 주소값을 활용한다
	* Call by value : 기본형 값으로 호출, byte, int, double 등과 같은 기본형의 값을 그대로 활용한다
- Overloading, Overriding, Constructor
- equals, hashCode 재정의 이유와 방법
- static, final, abstract
- 추상 클래스와 인터페이스
	* 추상클래스
		- abstract 키워드로 정의하고 extends 키워드로 상속
		- 말 그대로 추상이므로 인스턴스화가 불가능함
		- 하나 이상의 추상 메서드가 있어야 한다(내용이 없는 메서드)
		- 추상 메서드는 반드시 재정의된다
		- 일반 메서드도 포함 가능하다. 이 메서드는 자손 클래스가 재정의 없이 사용 가능하다
		- 자바에서는 다중상속은 불가하다
		- OOP 의 특성인 상속만을 위한 개념이라고 생각하면 됨
	* 인터페이스
		- interface 키워드로 정의하고 implements 키워드로 구현
		- 추상클래스는 어찌되었든 클래스의 일종인 반면(부모 클래스의 특성을 가짐), 인터페이스는 그보다 더 추상적이다
		- 공산품으로 치면 제품의 겉껍데기나 틀을 정의한 것이 인터페이스이며(표준화) 각 공정에서는 이것을 기반으로 제품을 생산하는 것으로 보면 된다
		- 오직 추상메서드로만 구성되며(아예 아무것도 없거나), 상수나 전역변수를 포함할 수 있다(메서드 영역에 할당됨)
		- 구현체는 여러 인터페이스로부터 다중구현이 가능하며, 추상메서드를 반드시 재정의해야 한다
- 예외처리

## Intensive
[목차로](#목차)

- 컬렉션 프레임워크
	* 컬렉션이란 용어는 데이터들을 담는 그릇 혹은 그러한 컨테이너의 개념을 함의한다
	* 순서가 있는 컬렉션은 인덱스로 접근 가능하지만, 순서가 없다면 Iterator 를 활용해 값에 접근한다
	* Collection 인터페이스의 구현체라면 모두 iterator() 메서드 사용이 가능하다(재정의)
	* 정해진 입력 자료형이 없어 매개변수나 반환값은 모두 Object 이므로 직접 형변환이 필요하다
	* 컬렉션을 사용한다면 정해진 자료형을 명시적으로 사용할 수 있다(더 명료해짐)
	* Array 와 ArrayList 비교
		- Array 는 기본 배열로 한번 할당된 사이즈 변경이 불가, ArrayList 는 List 컬렉션의 일종으로 사이즈 변경 가능
		- Array 는 사이즈 변경이 없는만큼 속도가 빠르고, ArrayList 는 그만큼 느리다
		- 순서가 있는 인덱스로 접근 가능. 전자는 a[i] 방식, 후자는 a.get(i) 방식
	* **List**
		- 순서가 있는(인덱스) 컬렉션, 데이터 중복 가능
		- LinkedList
		- Stack
		- Vector
			* 동적 배열(동적 사이즈)
			* 인덱스 접근가능
			* 동기화 > 한 번에 한 스레드만 접근가능
		- ArrayList
			* 동적 배열(동적 사이즈)
			* 동기화되지 않음. 동기화를 위해서는 명시적으로 코딩해야 함
		- Vector 와 ArrayList 비교
			* V 는 동기화되므로 스레드 안전함, AL 은 동기화가 없어 상대적으로 속도가 빠르다
			* 사이즈 증가 시 각각 100%, 50% 씩 증가한다
			* 특별히 동기화의 필요가 없다면 일반적으로 ArrayList 를 사용한다
	* **Set**
		- 집합 개념, 순서 없이 단순 데이터 저장 컬렉션, 데이터의 중복을 허용하지 않는다
		- HashSet
		- SortedSet
	* **Map**
		- Key 와 Value 로 이루어진 쌍의 집합, 순서는 존재하지 않으며 키는 중복 불가, 값은 중복 가능
		- 값을 가져올 때 Map 컬렉션은 Iterator 사용이 불가하므로, 일반적으로 Key 값으로 이루어진 Set 을 추출해 활용한다
		- HashTable
			* put, get 메서드
			* 동기화 적용 (synchronized 키워드)
			* key, value 에 null 을 허용하지 않는다
		- HashMap
			* HashTable 과 동일하나 동기화가 적용되지 않음
			* null 입력 가능
			* 빈번히 사용됨
		- ConcurrentHashMap
			* HashMap 에 동기화가 적용. thread-safe 하도록 만들기 위함
			* null 을 허용하지 않는다
		- SortedMap
- StringBuffer 와 StringBuilder 의 차이
	* 먼저, String 은 사이즈 변경 및 + 연산 시 새로운 주소에 새롭게 할당되지만, StringBuffer 와 StringBuilder 는 concat 시 동일한 주소에 append 된다
	* 특별한 이유(고성능 고려)가 아닌 이상 일반적으로 String 을 사용한다
	* StringBuffer
		- 동기화 지원, 모든 메서드에 synchronized 키워드가 붙는다
	* StringBuilder
		- 동기화 지원하지 않음
	* 동기화를 지원한다는 것은 그만큼 스레드 안전하지만, 속도가 느리다는 것
	* 그 외에 두 클래스는 모든 면에서 동일하다
- 대표적인 디자인패턴
	* Singleton
	* Factory
- 제네릭, enums, iterator, annotation
- 프로세스와 스레드, 데몬
- 스레드풀
- 람다
- 스트림(스트림 사용시 문제점은?)
- Synchronization, Serialization
- IO
- Java Networking, TCP/UDP
- 소켓 프로그래밍

## Web
[목차로](#목차)

- HTTP 프로토콜
- 쿠키와 세션
- Get, Post 방식 (+@)
- Request, Response
- 웹 프로젝트 기본구조
- 프로젝트 개발 순서(소프트웨어 공학)
- MVC 패턴
- Restful

## JSP/Servlet
[목차로](#목차)

- JSP
- Servlet
- Container 란?

## Spring
[목차로](#목차)

- 스프링 프레임워크란?
	* 자바 엔터프라이즈급 프로젝트를 구축하기 위한 오픈소스 기반 경량 프레임워크(EJB 에 비해 경량). 개발 전반의 다양한 개념과 기능들을 포괄한다
	* IoC, DI, AOP 등의 기능이 대표적이다
- IoC, DI
	* 각각 **Inversion of Control**, **Dependency Injection** 의 줄임이다
	* **제어의 역전**. 기존에는 객체를 생성함에 있어서 거의 모든 제어권이 동작하는 어플리케이션에게 있었으나, **IoC 패턴**은 해당 제어권을 컨테이너에 위임함으로써 모듈 간 의존성을 느슨하게 한다
	* 즉, 스프링을 비롯한 IoC 패턴이 적용된 프레임워크의 궁극적인 컨셉은 모듈 간 **의존성이나 결합도를 낮추는 것**에 있다
	* **DI** 는 IoC 컨셉의 한 방식으로 new 연산자를 이용해 직접 생성하던 객체를 **Interface 와 구현체 주입 방식**으로 얻어와 보다 확장성과 생산성이 보장되도록 활용하는 것을 말한다
	* 비유적으로 자동차 공장의 바퀴를 하나의 객체로 생각한다면, 기존에는 직접 바퀴를 생산했지만 DI 이후에는 규격에 맞는 여러 종류의 바퀴를 외부로부터 주입받아 조립하는 것을 의미한다
- AOP, AspectJ
- 스프링에서 싱글톤 패턴을 사용하는 이유
	* 가장 표면적인 이유는, 엔터프라이즈급 프로젝트에서 사용자 수가 기하급수적으로 증가함에 따라 발생하는 시스템 부하를 최소화하기 위함이다
	* 오직 하나의 인스턴스만 생성하므로 효율적이지만, 그만큼 공유 시 변화 가능성이 높아 안정성이 보장되지 않는다
	* 특별한 상황에서 구체적인 이유가 존재할 때 보수적으로 사용할 것
- Filter, Interceptor

## Database
[목차로](#목차)

- DBMS 란?
- ERD 란?
- UML 이란?
- 정규화(Normalization) 의 정의와 사용 이유, 장단점
	* 데이터 중복 및 컬럼 간 종속 문제를 해결하기 위해 사용
	* 각종 이상현상을 방지할 수 있으나, 데이터 검색 시 잦은 조인으로 인해 속도가 상대적으로 느릴 수 있다
- 무결성
- Sequence, Auto-Increment
- join
	* inner join
	* equi join
	* non-equi join
	* outer join
	* self join
- 오라클 join 과 표준 join 의 문법적 차이
- index
- 클러스터 인덱스와 넌 클러스터 인덱스
- 클러스터링과 리플리케이션의 차이
- Java 에서 데이터베이스 커넥션 방법
- JDBC 와 ODBC
- view 설명, 사용이유
- 이상현상의 종류 및 설명
- 정규화와 역정규화
- Trigger
- Data Warehouse 란?
- Oracle SQL 과 MySql 의 차이

## Front
[목차로](#목차)

- HTML, CSS
- Javascript, jQuery
- Ajax
- Responsive web

## Algorithm
[목차로](#목차)

- 구구단 작성하기
- 기본 정렬 알고리즘
	* Quick Sort
	* Merge Sort
	* Bubble Sort
	* Heap Sort
- 기본 자료구조 알고리즘
	* Array
	* Stack
	* Queue
	* Hash Table
	* Linked List
	* Tree
	* Heap
- 스레드가 3개 생성되었을 때, t1, t2, t3 의 순서가 보장되는 코드 작성
- pivotal(대각선이 고정인 행렬) 3x3, 4x4 를 뒤집는 로직 작성하기. 재귀 사용
- 10 칸짜리 배열로 스레드 세이프한 Queue 만들기
- Factorial 을 재귀로 작성하기
- 인덱스가 존재하는 정렬된 배열 x 에서 x[i] = i 인 인덱스 i 를 찾는 가장 최적화된 방법은?
	* 처음부터 탐색하는 방식(O(n))
	* binary 탐색 트리를 사용하는 방식(O(log(n)))
- 주어진 단일 연결리스트로부터 루프(Loop)를 발견하여 존재하면 true, 존재하지 않으면 false 를 반환하는 함수 detectLoop() 작성하기
	* 토끼와 거북이(Hare and Tortoise) 알고리즘 활용

## Project
[목차로](#목차)

- 협업의 방식, 장단점 (git 과 연관)
- 코드 리뷰 방식은?
- 프로젝트 진행 시 어려웠던 점과 극복 방식은?

## Computer
[목차로](#목차)

- 이진 체계 설명
- 진법 변환
- 정수와 실수 표현, 사칙연산
- 아스키 코드, 유니코드
- CPU
- Memory
- Processor
- Scheduling
- 페이지 교체 알고리즘
- 외부 단편화와 내부 단편화

## Linux
[목차로](#목차)

- 기본 명령어

## Network
[목차로](#목차)

- IP Address, Port
- IPv4, IPv6
- 인터넷, 인트라넷, 방화벽
- OSI 7 Layer
- VPN, NAT 비교

## Other
[목차로](#목차)

- 동기와 비동기의 차이, 장단점
- Stack, Queue
- 시간 복잡도와 공간 복잡도, 계산법
- 교착 상태(DeadLock), 네 가지 조건
- 멀티 프로세싱과 멀티 프로그래밍
- 컴파일러와 인터프리터 비교
- 자바에서 성능개선 이슈
	* 전역 개념을 활용하면 인스턴스화하는 과정이 생략되므로 성능이 개선됨
	* 그러나 무분별한 static 남용은 프로젝트의 복잡성과 모호성을 증대시킴
- 프레임워크란?
- SI, SM, 솔루션 설명
- ERP, SCM, CRM
- Big O, Omega, Theta 복잡도



