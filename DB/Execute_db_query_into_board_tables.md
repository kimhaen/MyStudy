
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-181218-lightgray.svg?style=flat-square)

# 자바코딩으로 커넥팅된 DB 에 쿼리문 실행하기

>이전에 오라클 JDBC Driver 를 통해 URL, ID, PWD 를 매개로 한 Connection 객체를 얻어와 활용하는 과정을 공부했다
이번에는 이 커넥션을 통해 DB 에 존재하는 게시판 테이블(BoardA, BoardB, BoardAtt)에 쿼리를 날리는 실습을 해본다

## 개요

1. 일반적인 게시판에서 활용되는 쿼리문 학습
	- select, insert, update, delete (이상 CRUD 쿼리)
	- view(글 상세), reply(답글달기) 등

2. 자바 코딩으로 DB 에 필요한 요청을 하는 전체적인 로직 이해하고 숙달하기

3. 보다 나은 쿼리를 위해 고민해보기

## 게시판 구조

>Hie


1. BoardA


	![desc BoardA;](
        https://github.com/daesungRa/MyStudy/blob/master/imgs/BoardA.PNG
      )
      

	- **A 형 게시판**인 BoardA 의 특징

		+ 쿼리(SELECT)의 결과를 계층형으로 표현하기에 용이하다
		+ 명확히 구조화되어 있으며 특정 DB 에 종속적이지 않다
		+ 그러나 대댓글이 계속해서 달리면 deep 의 길이가 무한정 늘어날 가능성이 있다 (10억 개라면..?ㄷㄷ)
		+ 컬럼 grp	: 만약 원본 글이라면 serial == grp == deep, 아니라면 grp는 최고 조상의 serial(group)와 같다
		+ 컬럼 deep	: 바로 윗 부모의 deep + "-" + 자신의 serial
		+ 정렬 시	: 원본 글에 대해 최신 글 순으로 grp 를 desc 하고, 각 원본에 대한 댓글들은 계층별로 asc

2. BoardB


	![desc BoardB;](
        https://github.com/daesungRa/MyStudy/blob/master/imgs/BoardB.PNG
      )
   

   	- **B 형 게시판**인 BoardB 의 특징

		+ 완전히 계층화된 A 형과 다르게 종속되는 부모 글에 대한 serial 만으로 간편하게 데이터 구조를 표현한다
		+ 쿼리를 계층형으로 구조화할 수 없으며 특정 DB 에 종속적이다
		+ 컬럼 pSerial	: 원본 글은 null 이며, 모든 댓글은 각 조상 원본 글의 serial 을 pSerial 값으로 갖는다(grp 와 유사)
		+ 정렬 시	: A 형처럼 계층화되어 있지 않으므로 별도의 계층화 쿼리문을 만들어야 한다(START WITH-CONNECT BY 문 사용)
      
3. BoardAtt


	![desc BoardAtt;](
        https://github.com/daesungRa/MyStudy/blob/master/imgs/BoardAtt.PNG
      )

      	- Attachment File (첨부파일) 목록을 저장하는 BoardAtt

		+ 특정 글에 종속된 첨부파일 하나가 추가될 때마다 serial 값은 1 씩 증가
		+ pSerial 은 BoardA 혹은 BoardB 의 특정 글의 serial 이다 (종속적)

* 유의할 점

	- 임시적으로 DB 에 생성한 게시판 구조. 제약조건은 아직 설정하지 않음.
	- SEQUENCE	: 각 테이블에는 **SEQUENCE** 가 존재한다(SEQ_BOARDA, SEQ_BOARDB, SEQ_BOARDATT). 시퀀스는 새 데이터가 insert 될 때마다 1 씩 자동증가하도록 세팅한다.
	- 무결성의 문제	: BoardAtt 의 외래키인 pSerial 은 BoardA 와 BoardB 의 주키인 bbs_serial 을 참조하는데, 주키 하나 당 여러 개의 BoardAtt 행이 존재할 수 있으므로 양자 간 join 이 일어났을 시 **1:N 의 도메인 원자성 위배 가능성**이 존재한다. 그러므로 경우에 따라 무결성 보장을 위해 별도의 join 테이블을 생성할 필요가 있다
	- 컬럼 HIT	: 게시판의 각 글은 조회될 때마다(view) HIT 값을 1 씩 증가시키도록 세팅. 조회수의 의미를 갖는다

## 코드 구조

**1. DBConnect 클래스**


```JAVA
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
```


	JDBC 드라이버로부터 Connection 객체를 반환하는 클래스


**2. BoardAVo, BoardBVo, BoardAtt 클래스**


