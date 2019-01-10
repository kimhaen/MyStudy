![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190110-lightgray.svg?style=flat-square)

# 스프링 프레임워크 구조

> 스프링 모듈, IoC 컨테이너, 스프링 프로젝트의 흐름 및 생성방법, 아파치 메이븐을 정리한다

## 스프링 모듈

- 코어 : spring-core, 스프링의 핵심인 DI 와 IoC 를 제공
- AOP : spring-aop, AOP 구현 기능 제공
- JDBC : spring-jdbc, 데이터베이스를 쉽게 다룰 수 있는 기능 제공(적은 양의 코드)
- tx : spring-tx, 스프링에서 제공하는 트랜젝션 관련 기능
- webmvc : spring-webmvc, 스프링에서 제공하는 컨트롤러와 뷰를 이용한 스프링 MVC 구현 기능

스프링에서 제공하는 모듈을 사용하려면 각 모듈에 대한 의존 설정을 개발 프로젝트에 XML 파일 등으로 직접 작성

## IoC Container

- 사실상 IoC 의 본체 컨테이너. 사용될 여러 객체를 생성하고, 조립, DI 작업을 수행한다
- 보통 IoC 컨테이너는 ApplicationContext 로 명명한다
- IoC 컨테이너에서 생성된 객체를 Bean 이라고 한다
- 여기서 생성된 객체들의 생명주기는 전적으로 IoC 에 의해 관리되며, 객체 init 및 destroy 작업 등을 설정할 수 있다

## 스프링 프로젝트의 생성

### 이클립스에서 Maven 프로젝트 생성하기

- maven 프로젝트로 생성
	* maven?
		- Apache Maven 은 JAVA 용 프로젝트 관리 도구이다<br/>[위키백과 - 아파치메이븐](https://ko.wikipedia.org/wiki/%EC%95%84%ED%8C%8C%EC%B9%98_%EB%A9%94%EC%9D%B4%EB%B8%90)
		- 아파치 소프트웨어 재단에서 개발하는 Java 기반 프로젝트의 라이프사이클 관리를 위한 빌드 도구. 이에 따라 컴파일과 빌드를 동시에 수행, 테스트를 병행하거나 서버 측 Deploy 자원을 관리할 수 있는 환경을 제공한다. 또한 라이브러리 관리 기능도 내포하고 있다. Java로 개발하다 보면 다양한 라이브러리를 필요로 하게 되는데, pom.xml 파일에 필요한 라이브러리만 적으면 Maven이 알아서 다운받고 설치해주고 경로까지 지정해준다.<br/>[나무위키 - Maven](https://namu.wiki/w/Maven)

	* 메이븐 설정 파일 (xml)
		- pom.xml : Project Object Model, 메이븐 프로젝트의 전체적인 환경설정 및 빌드 옵션을 설정
		- settins.xml : 메이븐 툴 자체에 관련된 설정 지정. 메이븐이 지정된 환경변수 경로에 존재. 있는지만 알면 됨

	* 메이븐 프로젝트 생성 시 : group-id 는 전체적인 상위 프로젝트명, artifact-id 는 현재 생성하고자 하는 maven 프로젝트명

- pom.xml 작성
	* pom.xml 파일에서는 프로젝트의 전체적인 환경설정을 한다
	* pom.xml 파일을 작성하고 project-update 를 하면, 프로젝트 환경에 필요한 모든 외부 라이브러리를 지정된 경로 (사용자 계정 > .m2 > repository) 에 다운받는다.
	* 기본 구조
		- project : modelVersion, groupId, artifactId, version, packaging, ... 이하 모든 설정정보를 포괄
		- properties : 메이븐 내부에서 반복적으로 사용될 상수 값을 정의할 때 사용
		- dependencies : 의존성 라이브러리 정보를 포함, 각 dependency 내용으로 최소 groupId, artifactId, version 정보가 필요함
		- build : 빌드 정보. 빌드에서 사용할 플러그인을 지정 (plugins) 및 디렉터리 구조 정의
		- repository : 의존성 정보 및 라이브러리를 다운받을 위치 지정 (사용자 계정 > .m2 > repository). 임의의 경로로 지정할 수도 있다
		- pluginRepositories : 메이븐 플러그인을 다운받을 저장소 위치 기술
		- distributionManagement : deploy goal 을 실행했을 시 배포되는 위치 기술. url 항목에는 일반적으로 회사 내부에 설치한 repository url 이 기술된다
	* 예시

```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
 
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
 
    <name>demo</name>
    <description>Demo project for Spring Boot</description>
 
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
 
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>
 
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
 
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
 
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```
- pom.xml 파일을 작성했는데 프로젝트 빌드에러가 난다면? : 내가 작성한 의존성 및 라이브러리 정보가 일치하지 않기 때문에, **Maven-Update Project** 옵션으로 외부 저장소로부터 업데이트하면 된다 (기본 저장소로 의존성에 맞게 다운받음)
- 혹은 자바 버전이 안맞거나 빌드 자체의 에러일 수 있으므로 각각 맞춰본다 (버전 맞추기, Project Clean)

참조 : [Maven pom.xml 파일 구조](https://wikidocs.net/18340)


### 직접 프로젝트 생성하기

> 메이븐 프로젝트의 구조를 바탕으로 직접 만들 수도 있다

- 메이븐 디렉토리 구조
	* 프로젝트 최상위 root 에 pom.xml 파일 위치 및 작성
	* src
	* src/main, src/test
	* src/main/java, /src/main/resources

- 이 구조에 맞게 직접 디렉토리와 파일을 생성하고, Maven 프로젝트로 import 하면 끝!

참조 : [Maven 디렉토리 구조](https://wikidocs.net/18339)














