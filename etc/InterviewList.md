
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

## Basic [목차로](#목차)

- 자바의 특징
	* 선 마이크로시스템즈에서 95년 제임스 고슬링을 필두로 만들어짐. 2010년 오라클에 인수됨
	* 대표적인 객체지향언어. 그러나 완벽한 객체지향은 아니다. Primitive 타입(기본형)은 객체로 취급하지 않기 때문이다.
	* 기본형 데이터를 객체로 취급하기 위해 Wrapper 클래스를 활용한다. 재사용과 형변환을 위해 활용. 오토박싱과 오토언박싱 적용됨
	* [JVM](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EA%B0%80%EC%83%81_%EB%A8%B8%EC%8B%A0), 즉 자바 가상 머신을 활용해 운영체제에 독립적
	* JVM 은 JRE(자바 런타임)에 포함되어 실행하고자 하는 운영체제에 간편설치 가능
	* JDK 는 자바 개발 도구이며, JVM 은 개발된 코드를 클래스 파일(byte)로 변환해 직접 실행한다.
	* 정확히는 javac 컴파일러가 바이트 코드로 변환하고 java classloader 가 JVM 에 적재한다
	* [GC](https://d2.naver.com/helloworld/1329), 즉 가비지 컬렉션은 참조되지 않는 메모리(HIP) 영역을 우선순위에 따라 회수하는 작업을 수행하며, FULL GC 는 stop-the-world 방식(전체 실행 중지)으로 이루어지기 때문에 성능을 고려한다면 이를 GC 튜닝이 필요하다. 그렇다고 stop-the-world 를 아예 안할 수는 없다
	* JVM 이라는 매개가 존재하기 때문에 언어 자체의 무게감이나 실행속도의 측면에서 상대적으로 느리지만, 일반적인 영역에서 체감할 정도는 아니다
	* 이식성, 호환성이 높고 C 계열과 문법상 유사해 진입장벽이 낮고 대중적
	* 비교적 하이 레벨 언어기 때문에 생산성이 높다
	* C 계열 언어와의 차이점 : C 는 포인터 등을 활용한 로우 레벨 언어로 객체지향 개념을 포함하는 C++ 은 디자인적 측면에서 굉장히 복잡해지는 결과가 나타났으나, 자바는 애초에 이를 과감히 포기하고 GC 를 내장한 하이 레벨(상대적) 언어로 복잡성을 해소하였다.
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
- Overloading, Overriding, Constructor
- equals, hashCode 재정의 이유와 방법
- static, final, abstract
- 추상 클래스와 인터페이스
- 예외처리

## Intensive [목차로](#목차)

- 컬렉션 프레임워크
	* Array 와 ArrauList 의 차이
	* List
	* Set
	* Map
- StringBuffer 와 StringBuilder 의 차이
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

## Web [목차로](#목차)

- HTTP 프로토콜
- 쿠키와 세션
- Get, Post 방식 (+@)
- Request, Response
- 웹 프로젝트 기본구조
- 프로젝트 개발 순서(소프트웨어 공학)
- MVC 패턴
- Restful

## JSP/Servlet [목차로](#목차)

- JSP
- Servlet
- Container 란?

## Spring [목차로](#목차)

- 스프링 프레임워크란?
- IoC, DI
- AOP, AspectJ
- 스프링에서 싱글톤 패턴을 사용하는 이유
- Filter, Interceptor

## Database [목차로](#목차)

- DBMS 란?
- ERD 란?
- UML 이란?
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

## Front [목차로](#목차)

- HTML, CSS
- Javascript, jQuery
- Ajax
- Responsive web

## Algorithm [목차로](#목차)

- 스레드가 3개 생성되었을 때, t1, t2, t3 의 순서가 보장되는 코드 작성
- pivotal(대각선이 고정인 행렬) 3x3, 4x4 를 뒤집는 로직 작성하기. 재귀 사용
- 10 칸짜리 배열로 스레드 세이프한 Queue 만들기
- Factorial 을 재귀로 작성하기
- 인덱스가 존재하는 정렬된 배열 x 에서 x[i] = i 인 인덱스 i 를 찾는 가장 최적화된 방법은?
	* 처음부터 탐색하는 방식(O(n))
	* binary 탐색 트리를 사용하는 방식(O(log(n)))
- 주어진 단일 연결리스트로부터 루프(Loop)를 발견하여 존재하면 true, 존재하지 않으면 false 를 반환하는 함수 detectLoop() 작성하기
	* 토끼와 거북이(Hare and Tortoise) 알고리즘 활용

## Project [목차로](#목차)

- 협업의 방식, 장단점 (git 과 연관)
- 코드 리뷰 방식은?
- 프로젝트 진행 시 어려웠던 점과 극복 방식은?

## Computer [목차로](#목차)

- 이진 체계 설명
- 진법 변환
- 정수와 실수 표현, 사칙연산
- 아스키 코드, 유니코드
- CPU
- Memory
- Processor
- Scheduling

## Linux [목차로](#목차)

- 기본 명령어

## Network [목차로](#목차)

- IP Address, Port
- IPv4, IPv6
- 인터넷, 인트라넷, 방화벽
- OSI 7 Layer
- VPN, NAT 비교

## Other [목차로](#목차)

- 동기와 비동기의 차이, 장단점
- Stack, Queue
- 시간 복잡도와 공간 복잡도, 계산법
- 교착 상태(DeadLock), 병목 현상
- 멀티 프로세싱과 멀티 프로그래밍
- 컴파일러와 인터프리터 비교
- 자바에서 성능개선 이슈
	* 전역 개념을 활용하면 인스턴스화하는 과정이 생략되므로 성능이 개선됨
	* 그러나 무분별한 static 남용은 프로젝트의 복잡성과 모호성을 증대시킴
- 프레임워크란?
- SI, SM, 솔루션 설명
- ERP, SCM, CRM
- Big O, Omega, Theta 복잡도