```JAVA
package db;

import java.util.List;

public class BoardAVo {
	private int bbs_serial;
	private int bbs_grp;
	private String bbs_deep;
	private String bbs_subject;
	private String bbs_content;
	private String bbs_mdate; // Date 타입도 상관 없지만, 포맷 맞추기가 번거롭다
	private String bbs_id;
	private String bbs_pwd;
	private int bbs_hit;
	
	// 첨부파일의 목록
	private List<BoardAtt> att;
	
	// 첨부파일의 수
	private int attFileCnt;
	
	// 사용자로부터 직접 입력 받을 사항만 생성자로 초기화. 나머지는 내부적으로 처리
	// 아무 매개변수도 없는 디폴트 생성자도 필요하다 - Why?
	public BoardAVo() { }
	public BoardAVo(String subject, String content, String id, String pwd) {
		this.bbs_subject = subject;
		this.bbs_content = content;
		this.bbs_id = id;
		this.bbs_pwd = pwd;
	}
	
	public int getBbs_serial() {
		return bbs_serial;
	}
	public void setBbs_serial(int bbs_serial) {
		this.bbs_serial = bbs_serial;
	}
	public int getBbs_grp() {
		return bbs_grp;
	}
	public void setBbs_grp(int bbs_grp) {
		this.bbs_grp = bbs_grp;
	}
	public String getBbs_deep() {
		return bbs_deep;
	}
	public void setBbs_deep(String bbs_deep) {
		this.bbs_deep = bbs_deep;
	}
	public String getBbs_subject() {
		return bbs_subject;
	}
	public void setBbs_subject(String bbs_subject) {
		this.bbs_subject = bbs_subject;
	}
	public String getBbs_content() {
		return bbs_content;
	}
	public void setBbs_content(String bbs_content) {
		this.bbs_content = bbs_content;
	}
	public String getBbs_mdate() {
		return bbs_mdate;
	}
	public void setBbs_mdate(String bbs_mdate) {
		this.bbs_mdate = bbs_mdate;
	}
	public String getBbs_id() {
		return bbs_id;
	}
	public void setBbs_id(String bbs_id) {
		this.bbs_id = bbs_id;
	}
	public String getBbs_pwd() {
		return bbs_pwd;
	}
	public void setBbs_pwd(String bbs_pwd) {
		this.bbs_pwd = bbs_pwd;
	}
	public int getBbs_hit() {
		return bbs_hit;
	}
	public void setBbs_hit(int bbs_hit) {
		this.bbs_hit = bbs_hit;
	}
	public int getAttFileCnt() {
		return attFileCnt;
	}
	public void setAttFileCnt(int attFileCnt) {
		this.attFileCnt = attFileCnt;
	}
	public List<BoardAtt> getAtt() {
		return att;
	}
	public void setAtt(List<BoardAtt> att) {
		this.att = att;
	}
	
	
}
```


	A 형 게시판 BoardA 의 데이터를 담을 클래스


```JAVA
package db;

import java.util.List;

public class BoardBVo {
	private int bbs_serial;
	private String bbs_subject;
	private String bbs_content;
	private String bbs_mdate; // Date 타입도 상관 없지만, 포맷 맞추기가 번거롭다
	private String bbs_id;
	private String bbs_pwd;
	private int bbs_hit;
	private int pSerial;
	
	// 첨부파일의 목록
	private List<BoardAtt> att;
	
	// 첨부파일의 수
	private int attFileCnt;
	
	// 사용자로부터 직접 입력 받을 사항만 생성자로 초기화. 나머지는 내부적으로 처리
	// 아무 매개변수도 없는 디폴트 생성자도 필요하다 - Why?
	public BoardBVo() { }
	public BoardBVo(String subject, String content, String id, String pwd) {
		this.bbs_subject = subject;
		this.bbs_content = content;
		this.bbs_id = id;
		this.bbs_pwd = pwd;
	}
	
	public int getBbs_serial() {
		return bbs_serial;
	}
	public void setBbs_serial(int bbs_serial) {
		this.bbs_serial = bbs_serial;
	}
	public String getBbs_subject() {
		return bbs_subject;
	}
	public void setBbs_subject(String bbs_subject) {
		this.bbs_subject = bbs_subject;
	}
	public String getBbs_content() {
		return bbs_content;
	}
	public void setBbs_content(String bbs_content) {
		this.bbs_content = bbs_content;
	}
	public String getBbs_mdate() {
		return bbs_mdate;
	}
	public void setBbs_mdate(String bbs_mdate) {
		this.bbs_mdate = bbs_mdate;
	}
	public String getBbs_id() {
		return bbs_id;
	}
	public void setBbs_id(String bbs_id) {
		this.bbs_id = bbs_id;
	}
	public String getBbs_pwd() {
		return bbs_pwd;
	}
	public void setBbs_pwd(String bbs_pwd) {
		this.bbs_pwd = bbs_pwd;
	}
	public int getBbs_hit() {
		return bbs_hit;
	}
	public void setBbs_hit(int bbs_hit) {
		this.bbs_hit = bbs_hit;
	}
	public int getAttFileCnt() {
		return attFileCnt;
	}
	public void setAttFileCnt(int attFileCnt) {
		this.attFileCnt = attFileCnt;
	}
	public List<BoardAtt> getAtt() {
		return att;
	}
	public void setAtt(List<BoardAtt> att) {
		this.att = att;
	}
	
	
}
```


	B 형 게시판 BoardB 의 데이터를 담을 클래스


```JAVA
package db;

public class BoardAtt {
	int serial;
	int pSerial;
	String delFile;
	String attFile;
	String oriFile;
	
	// update 시 분기로 사용할 플래그
	String flag = "A"; // A=append, D=delete, U=update
	
	public BoardAtt() { }
	public BoardAtt(int s, int ps, String att, String ori) {
		this.serial = s;
		this.pSerial = ps;
		this.attFile = att;
		this.oriFile = ori;
	}
	
	public int getSerial() {
		return serial;
	}
	public void setSerial(int serial) {
		this.serial = serial;
	}
	public int getpSerial() {
		return pSerial;
	}
	public void setpSerial(int pSerial) {
		this.pSerial = pSerial;
	}
	public String getAttFile() {
		return attFile;
	}
	public void setAttFile(String attFile) {
		this.attFile = attFile;
	}
	public String getOriFile() {
		return oriFile;
	}
	public void setOriFile(String oriFile) {
		this.oriFile = oriFile;
	}
	public String getDelFile() {
		return delFile;
	}
	public void setDelFile(String delFile) {
		this.delFile = delFile;
	}
	public String getFlag() {
		return flag;
	}
	public void setFlag(String flag) {
		this.flag = flag;
	}
	
}
```


	첨부파일 게시판인 BoardAtt 의 데이터를 담을 클래스
	

