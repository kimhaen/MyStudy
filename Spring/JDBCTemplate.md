
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190119-lightgray.svg?style=flat-square)

# JDBC Template ?

> 개발자로써 기본적인 쿼리 작성 및 DB 환경관리, 자신의 프로젝트와 DB 의 커넥션 설정 등은 매우 중요한 이슈이다

```
JDBC Template 은 기존의 번거롭고 반복적이던 DB 커넥션 로직을 모듈화 시킴으로써
개발자가 쿼리작성과 같은 실제적인 코딩만 해도 되도록 편의성을 높인 API 이다
```

## 기존 JDBC 커넥션 형성 과정

- JDBC 드라이버 로딩 > 커넥션 객체 생성 > 쿼리 작성 및 실행 > 자원 해제
- 이 중 쿼리 작성 및 실행 외 나머지 모두는 항상 동일한 과정을 거친다.
- 다음은 insert 와 select 작업에 사용되는 일반적인 DB 커넥션 생성 로직이다

```JAVA
public class MemberDao {

	// driver, url, id, pw 작성
	private String driver = "oracle.jdbc.driver.OracleDriver";
	private String url = "jdbc:oracle:thin:@localhost:1521:xe";
	private String userId = "hr";
	private String userPw = "hr";

	// Connection, ps, rs 선언
	private Connection conn = null;
	private PreparedStatement ps = null;
	private ResultSet rs = null;

	// Constructor
	public MemberDao () {
		try {
			// 생성자 호출 시 메모리에 jdbc 드라이버 로딩
			Class.forName(driver);
		} catch (Exception ex) {
			ex.printStackTrace();
		}
	}

	// insert
	public int memberInsert(Member member) {
		int result = 0;
		try {
			final String sql = " insert into member_spring (memId, memPw, memEmail) "	
					+ " values (?, ?, ?) ";
			// 커넥션 객체 ~ ps 생성 및 사용 후 사용종료된 자원 할당해제
			conn = DriverManager.getConnection(url, userId, userPw);
			ps = conn.preparedStatement(sql);
			ps.setString(1, member.getMemId());
			ps.setString(2, member.getMemPw());
			ps.setString(3, member.getMemEmail());
			result = ps.executeUpdate();
		} catch (ClassNotFoundException ex1) {
			ex1.printStackTrace();
		} catch (SQLException ex2) {
			ex2.printStackTrace();
		} finally {
			try {
				// 사용 종료 자원 할당해제
				if (rs != null) rs.close();
				if (ps != null) ps.close();
				if (conn != null) conn.close();
			} catch (Exception ex2) { }
		}
	}

	// select
	public Member memberSelect(Member member) {

		Member member = null;
		try {
			final String sql = " select * from member_spring where userId = ? and userPw = ?";
			// 커넥션 객체 ~ ps ~ rs 생성 및 사용 후 사용종료된 자원 할당해제
			conn = DriverManager.getConnection(url, userId, userPw);
			ps = conn.preparedStatement(sql);
			ps.setString(1, member.getMemId());
			ps.setString(2, member.getMemPw());
			rs = ps.executeQuery();

			// select 쿼리 결과가 있다면 Member 객체에 세팅 후 반환
			while (rs.next()) {
				String id = rs.getString("memId");
				String pw = rs.getString("memPw");
				String email = rs.getString("memEmail");
				int purcNum = rs.getInt("memPurcNum");

				member = new Member();
				member.setMemId(id);
				member.setMemPw(pw);
				member.setMemEmail(email);
				member.setMemPurcNum(purcNum);
			}
		} catch (ClassNotFoundException ex1) {
			ex1.printStackTrace();
		} catch (SQLException ex2) {
			ex2.printStackTrace();
		} finally {
			try {
				// 사용 종료 자원 할당해제
				if (rs != null) rs.close();
				if (ps != null) ps.close();
				if (conn != null) conn.close();
			} catch (Exception ex2) { }
		}

		return member;
	}

	// update
	public int memberUpdate(Member member) {
		...
	}

	// update
	public int memberUpdate(Member member) {
		...
	}

}
```

- 드라이버 로딩은 생성자에 의해 한 번만 이루어진다 치더라도,
- 커넥션-ps-rs 를 순서대로 생성하고 최종적으로 close 하는 작업은 CRUD 메서드가 실행될 때마다 동일하게 반복된다
- 다만, 동적으로 변하는 사항은 개발자에 의해 짜여지는 쿼리문이므로, 이것만 제외하고 나머지를 모듈화시키는 것은 합리적이다

## JDBC Template 의 역할 및 설정

- JDBC Template 는 이런 식으로 동일하게 반복되는 커넥션 세팅 과정을 모듈화한다
- 즉, 한 번만 설정한 뒤 그 이후로는 재사용하기만 하면 되므로 개발자의 실제적인 부담이 줄어든다 (쿼리 작성 및 실행만 하면 됨)
- Template 에는 c3p0 와 oracle 제품이 있는데, 구문상의 작은 차이를 제외하면 모든 세팅 방식은 완전 동일하다
- JDBC Template 은 각 제품에 호환되는 dataSource 를 이용해서 세팅되며(보통 생성자에서), 이후 이것을 재사용하기만 하면 된다
- dataSource 에 투입되는 세팅 내용은 driver, url, id, pw 로 기존의 딱딱한 방식의 그것과 같다 (최초 작성해줘야 하는 것은 동일, 제품에 따라 구문이 다소 상이함)

