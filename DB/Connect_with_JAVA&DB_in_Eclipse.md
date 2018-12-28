
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-181217-lightgray.svg?style=flat-square)

# DB 연동하기

>오라클 데이터베이스에 접근하기 위해서는 JDBC 라는 DBMS 도구가 필요하다.<br>
이클립스에서 DBMS 연동을 위해 해당 프로젝트의 BuildPath 에 오라클 JDBC 설치경로를 등록한다.<br>
혹은 JRE 라이브러리 경로(일반적으로 **C:\Program Files\Java\jre1.8.0_191\lib\ext**)에 해당 라이브러리 **JAR 파일**을 등록하면 된다

## DB 커넥션 클래스 만들기

- DB 커넥션 클래스

```JAVA
package db;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBConnect {
	Connection conn;
	String driver = "oracle.jdbc.driver.OracleDriver";
	String dbUrl = "jdbc:oracle:thin:@localhost:1521:xe"; //xe 는 경량버전, orcl 이 정식버전이다
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
```

이 클래스의 목적은 <strong>JDBC</strong> 를 통한 <strong>DB</strong> 와의 <strong>Connection</strong> 객체를 얻는 것이다<br>
<br><strong>Connection</strong> 객체를 얻기 위해서는,<br><br>
1. JDBC 드라이버를 메모리에 적재한다. 드라이버명은 **oracle.jdbc.driver.OracleDriver** 이다
2. 연결 객체를 생성한다
3. 생성된 연결 객체를 반환하는 메서드 작성
4. 필요한 라이브러리 >> **java.sql.Connection**, **java.sql.DriverManager**

<br>연결 객체를 생성할 때 필요한 인자는,<br><br>
- 연결할 디비의 URL
- 유저 아이디
- 유저 비밀번호

<br> getConn() 메서드는 연결을 위한 Connection 객체를 반환한다


## 커넥션 활용하기

>이제 JDBC 를 사용하기 위해서는 Connection 객체를 반환받을 수 있는 DBConnect 클래스를 인스턴스화 하면 된다
<br>
<pre>
1. DB 에 질의하고자 하는 쿼리문을 작성한다<br>
2. DBConnect 로부터 반환받은 Connection 객체로부터 작성한 쿼리문을 적용한 PreparedStatement 객체를 생성한다<br>
3. 필요에 의해 PreparedStatement 에 가변적인 파라미터를 적용한다<br>
4. 질의 결과가 필요하다면, ResultSet 객체를 생성해 ps.executeQuery() 반환값을 저장한다(일종의 CURSOR 역할)<br>
5. ResultSet 을 활용해 필요한 결과 데이터를 활용한다<br>
</pre>

<br><pre>*유의할 점*<br>
- 자바 커넥션을 활용한 DB 쿼리는 오토커밋이다(쿼리 실행 시마다 자동으로 커밋된다)
- 그러므로 애초에 트랜잭션 단위를 적절히 설정해야 한다
- 모든 쿼리를 실행한 이후에는, 사용했던 DB 자원들(rs, ps, conn)을 close(해제)해야 한다(그렇지 않으면 지속적으로 자원을 소모함)
</pre>
<br>
<strong>활용 예시</strong>

	*empTest1 메서드*

```JAVA
// 급여가 10000 이상인 직원의 사번, 이름, 급여, 입사일 출력
public void empTest1() {
		try {
			conn = new DBConnect().getConn();
			System.out.println("DB 연결됨 ... ");
			
			if (conn != null) {
				sql = "select employee_id, first_name, salary, hire_date from employees where salary >= ?";
				ps = conn.prepareStatement(sql);
				ps.setInt(1, 10000);
				rs = ps.executeQuery();
				System.out.println("쿼리문 완성 ... ");
				
				System.out.println("\n<< 급여가 10000 이상인 직원의 사번, 이름, 급여, 입사일 출력 >>");
				while (rs.next()) {
					System.out.println(rs.getString("employee_id"));
					System.out.println(rs.getString("first_name"));
					System.out.println(rs.getInt("salary"));
					System.out.println(rs.getDate("hire_date"));
					System.out.println("----------");
				}
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				rs.close();
				ps.close();
				conn.close();
			} catch (Exception ex) { }
		}
	}
```

이것은 일반적인 select 쿼리를 conn 객체의 ps 를 통해 전송한 것이다.
쿼리의 결과는 rs 객체에 담기도록 하였으며, 그 값들을 콘솔에 출력하도록 하였다.

	*empTest2 메서드*
```JAVA
public void empTest2() {
		try {
			conn = new DBConnect().getConn();
			System.out.println("DB 연결됨 ... ");
			
			if (conn != null) {
				sql = "select e.employee_id as \"id\", e.salary as \"sal\", e.hire_date as \"hDate\", m.first_name \"mName\" "
						+ "from employees e join employees m "
						+ "on e.manager_id = m.employee_id "
						+ "where to_char(e.hire_date, 'MM') = '12'";
				ps = conn.prepareStatement(sql);
				rs = ps.executeQuery();
				System.out.println("쿼리문 완성 ... ");
				
				System.out.println("\n<< 입사월이 12월인 직원의 사번, 급여, 입사일, 매니저명 출력 >>");
				while (rs.next()) {
					System.out.println(rs.getString("id"));
					System.out.println(rs.getInt("sal"));
					System.out.println(rs.getDate("hDate"));
					System.out.println(rs.getString("mName"));
					System.out.println("----------");
				}
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		// finally 절에 처리하는 것이 정상
		} finally {
			try {
				rs.close();
				ps.close();
				conn.close();
			} catch (Exception ex) { }
		}
	}
```

	이 코드는 employees 를 inner join 하여 그 값을 반환받아 출력하도록 하였다.

	*DBTest 메인메서드*

```JAVA
public class DBTest {
	Connection conn = null;
	String sql = null;
	PreparedStatement ps = null;
	ResultSet rs = null;
	
	public static void main(String[] args) {
		DBTest dbt = new DBTest();
		dbt.empTest1();
		dbt.empTest2();
	}
```

	메인 메서드로써 위의 empTest1 과 empTest2 메서드를 포함하고 있으며, 그것을 호출하였다.