**3. CRUD 및 view, reply 기능을 테스트하는 클래스 - DBTest, BoardATest, BoardBTest**

```JAVA
package member;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

import db.DBConnect;

public class DBTest {
	Connection conn = null;
	String sql = null;
	PreparedStatement ps = null;
	ResultSet rs = null;
	
	public static void main(String[] args) {
		/*
		Connection conn = null;
		String sql = null;
		ResultSet rs = null; // cursor 와 동일한 역할, 쿼리의 결과 명시적 저장

		try {
			conn = new DBConnect().getConn();
			if (conn != null) {
				System.out.println("정상적으로 DB 가 연결됨 ... ");
				
				sql = "select first_name, salary, hire_date from employees where department_id = ?"; // 문장 내 세미콜론은 붙이지 않는다
				
				PreparedStatement ps = conn.prepareStatement(sql);
				ps.setInt(1, 80);
				rs = ps.executeQuery(); // 쿼리 결과를 커서에 저장
				
				while (rs.next()) {
					System.out.println(rs.getString("first_name")); // employees 테이블의 모든 이름 조회. 무조건 String 으로 받는다
					System.out.println(rs.getInt("salary")); // salary 는 정수형. 무조건 int (실수형은 getDouble)
					System.out.println(rs.getString("hire_date")); // 날짜를 String 으로 받으면 시분초까지 출력됨
					System.out.println(rs.getDate("hire_date")); // 날짜를 Date 로 받으면 연월일만 출력됨
					System.out.println("------------------------");
				}
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		}
		*/
		
		DBTest dbt = new DBTest();
		/*dbt.empTest1();
		dbt.empTest2();
		dbt.empTest3();*/
		// dbt.empTest4("D", 5000, 10000);
		
		// dbt.empTest5("c001", "park gil dong", "1111"); // member 테이블에 새로운 유저 insert
		// dbt.empTest6("c001", "abc@def.com", "123-4567-8910", 2);
		dbt.empTest7("kim1");
	}
	
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
	
	// 입사월이 12월인 직원의 사번, 급여, 입사일, 매니저명 출력
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

	// 급여가 5000 ~ 10000 사이인 직원의 이름, 급여, 연락처, 매니저명 출력
	public void empTest3() {
		try {
			DBConnect dbc = new DBConnect();
			conn = dbc.getConn();
			System.out.println("DB 연결됨 ... ");
			
			if (conn != null) {
				sql = "select e.first_name as \"name\", e.salary as \"sal\", e.phone_number as \"phone\", m.first_name \"mName\" "
						+ "from employees e join employees m "
						+ "on e.manager_id = m.employee_id "
						+ "where e.salary between ? and ?";
				ps = conn.prepareStatement(sql);
				ps.setInt(1, 5000);
				ps.setInt(2, 10000);
				rs = ps.executeQuery();
				
				System.out.println("\n<< 급여가 5000 ~ 10000 사이인 직원의 이름, 급여, 연락처, 매니저명 출력 >>");
				while (rs.next()) {
					System.out.println(rs.getString("name"));
					System.out.println(rs.getInt("sal"));
					System.out.println(rs.getString("phone"));
					System.out.println(rs.getString("mName"));
					System.out.println("------------------");
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
	
	// 이름에 a 가 포함되고 두 급여 사이를 매개변수로 전달받아 해당 직원의 사번, 이름, 급여 조회
	public void empTest4(String fn, int sal1, int sal2) { // 이런 식으로 매개변수를 받는 것이 PreparedStatement 사용하는 이유
		try {
			conn = new DBConnect().getConn();
			System.out.println("DB 연결됨 ... ");
			
			if (conn != null) {
				sql = "select employee_id, first_name, last_name, salary "
						+ "from employees "
						+ "where first_name like ? "
						+ "and salary between ? and ? "
						+ "order by employee_id asc";
				ps = conn.prepareStatement(sql);
				ps.setString(1, "%" + fn + "%"); // 이렇게 매개변수를 가변적으로 세팅하는 것이 ps 의 목적!!!
				ps.setInt(2, sal1);
				ps.setInt(3, sal2);
				rs = ps.executeQuery();
				
				System.out.println("\n<< 이름에 " + fn + " 가 포함되고, 급여 " + sal1 + " 과 " + sal2
										+ " 사이를 매개변수로 전달받아 해당 직원의 사번, 이름, 급여 조회 >>");
				while (rs.next()) {
					System.out.println("id : " + rs.getString("employee_id"));
					System.out.println("first name : " + rs.getString("first_name"));
					System.out.println("last name : " + rs.getString("last_name"));
					System.out.println("salary : " + rs.getInt("salary"));
					System.out.println("--------------------");
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
	
	// 대상: member table
	// 아이디, 이름, 암호를 매개변수로 전달받아 저장
	// 저장만 하는 경우에서 ResultSet 은 필요 없다. ps.executeQuery() 의 반환형임
	// 저장시에는 ps.executeUpdate() 메서드가 호출되며, 그 반환형은 int 이다(쿼리실행(저장) 결과를 반영)
	public void empTest5(String id, String name, String pwd) {
		try {
			DBConnect dbc = new DBConnect();
			conn = dbc.getConn();
			System.out.println("DB 연결됨 ... ");
			
			if (conn != null) {
				sql = "insert into member(id, name, pwd) values(?, ?, ?)";
				ps = conn.prepareStatement(sql);
				ps.setString(1, id);
				ps.setString(2, name);
				ps.setString(3, pwd);
				
				int cnt = ps.executeUpdate();
				if (cnt >= 1) {
					System.out.println(id + " : 정상적으로 입력됨");
				} else {
					System.out.println(id + " : 데이터 저장 중 오류 발생");
				}
			}			
		} catch (Exception ex) {
			ex.printStackTrace();
		// finally 절에 처리하는 것이 정상
		} finally {
			try {
				ps.close();
				conn.close();
			} catch (Exception ex) { }
		}
	}
	
	// c001 의 이메일, 연락처, 학년을 매개변수로 전달받아 수정
	// 단, 가입일은 현재 날짜로 자동 입력
	public void empTest6(String id, String email, String phone, int grade) {
		try {
			DBConnect dbc = new DBConnect();
			conn = dbc.getConn();
			System.out.println("DB 연결됨 ... ");
			
			if (conn != null) {
				sql = "update member "
						+ "set email = ?, phone = ?, grade = ? "
						+ "where id = ?";
				ps = conn.prepareStatement(sql);
				ps.setString(1, email);
				ps.setString(2, phone);
				ps.setInt(3, grade);
				ps.setString(4, id);
				
				int cnt = ps.executeUpdate();
				if (cnt >= 1) {
					System.out.println(id + " : 정상적으로 수정됨");
				} else {
					System.out.println(id + " : 데이터 수정 중 오류 발생");
				}
			}			
		} catch (Exception ex) {
			ex.printStackTrace();
		// finally 절에 처리하는 것이 정상
		} finally {
			try {
				ps.close();
				conn.close();
			} catch (Exception ex) { }
		}
	}
	
	// c001 을 매개변수로 전달 받아 해당 데이터를 member 테이블에서 삭제하기
	public void empTest7(String id) {
		try {
			conn = new DBConnect().getConn();
			
			if (conn != null) {
				sql = "delete from member where id = ?";
				ps = conn.prepareStatement(sql);
				ps.setString(1, id);
				int cnt = ps.executeUpdate();
				
				if (cnt >= 1) {
					System.out.println(id + " : 정상적으로 삭제됨");
				} else {
					System.out.println(id + " : 데이터 삭제 중 오류 발생");
				}
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				
			} catch (Exception ex) { }
		}
	}
}
```


	기본 테스트 - 여러 쿼리문 복습 겸 JDBC 를 통해 실행해보기


