
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190103-lightgray.svg?style=flat-square)

# DB 커넥션 풀

> 지금까지 공부한 것처럼 웹 서버는 JDBC 드라이버를 통해 접속한 사용자에 해당하는 Connection 객체를 생성하여 여러 가지 쿼리를 전송하였는데, 만약 이용자 규모가 커지고 동시접속자가 많아진다면 당연히 커넥션을 생성하는 서버에 부담이 가중될 것이다

- 이때 사용하는 것이 커넥션 풀인데, 이것은 JAVA Thread Pool 과 같이 미리 정해진 개수의 스레드(여기서는 커넥션)를 생성하여 순차적으로 처리한다
- 이 때의 장점은,
	* 동시에 여러 요청 처리가 가능하고
	* 정해진 커넥션 이상의 부하가 없으며(예상치 못한 서버다운 방지)
	* 미리 커넥션을 생성함으로써 각각의 커넥션을 생성하기 위한 불필요한 로직이 사라진다는 것이다

## Connection Pool 의 사용

- 커넥션 풀은 JAVA 소스 단이 아닌 웹 서버 컨테이너(톰캣의 context.xml 등) 내에 정의한다
- 서버 컨테이너는 구동되면서 이 xml 에 따라 일정량의 커넥션을 미리 생성해 둔다

context.xml 에 정의

```JAVA
<Resource
	auth="Container"
	driverClassName="oracle.jdbc.driver.OracleDriver"
	url="jdbc:oracle:thin:@localhost:1521:xe"
	username="hr"
	password="hr"
	name="jdbc/Oracle11g"
	type="javax.sql.DataSource"
	maxActive="5"
	maxWait="1000"
	closeMethod="close"/>
```

Context 내부에 정의하면 된다<br/>

DAO 객체 내 커넥션으로 세팅

```JAVA
ServletContext sc = event.getServletContext();
InitialContext initialContext = new InitialContext();
DataSource ds = (DataSource)initialContext.lookup("java:comp/env/jdbc/studydb");

MemberDao dao = new MemberDao();
dao.setDataSource(ds);
```






