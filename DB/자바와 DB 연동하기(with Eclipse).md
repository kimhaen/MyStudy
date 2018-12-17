
# DB 연동하기

>오라클 데이터베이스에 접근하기 위해서는 JDBC 라는 DBMS 도구가 필요하다.<br>
이클립스에서 DBMS 연동을 위해 해당 프로젝트의 BuildPath 에 오라클 JDBC 경로를 등록한다
혹은 JRE 라이브러리 경로(일반적으로 **C:\Program Files\Java\jre1.8.0_191\lib\ext**)에 해당 라이브러리 **JAR 파일**을 등록하면 된다

## DB 커넥션 클래스 만들기

	*DBConnect 클래스*
<pre><code>
package db;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBConnect {
	Connection conn;
	String driver = "oracle.jdbc.driver.OracleDriver";
	String dbUrl = "jdbc:oracle:thin:@localhost:1521:xe"; // orcl(정식버전)
	String dbUser = "hr";
	String dbPwd = "hr";
	
	public DBConnect() {
		try {
			
			// driver file loading
			// db(jdbc) 가 메모리에 적재된다
			Class.forName(driver);
			
			// 연결객체 생성
			conn = DriverManager.getConnection(dbUrl, dbUser, dbPwd);
			
		} catch (Exception ex) { ex.printStackTrace(); }
	}

	public Connection getConn() {
		return conn;
	}
}
</code></pre>

이 클래스의 목적은 <strong>JDBC</strong> 를 통한 <strong>DB</strong> 와의 <strong>Connection</strong> 객체를 얻는 것이다<br>
<strong>Connection</strong> 객체를 얻기 위해서는,<br><br>
1. JDBC 드라이버를 메모리에 적재한다
2. 연결 객체를 생성한다
3. 생성된 연결 객체를 반환하는 메서드 작성


## 커넥션 구현하기