```JAVA
package db;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class BoardATest {
	DBConnect dbc = null;
	// conn 은 모든 실행이 끝난 이후가 아니라면 close() 해서는 안 된다
	Connection conn = null;
	PreparedStatement ps = null;
	ResultSet rs = null;

	// Constructor 에서 Connection 정보를 만들어 필드에 저장
	// PreparedStatement 와 ResultSet 도 필드에 지정
	public BoardATest() {
		this.dbc = new DBConnect();
		this.conn = dbc.getConn();
	}
	
	// 게시판의 제목, 내용을 매개변수 find 로 검색하여 반환하도록 작성
	// BoardAVo.java 는 테이블의 컬럼내용과 동일하게 필드 작성(Value Object 라는 의미) 후 게터세터 세팅
	public List<BoardAVo> select (String find) {
		// 굳이 List 컬렉션을 사용하는 이유는? 그냥 ResultSet 을 반환할 수도 있지 않은가?
		// 불특정 다수가 공통의 DB 테이블을 사용하고자 할때 ResultSet 만을 사용한다면 해당 테이블이 LOCK 이 걸리므로
		// 작업해제가 될때까지 딜레이가 걸린다. 그래서 결과값을 빠르게 List 에 저장시키고 자원을 해제한다면
		// 불필요한 딜레이 없이 바로바로 여러 사용자가 공통의 테이블을 돌아가면 사용할 수 있을 것이다
		List<BoardAVo> list = new ArrayList<BoardAVo>();
		
		try {
			String sql = 
					"select * from ("	// 페이징 처리
					+ "	select rownum rno, s.* from ("	// rownum 설정
					+ "		select a.*, (select count(*) from boardAtt where pserial = a.bbs_serial) attCnt " // 첨부파일 수도 포함
					+ "			from boardA a"	// 가져오고자 하는 정보
					+ "			where bbs_subject like ? "
					+ "				or bbs_content like ? "
					+ "			order by bbs_grp desc, bbs_deep asc"
					+ "	)s "
					+ ") where rno between 1 and 10";
			ps = conn.prepareStatement(sql);
			ps.setString(1, "%" + find + "%"); // 매개변수로 전달받은 검색필터문자
			ps.setString(2, "%" + find + "%"); // '%다문%'
			rs = ps.executeQuery();
			
			while (rs.next()) {
				BoardAVo vo = new BoardAVo();
				vo.setBbs_serial(rs.getInt("bbs_serial"));
				vo.setBbs_grp(rs.getInt("bbs_grp"));
				vo.setBbs_deep(rs.getString("bbs_deep"));
				vo.setBbs_subject(rs.getString("bbs_subject"));
				vo.setBbs_content(rs.getString("bbs_content"));
				vo.setBbs_mdate(rs.getDate("bbs_mdate").toString());
				vo.setBbs_id(rs.getString("bbs_id"));
				vo.setBbs_pwd(rs.getString("bbs_pwd"));
				vo.setBbs_hit(rs.getInt("bbs_hit"));
				vo.setAttFileCnt(rs.getInt("attCnt")); // 첨부파일 수
				
				list.add(vo);
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception ex) { }
		}
		
		return list;
	}
	
	// 사용자로부터 특정 serial 번호에 해당하는 게시판 글 하나를 조회한다
	// view 목록은 글 제목, 내용, 관련 이미지 등, 첨부파일이다
	// 첨부파일을 위해 boardA 와 boardAtt 테이블이 조인되는데, 이때 데이터 무결성을 위한 조치를 한다
	public BoardAVo view (int serial) {
		BoardAVo vo = new BoardAVo();
		String sql = null;
		
		try {
			// HIT 수 증가 (조회수)
			sql = "update boardA set bbs_hit = bbs_hit + 1 where bbs_serial = " + serial;
			conn.setAutoCommit(false); // hit 수 증가시켰는데 이후 조회작업이 실패할 경우 롤백해야 하므로
			ps = conn.prepareStatement(sql);
			ps.executeUpdate();
			
			// serial 에 해당하는 본문 글 1 건 불러오기 (상세보기)
			sql = "select * from boardA where bbs_serial = " + serial;
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			rs.next();
			
			vo.setBbs_serial(rs.getInt("bbs_serial"));
			vo.setBbs_grp(rs.getInt("bbs_grp"));
			vo.setBbs_deep(rs.getString("bbs_deep"));
			vo.setBbs_subject(rs.getString("bbs_subject"));
			vo.setBbs_content(rs.getString("bbs_content"));
			vo.setBbs_mdate(rs.getDate("bbs_mdate").toString());
			vo.setBbs_id(rs.getString("bbs_id"));
			vo.setBbs_pwd(rs.getString("bbs_pwd"));
			vo.setBbs_hit(rs.getInt("bbs_hit"));
			
			// BoardAVo 의 att 에 첨부파일 목록 저장하기
			sql = "select * from boardAtt where pSerial = " + serial;
			List<BoardAtt> att = new ArrayList<BoardAtt>();
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			
			while (rs.next()) {
				BoardAtt at = new BoardAtt(
						rs.getInt("serial"),
						rs.getInt("pserial"),
						rs.getString("attFile"),
						rs.getString("oriFile")
						);
				
				att.add(at);
			}
			
			vo.setAtt(att);
			
			conn.commit();
		} catch (Exception ex) {
			try {
				conn.rollback();
			} catch (SQLException se) {
				se.printStackTrace();
			}
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) { }
		}
		
		return vo;
	}
	
	// serial 에 해당하는 제목과 내용을 수정하시오 (제목과 내용은 임의의 값으로)
	public void update(int serial, String subject, String content) {
		BoardAVo preVo = new BoardAVo(); // 객체를 제대로 생성해 줘야지..
		BoardAVo postVo = new BoardAVo();
		String sql = null;
		
		try {
			// 수정된 정보를 확인하기까지 AutoCommit == false
			conn.setAutoCommit(false);
			
			// 이전값 가져오기
			sql = "select bbs_serial, bbs_subject, bbs_content from boardA where bbs_serial = " + serial;
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			rs.next();
			preVo.setBbs_serial(rs.getInt("bbs_serial"));
			preVo.setBbs_subject(rs.getString("bbs_subject"));
			preVo.setBbs_content(rs.getString("bbs_content"));
			
			// 새로운값 세팅 (수정)
			sql = "update boardA set bbs_subject = ?, bbs_content = ? where bbs_serial = " + serial;
			ps = conn.prepareStatement(sql);
			ps.setString(1, subject);
			ps.setString(2, content);
			ps.executeUpdate();
			
			// 수정 결과
			sql = "select bbs_serial, bbs_subject, bbs_content from boardA where bbs_serial = " + serial;
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			rs.next();
			postVo.setBbs_serial(rs.getInt("bbs_serial"));
			postVo.setBbs_subject(rs.getString("bbs_subject"));
			postVo.setBbs_content(rs.getString("bbs_content"));
			
			// 결과 출력
			System.out.println("serial : " + serial);
			System.out.println("subject : [이전값] " + preVo.getBbs_subject() + " > [변경값] " + postVo.getBbs_subject());
			System.out.println("content : [이전값] " + preVo.getBbs_content() + " > [변경값] " + postVo.getBbs_content());
			
			// 확인 완료되면 커밋
			conn.commit();
		} catch (Exception ex) {
			try {
				conn.rollback();
				ex.printStackTrace();
			} catch (SQLException se) { }
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception ex) { }
		}
	}	
	
	// serial 에 해당하는 본문 글을 삭제(단, 첨부가 있는 경우는 첨부파일 목록도 삭제)
	public void delete(int serial) {		
		try {
			conn.setAutoCommit(false);
			
			// 걍 쿼리만 날리면 끝!
			// 시리얼에 해당하는 글 삭제
			String sql = "delete from boardA where bbs_serial = " + serial;
			ps = conn.prepareStatement(sql);
			ps.executeUpdate();
			
			// 삭제하고자 하는 시리얼의 글을 pserial 로 참조하는 boardAtt 의 글도 삭제
			sql = "delete from boardAtt where pSerial = " + serial;
			ps = conn.prepareStatement(sql);
			ps.executeUpdate();
			
			System.out.println(serial + " 번 글 삭제 완료");
			conn.commit();
		} catch (Exception ex) {
			try {
				conn.rollback();
				ex.printStackTrace();
			} catch (SQLException se) { }
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception ex) { }
		}
	}
	
	public static void main(String[] args) {
		BoardATest bat = new BoardATest();
		/*
		// 전체 글 목록 출력. 페이징 처리 됨
		List<BoardAVo> list = bat.select("a");
		
		System.out.println("\n\t<< 게시판 목록 >>");
		for (int i = 0; i < list.size(); i++) {
			BoardAVo vo = list.get(i);
			System.out.println("NO : " + (i + 1));
			System.out.println("Serial : " + vo.getBbs_serial());
			System.out.println("그룹(Grp) : " + vo.getBbs_grp());
			System.out.println("Deep : " + vo.getBbs_deep());
			System.out.println("Subject : " + vo.getBbs_subject());
			System.out.println("Content : " + vo.getBbs_content());
			System.out.println("MDate : " + vo.getBbs_mdate());
			System.out.println("ID : " + vo.getBbs_id());
			System.out.println("PWD : " + vo.getBbs_pwd());
			System.out.println("HIT : " + vo.getBbs_hit());
			System.out.println("Number of attFile : " + vo.getAttFileCnt());
			System.out.println("=====================================");
		}
		
		// 선택된 글(serial) 상세보기
		BoardAVo view = bat.view(20);
		
		System.out.println("================ 상세보기 ================");
		System.out.println("Serial : " + view.getBbs_serial());
		System.out.println("그룹(Grp) : " + view.getBbs_grp());
		System.out.println("Deep : " + view.getBbs_deep());
		System.out.println("Subject : " + view.getBbs_subject());
		System.out.println("Content : " + view.getBbs_content());
		System.out.println("MDate : " + view.getBbs_mdate());
		System.out.println("ID : " + view.getBbs_id());
		System.out.println("PWD : " + view.getBbs_pwd());
		System.out.println("HIT : " + view.getBbs_hit());
		System.out.println("================ 첨부파일 ================");
		List<BoardAtt> att = view.getAtt();
		if (att.size() == 0) {
			System.out.println("[info] 첨부 파일이 없습니다.");
		} else {
			System.out.println("--------------------");
			for (int i = 0; i < att.size(); i++) {
				BoardAtt at = att.get(i);
				System.out.println("\tserial : " + at.getSerial());
				System.out.println("\tpSerial : " + at.getpSerial());
				System.out.println("\tatt file : " + at.getAttFile());
				System.out.println("\tori file : " + at.getOriFile());
				System.out.println("--------------------");
			}
			System.out.println("================ 첨부파일 끝================");
		}
		*/
		// serial 번호로 글 제목, 내용 수정
		bat.update(10, "10 번 글 수정. 하하하", "수정수정수정수정");
		bat.delete(22);
	}
}
```


	CRUD 테스트
	SELECT(페이징처리 + rownum추가 + 정렬후 반환)



