
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190225-lightgray.svg?style=flat-square)



# MyBatis

## MyBatis 란?

- SqlSessionFactory 인스턴스 사용 (핵심)
- 퍼시스턴스 프레임워크 (하드디스크에 저장된 데이터 사용)
- 환경설정 및 원시타입, Map, POJO 를 설정하고, 매핑은 XML 과 애노테이션을 사용한다 (주로 XML)
- 서버의 거의 모든 데이터베이스 로직이 사라지고, SQL 은 XML 로 분리된다

## 환경설정

- SqlSessionFactory 빌드에는 XML 과 자바 코드 방식이 있는데, XML 방식으로 공부한다
- 설정 파일들은 WEB-INF/classes/ 또는 src/ 안에 존재해야 한다
- 환경설정 요소
	* configuration, environments, mapper 등
	* 각각 환경설정, 데이터베이스 연결 정보, sql 문장 순으로 기록된다

**configuration, environment**
**config.xml**
```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
 PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-config.dtd"

<configuration>

	<properties resource="config.properties"/>

	<environments default="development">
		<!--db 연결을 위한 일반적인 정보들 -->
		<environment id="development">
			<transactionManager type="JDBC"/>
			<dataSource type="POOLED">
				<property name="driver" value="${driver}"/>
				<property name="url" value="${url}"/>
				<property name="username" value="${username}"/>
				<property name="password" value="${password}"/>
			</dataSource>
		</environment>
	</environments>
	<mappers>
		<!-- sql 문장들이 들어 있는 mapper 파일들 -->
		<mapper resource="member.xml"></mapper>
		<mapper resource="board/board.xml"></mapper>
	</mappers>
</configuration>
```

- 위 환경설정 파일은 예시이다
- configuration 의 properties 태그는 없어도 되는데, 이때는 environments 의 dataSource 각 속성값들을 직접 지정해줘야 한다
	* driver, url, username, password
	* dataSource 는 데이터베이스 커넥션을 얻기 위한 환경 값들을 저장한다
- environments 내부에는 실제 설정정보를 입력하는데, 보통 dataSource 를 투입하면 된다
- mappers 는 sql 쿼리정보가 설정된 별도의 xml 파일 정보를 로드한다.
	* 별도의 xml 파일을 로드하는 이유는 각 테이블 별로 구분할 필요가 있기 때문
	* mapper 경로 지정 방법
		- xml 파일명이 특정 경로를 포함하지 않는다면, WEB-INF/class/ 내에 있는 것으로 간주하고
		- xml 파일명이 특정 경로를 포함한다면 src/ 하위 지정한 패키지나 디렉토리 내에 있는 것으로 간주하여 탐색한다
		- 이 외에 특정 패키지를 지정하거나 아예 별도의 디렉토리를 지정할 수 있으나, 보통 위 두 가지를 많이 사용한다

## mapper 설정파일 만들기

- mapper xml 파일에는 각 테이블 로직에 따른 sql 쿼리를 세팅한다
- 로직 식별을 의해 mapper 태그에 namespace 를 지정하고, 각 쿼리에 따른 id 도 지정한다
- 추후 쿼리를 사용할 때는 [namespace].[id] 형식으로 접근한다

**member.xml**
```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="member">
	<select id="selectId" parameterType="String" resultType="com.myweb.memberBean.MemberVo">
		SELECT * FROM MEMBER WHERE ID = #{_parameter}
	</select>
</mapper>
```

- member.selectId 에 접근하기 위하여 각 호출부에서는 sqlSession.selectOne("member.selectId", id); 메서드를 활용한다. selectOne 은 결과값이 하나라는 의미이다
- 입력 파라미터 id 에 따른 select 결과를 MEMBER 테이블로부터 가져오는 쿼리이다
- #{_parameter} 부분은 어떤 형식의 파라미터든지 한 개를 받아들이며, 앞에 '#' 기호가 붙는다면 실제 쿼리상에 작은따옴표가 포함된다
- '#' 대신 '$' 기호가 붙으면 따옴표가 포함되지 않은 입력 파라미터 그대로 들어가는데, 그렇게 되면 db 는 식별자 혹은 쿼리로 인식한다
- 실제 쿼리상에 따옴표가 붙는다는 의미는 sql injection 공격을 무의미하게 만드는 점에서 보안상 유리하다

## SqlSessionFactory 활용하기

### SqlSessionFactory 생성 및 SqlSession 만들기

- 지정 myBatis 경로 내 config.xml 파일에 커넥션 및 각종 쿼리에 대한 정보가 설정되어 있으므로,
- org.apache.ibatis.io.Resources 클래스의 getResourceAsReader("myBatis/config.xml"); 메서드로 java.io.Reader 객체를 얻는다
- 이후 reader 정보를 기반으로 SqlSessionFactory 를 빌드한다

```JAVA
package myBatis;

import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

public class MemberFactory {
	private static SqlSessionFactory factory;
	
	static {
		try {
			Reader reader = Resources.getResourceAsReader("myBatis/config.xml");
			factory = new SqlSessionFactoryBuilder().build(reader);
		} catch (Exception ex) {
			ex.printStackTrace();
		}
	}

	public static SqlSessionFactory getFactory() {
		return factory;
	}
}
```

- 위 클래스에서 static factory 를 get 하여 openSession() 메서드를 실행하면
- 지정된 설정에 따른 SqlSession 이 최종적으로 만들어진다!

## SqlSession 활용 예제