```JAVA
...

	private ComboPooledDataSource dataSource;
	private JdbcTemplate template;

	// Constructor
	public MemberDao() {
		dataSource = new ComboPooledDataSource();
		try {
			dataSource.setDriverClass(driver);
			dataSource.setJdbcUrl(url);
			dataSource.setUser(userid);
			dataSource.setPassword(userpw);
		} catch (PropertyVetoException e) {
			e.printStackTrace();
		}
		
		template = new JdbcTemplate();
		template.setDataSource(dataSource);
	}

...
```

- 일반적인 dataSource 클래스를 사용해도 된다
- ComboPooledDataSource 는 이후 커넥션 풀을 활용하기 위한 클래스이다

## 필요한 쿼리에 따른 여러 가지 사용 방법

- 기본적으로 CRUD 에서 반환값은 int, VO, int, int 이다
- 각각 투입, 검색, 투입, 투입 (?) 의 개념이기 때문 (임의로 정의함)

###  투입 (INSERT, UPDATE, DELETE) 에 따른 Template 활용

- 투입 개념의 쿼리에서는 template.update() 메서드를 활용한다
- 실행 결과는 int 로 반환되므로 그것에 맞게 저장 후 반환하면 된다

1. 작성한 sql 문과 각 인자를 직접 투입하기
- sql, 인자, 인자, ... 순으로 그냥 투입하면 된다
2. PreparedStatementCreator 활용하기 (새로운 ps 생성)
- creator 는 template 에서 활용될 ps 를 반환하는 createPreparedStatement 메서드를 재정의한다
- 이것은 드라이버로부터 생성된 커넥션 객체를 매개변수로 받아 ps 세팅 후 반환한다
3. 작성한 sql, 문과 PreparedStatementSetter 활용하기 (기존 ps 세팅)
- 드라이버로부터 ps 를 매개변수로 받는 setValues 메서드를 재정의해 각 인자값을 세팅한다

```JAVA
...

	public int memberInsert(final Member member) {
		
		int result = 0;
		
		final String sql = "INSERT INTO member_spring (memId, memPw, memEmail) values (?,?,?)";
		
//		DriverManagerDataSource dataSource = new DriverManagerDataSource();
//		dataSource.setDriverClassName(driver);
//		dataSource.setUrl(url);
//		dataSource.setUsername(userid);
//		dataSource.setPassword(userpw);
//		
//		JdbcTemplate template = new JdbcTemplate();
//		template.setDataSource(dataSource);
		
//		1st
//		result = template.update(sql, member.getMemId(), member.getMemPw(), member.getMemEmail());
		
//		2nd
//		result = template.update(new PreparedStatementCreator() {
//			
//			@Override
//			public PreparedStatement createPreparedStatement(Connection conn)
//					throws SQLException {
//				PreparedStatement pstmt = conn.prepareStatement(sql);
//				pstmt.setString(1, member.getMemId());
//				pstmt.setString(2, member.getMemPw());
//				pstmt.setString(3, member.getMemEmail());
//				
//				return pstmt;
//			}
//		});
		
//      3rd
		result = template.update(sql, new PreparedStatementSetter() {
			
			@Override
			public void setValues(PreparedStatement pstmt) throws SQLException {
				pstmt.setString(1, member.getMemId());
				pstmt.setString(2, member.getMemPw());
				pstmt.setString(3, member.getMemEmail());
				
			}
		});
		
		return result;
		
		/*
		int result = 0;
		
		try {
			Class.forName(driver);
			conn = DriverManager.getConnection(url, userid, userpw);
			String sql = "INSERT INTO member (memId, memPw, memEmail) values (?,?,?)";
			pstmt = conn.prepareStatement(sql);
			pstmt.setString(1, member.getMemId());
			pstmt.setString(2, member.getMemPw());
			pstmt.setString(3, member.getMemEmail());
			result = pstmt.executeUpdate();
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				if(pstmt != null) pstmt.close();
				if(conn != null) conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		
		return result;
		*/
	}

...
```

### 검색 (SELECT) 에 따른 Template 활용

- 검색 쿼리에서는 template.query() 메서드를 활용한다

1. 작성한 sql 문과 RowMapper<Member> 활용하여 각 인자를 직접 투입하기 (기존 ps 세팅)
2. PreparedStatementCreator, RowMapper<Member> 활용하기 (새로운 ps 생성)
3. 작성한 sql, 문과 PreparedStatementSetter, RowMapper<Member> 활용하기 (기존 ps 세팅)
4. 작성한 sql, Object[] 배열 객체 활용하기