```JAVA
package db;

import java.io.File;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.List;

public class BoardBTest {

	// 커넥션 변수 지정
	Connection conn = null;
	String sql = null;
	PreparedStatement ps = null;
	ResultSet rs = null;

	// 생성자
	// 커넥션 객체 생성해 초기화하기
	public BoardBTest() {
		this.conn = new DBConnect().getConn();
	}

	// main method
	public static void main(String[] args) {
		BoardBTest bTest = new BoardBTest();
		// <<SELECT>>
		// bTest.select("");
		// bTest.select2(4);
		
		// <<INSERT>>
		/*
		List<BoardAtt> list = new ArrayList<BoardAtt>();
		BoardAtt ba1 = new BoardAtt(0, 0, "zzz.png", "zzz.png");
		BoardAtt ba2 = new BoardAtt(0, 0, "하하하.png", "호호호.png");
		BoardAtt ba3 = new BoardAtt(0, 0, "999.png", "999.png");
		
		list.add(ba1);
		list.add(ba2);
		list.add(ba3);
		
		bTest.insert("sub....", "content....", "a002", "1111", list);
		*/
		
		// <<INSERT>>
		List<BoardAtt> list = new ArrayList<BoardAtt>();
		BoardAtt ba1 = new BoardAtt(0, 0, "trtr.png", "trtr.png");
		BoardAtt ba2 = new BoardAtt(0, 0, "gg.png", "gg.png");
		BoardAtt ba3 = new BoardAtt(0, 0, "1212.png", "1212.png");
		
		list.add(ba1);
		list.add(ba2);
		list.add(ba3);
		
		bTest.insert2("sub....", "content....", "a004", "1111", list);
		
		
		// <<DELETE>>
		// bTest.delete(42); // serial 이 42 번인 글 삭제 및 42 번에 종속된 첨부파일들도 삭제
		
		// <<UPDATE>>
		// bTest.update(43, "update sub....", "uirueiuri", new ArrayList<>()); // 끝에는 공갈 객체 전송
	}

	// 제목(첨부파일 수), 작성자, 작성일, 조회수
	public void select(String find) {
		try {
			// 쿼리문 작성
			sql = "select * from (" // paging
					+ "	select rownum rno, s.* from (" // rownum
					+ "		select bvo.bbs_subject subject, (select count(*) from boardAtt where pserial = bvo.bbs_serial) attCnt, "
					+ "				bvo.bbs_id id, bvo.bbs_mdate mdate, bvo.bbs_hit hit " // select
					+ "			from boardB bvo "
					+ "			where bvo.bbs_subject like ? "
					+ "				or bvo.bbs_content like ? "
					+ "			order by bvo.bbs_mdate desc"
					+ "	)s "
					+ ") where rno between 1 and 5";
			
			// setting ps
			ps = conn.prepareStatement(sql);
			ps.setString(1, "%" + find + "%");
			ps.setString(2, "%" + find + "%");
			// execute ps, 결과를 rs 에 담기
			rs = ps.executeQuery();
			
			// rs 내용 있는 대로 BoardBVo 객체에 담아 곧바로 출력
			int no = 1; // 글 넘버
			while (rs.next()) {// rs 에서 곧바로 출력해도 되지만, BoardB 구조 이해를 위해 저장 후 출력
				// 출력할거 : 제목, 첨부파일 수, 작성자, 작성일, 조회수
				BoardBVo bvo = new BoardBVo();
				bvo.setBbs_subject(rs.getString("subject")); // 제목
				bvo.setAttFileCnt(rs.getInt("attCnt")); // 첨부파일 수
				bvo.setBbs_id(rs.getString("id")); // 작성자
				bvo.setBbs_mdate(rs.getString("mdate")); // 작성일
				bvo.setBbs_hit(rs.getInt("hit"));
				
				// 바로 출력
				System.out.println("no : " + no);
				System.out.println("제목 : " + bvo.getBbs_subject());
				System.out.println("첨부파일 수 : " + bvo.getAttFileCnt());
				System.out.println("작성자 : " + bvo.getBbs_id());
				System.out.println("작성일 : " + bvo.getBbs_mdate());
				System.out.println("조회수 : " + bvo.getBbs_hit());
				System.out.println("====================================");
				
				no++;
			}
			
			
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) {
			}
		}
	}
	
	// 파라미터로 전달받은 시리얼 넘버 포함 이 시리얼을 부모로 하는 모든 자식 글(댓글)을 계층형으로 조회 
	public void select2(int serial) {
		try {
			// 쿼리문 작성
			sql = "select * from (" // paging
					+ "	select rownum rno, s.* from ( " // rownum
					+ "		select level, rpad (bbs_subject, 30, ' ') bbs_subject, bbs_id, bbs_mdate, bbs_hit, "
					+ "				(select count(*) from boardAtt where pserial = boardB.bbs_serial) attCnt "
					+ "			from boardB "
					+ "			connect by prior bbs_serial = bbs_pserial"
					+ "			start with bbs_pSerial is null "
					+ "			order siblings by bbs_	serial desc "
					+ "	)s "
					+ ") where rno between 1 and 1000";
			
			// setting ps
			ps = conn.prepareStatement(sql);
			// execute ps, 결과를 rs 에 담기
			rs = ps.executeQuery();
			
			// rs 내용 있는 대로 BoardBVo 객체에 담아 곧바로 출력
			while (rs.next()) {
				System.out.println(rs.getInt("rno") + rs.getInt("level") + rs.getString("bbs_subject")
						+ "(" + rs.getInt("attCnt") + ")" + rs.getString("bbs_id")
						+ rs.getDate("bbs_mdate") + rs.getInt("bbs_hit"));
			}			
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) {
			}
		}
	}

	// 새 글 작성
	public void insert(String subject, String content, String id, String pwd, List<BoardAtt> attFile) {
		try {
			conn.setAutoCommit(false);
			sql = "insert into boardB values(seq_boardB.nextval, ?, ?, sysdate, ?, ?, 1, null)";
			ps = conn.prepareStatement(sql);
			ps.setString(1, subject);
			ps.setString(2, content);
			ps.setString(3, id);
			ps.setString(4, pwd);
			int cnt = ps.executeUpdate();
			
			if (cnt > 0) {
				// 첨부파일 저장. 매개변수로 전달받은 List 가 있다면
				if (attFile != null) {
					// 본문 글이 저장될 때 발생한 현재 시퀀스 얻어오기
					sql = "select seq_boardB.currval ps from dual";
					ps = conn.prepareStatement(sql);
					rs = ps.executeQuery();
					rs.next();
					// 시퀀스 저장
					int pSerial = rs.getInt("ps");
					
					for (int i = 0; i < attFile.size(); i++) {
						// 리스트에 저장된 객체를 가져와서 필요한 정보를 boardAtt 테이블에 insert
						BoardAtt fileName = attFile.get(i);
						sql = "insert into boardAtt values(seq_boardAtt.nextval, ?, ?, ?)";
						ps = conn.prepareStatement(sql);
						ps.setInt(1, pSerial);
						ps.setString(2, fileName.getAttFile());
						ps.setString(3, fileName.getOriFile());
						ps.executeUpdate();
					}
				}
			}
			
			System.out.println("입력 완료!");
			conn.commit();
		} catch (Exception ex) {
			ex.printStackTrace();
			try {
				conn.rollback();
			} catch (Exception ex2) { }
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) {
			}
		}
	}
	
	// insert all 을 활용한 새 글 작성
	// 첨부파일 목록을 불러와 boardAtt 테이블에 저장 시 execute 문은 한 번만 실행되도록
	public void insert2(String subject, String content, String id, String pwd, List<BoardAtt> attFile) {
		try {
			conn.setAutoCommit(false);
			sql = "insert into boardB values(seq_boardB.nextval, ?, ?, sysdate, ?, ?, 1, null)";
			ps = conn.prepareStatement(sql);
			ps.setString(1, subject);
			ps.setString(2, content);
			ps.setString(3, id);
			ps.setString(4, pwd);
			int cnt = ps.executeUpdate();
				
			if (cnt > 0) {
				// 첨부파일 저장. 매개변수로 전달받은 List 가 있다면
				if (attFile != null) {
					// 본문 글이 저장될 때 발생한 현재 시퀀스 얻어오기
					sql = "select seq_boardB.currval ps from dual";
					ps = conn.prepareStatement(sql);
					rs = ps.executeQuery();
					rs.next();
					// 시퀀스 저장
					int pSerial = rs.getInt("ps");
					
					sql = "insert all ";
					for (int i = 0; i < attFile.size(); i++) {
						// 리스트에 저장된 객체를 가져와서 필요한 정보를 boardAtt 테이블에 insert
						BoardAtt fileName = attFile.get(i);
						// ps 세팅 없이 가변 데이터 저장을 위해 포맷 사용
						sql = sql + String.format(
								"into boardAtt values(seq_boardAtt.nextval, '%d', '%s', '%s') ",
								pSerial, fileName.getAttFile(), fileName.getOriFile() // 부모 글 시퀀스(종속적), 임시파일명, 원본파일명
								);
					}
					
					sql += " select * from dual";
					ps = conn.prepareStatement(sql);
					ps.executeUpdate();
				}
			}
			
			System.out.println("입력 완료!");
			conn.commit();
		} catch (Exception ex) {
			ex.printStackTrace();
			try {
				conn.rollback();
			} catch (Exception ex2) { }
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) {
			}
		}
	}

	// 시리얼 번호를 기준으로 내용 변경
	// UPDATE 기능을 구현하는 방식
		// 저장 객체 내부적으로 flag 를 생성하여 update, delete 등의 추가적인 로직을 수행함
	public void update(int serial, String subject, String content, List<BoardAtt> attFile) {
		try {
			conn.setAutoCommit(false);
			sql = "update boardB set bbs_subject = ?, bbs_content = ? where bbs_serial = " + serial;
			ps = conn.prepareStatement(sql);
			ps.setString(1, subject);
			ps.setString(2, content);
			int cnd = ps.executeUpdate();
			
			if (cnd > 0) {
				System.out.println("정상적으로 수정됨");
				
				// 첨부파일의 추가, 삭제, 수정
				// attFile 의 각 BoardAtt 가 서로 다른 플래그를 가질 수 있음
				for (int i = 0; i < attFile.size(); i++) {
					BoardAtt att = attFile.get(i);
					switch (att.getFlag()) {
					case "A":
						fileAppend(serial, att);
						break;
					case "D":
						fileDelete(serial, att);
						break;
					case "U":
						fileDelete(serial, att);
						fileAppend(serial, att);
						break;
					}
				}
			}
			
			conn.commit();
		} catch (Exception ex) {
			ex.printStackTrace();
			try {
				conn.rollback();
			} catch (Exception ex2) { }
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) {
			}
		}
	}
	
	public void fileAppend(int pSerial, BoardAtt att) throws Exception {
		String sql = "insert into boardAtt values(seq_boardAtt.nextval, ?, ?, ?)";
		ps = conn.prepareStatement(sql);
		ps.setInt(1, pSerial);
		ps.setString(2, att.getAttFile());
		ps.setString(3, att.getOriFile());
		ps.executeUpdate();
	}
	
	public void fileDelete(int pSerial, BoardAtt att) throws Exception {
		String sql = "delete from boardAtt where pSerial = ? and attFile = ?";
		ps = conn.prepareStatement(sql);
		ps.setInt(1, pSerial);
		ps.setString(2, att.getDelFile());
		ps.executeUpdate();
	}

	// 시리얼 번호를 기준으로 글 삭제
	// 삭제하고자 하는 글을 참조하는 댓글과 파일첨부 목록도 삭제
	public void delete(int serial) {
		try {
			// 해당되는 글이 삭제되고,
			conn.setAutoCommit(false);
			sql = " delete from boardB where bbs_serial = ? ";
			ps = conn.prepareStatement(sql);
			ps.setInt(1,  serial);
			ps.executeUpdate();

			// 하드디스크에 저장되어 있는 삭제 대상 파일
			// 글에 종속된 첨부파일들도 삭제
			// 그것을 위해 boardAtt 로부터 pSerial 에 해당하는 파일 목록을 불러와 list 에 저장시키고
			List<String> delList = new ArrayList<String>();
			sql = " select attFile from boardAtt where pSerial = " + serial;
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			while (rs.next()) {
				delList.add(rs.getString("attFile")); // 파일명을 얻어와 ArrayList 인 delList 에 저장
			}
			
			// 해당 경로에 해당 파일이 존재한다면 삭제
			File file = null;
			for (int i = 0; i < delList.size(); i++) {
				file = new File("D:\\Temp\\" + delList.get(i));
				if (file.exists()) file.delete();
			}
			
			// 실제 디스크 파일이 삭제되었다면 boardAtt 에서 pSerial 에 해당하는 목록 삭제
			sql = " delete from boardAtt where pSerial = ? ";
			ps = conn.prepareStatement(sql);
			ps.setInt(1, serial);
			ps.executeUpdate();
			
			System.out.println("삭제 완료!");
			conn.commit();			
		} catch (Exception ex) {
			ex.printStackTrace();
			try {
				conn.rollback();
			} catch (Exception ex2) { }
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) {
			}
		}
	}

	// 해당 파일 상세와 첨부파일 목록
	public void view(int serial) {
		try {

		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) {
			}
		}
	}

	// pSerial 에 해당하는 답변글 조회
	public void reply(int pSerial, String subject, String content, String id, String pwd) {
		try {
			// 쿼리 작성
			sql = "";
			
			
			// ps 세팅
			// ps 실행 및 rs 에 결과 담기
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				rs.close();
				ps.close();
			} catch (Exception e) {
			}
		}
	}

}
```


	CRUD 테스트
	SELECT2 (+ 계층형 쿼리) / INSERT2 (+ INSERT ALL) / VIEW / REPLY