```JAVA
	public Member memberSelect(final Member member) {
		
		List<Member> members = null;
		
		final String sql = "SELECT * FROM member_spring WHERE memId = ? AND memPw = ?";
		
//		DriverManagerDataSource dataSource = new DriverManagerDataSource();
//		dataSource.setDriverClassName(driver);
//		dataSource.setUrl(url);
//		dataSource.setUsername(userid);
//		dataSource.setPassword(userpw);
//		
//		JdbcTemplate template = new JdbcTemplate();
//		template.setDataSource(dataSource);
		
//		1st
//		members = template.query(sql, new PreparedStatementSetter() {
//			
//			@Override
//			public void setValues(PreparedStatement pstmt) throws SQLException {
//				pstmt.setString(1, member.getMemId());
//				pstmt.setString(2, member.getMemPw());
//			}
//		}, new RowMapper<Member>() {
//
//			@Override
//			public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
//				Member mem = new Member();
//				mem.setMemId(rs.getString("memId"));
//				mem.setMemPw(rs.getString("memPw"));
//				mem.setMemEmail(rs.getString("memEmail"));
//				mem.setMemPurcNum(rs.getInt("memPurcNum"));
//				return mem;
//			}
//		});
		
//		2nd
//		members = template.query(new PreparedStatementCreator() {
//			
//			@Override
//			public PreparedStatement createPreparedStatement(Connection conn)
//					throws SQLException {
//				PreparedStatement pstmt = conn.prepareStatement(sql);
//				pstmt.setString(1, member.getMemId());
//				pstmt.setString(2, member.getMemPw());
//				return pstmt;
//			}
//		}, new RowMapper<Member>() {
//
//			@Override
//			public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
//				Member mem = new Member();
//				mem.setMemId(rs.getString("memId"));
//				mem.setMemPw(rs.getString("memPw"));
//				mem.setMemEmail(rs.getString("memEmail"));
//				mem.setMemPurcNum(rs.getInt("memPurcNum"));
//				return mem;
//			}
//		});
		
//		3rd
//		members = template.query(sql, new RowMapper<Member>() {
//
//			@Override
//			public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
//				Member mem = new Member();
//				mem.setMemId(rs.getString("memId"));
//				mem.setMemPw(rs.getString("memPw"));
//				mem.setMemEmail(rs.getString("memEmail"));
//				mem.setMemPurcNum(rs.getInt("memPurcNum"));
//				return mem;
//			}
//			
//		}, member.getMemId(), member.getMemPw());
		
//		4th
		members = template.query(sql, new Object[]{member.getMemId(), member.getMemPw()}, new RowMapper<Member>() {

			@Override
			public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
				Member mem = new Member();
				mem.setMemId(rs.getString("memId"));
				mem.setMemPw(rs.getString("memPw"));
				mem.setMemEmail(rs.getString("memEmail"));
				mem.setMemPurcNum(rs.getInt("memPurcNum"));
				return mem;
			}
			
		});
		
		if(members.isEmpty()) 
			return null;
		
		return members.get(0);
		
		/*
		Member mem = null;
		
		try {
			
			Class.forName(driver);
			conn = DriverManager.getConnection(url, userid, userpw);
			String sql = "SELECT * FROM member_spring WHERE memId = ? AND memPw = ?";
			pstmt = conn.prepareStatement(sql);
			pstmt.setString(1, member.getMemId());
			pstmt.setString(2, member.getMemPw());
			rs = pstmt.executeQuery();
			
			while (rs.next()) {
				String memId = rs.getString("memid");
				String memPw = rs.getString("mempw");
				String memEmail = rs.getString("memEmail");
				int memPurcNum = rs.getInt("memPurcNum");
				
				mem = new Member();
				mem.setMemId(memId);
				mem.setMemPw(memPw);
				mem.setmemEmail(memEmail);
				mem.setMemPurcNum(memPurcNum);
			}
			
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				if(rs != null) rs.close();
				if(pstmt != null) pstmt.close();
				if(conn != null) conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		
		return mem;
		*/
		
	}
```

## JDBC Template 사용 시 주의할 점 (경험..)

1. 드라이버 로딩은 되지만, 커넥션 객체를 못얻어오는 오류
- 흔한 실수긴 하지만 jdbc url 경로에 오타가 있어서 CannotGetJdbcConnectionException 이 발생함
```
org.springframework.jdbc.CannotGetJdbcConnectionException:
Could not get JDBC Connection; nested exception is java.sql.SQLException:
Connections could not be acquired from the underlying database!
```
- url 경로에 ':' 한 개를 빼먹은 정말 단순 오타였음..

2. 단순 SQL 구문 오류
- select 문에서 테이블명을 잘못 기입하여 badSqlGrammarException 이 발생함
```
org.springframework.jdbc.BadSqlGrammarException:
PreparedStatementCallback; bad SQL grammar [ select * from member where memId = ? and memPw = ? ];
nested exception is java.sql.SQLSyntaxErrorException: ORA-00904:
"MEMPW": invalid identifier
```
- 메세지를 보면 구문이 잘못됐다고 친절하게 알려준다
- 잘못된 부분은 'memPw' 라고 하지만 내 의도는 'member_spring' 테이블에 대한 구문이었으므로 테이블명을 잘못 기입한 문제였다







