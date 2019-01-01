
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190101-lightgray.svg?style=flat-square)

# 웹 프로젝트에서 로그인 로직 구현 - html, jsp, servlet, DB connection

> 로그인 페이지에서부터 시작해 검색된 정보를 담는 Bean, DB 에 커넥팅하여 쿼리 날리는 주체인 DAO, DAO 를 활용해 로그인 검증을 하는 LoginOk.java, 성공시 결과를 나타내는 LoginResult.jsp 까지의 흐름을 살펴본다.

- View : Login.html, LoginResult.jsp
- Controller : LoginOk.java (Servlet)
- Model : UsersVo.java, UsersDao.java

## 코드

Login.html (로그인 화면)
```JAVA
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login Page</title>
</head>
<body>

	<form action="LoginOk" method="post">
		아이디 : <input type="text" name="id" size="10"><br/>
		비밀번호 : <input type="text" name="pwd" size="10"><br/>
		<input type="submit" value="Login"><input type="reset" value="cancel">
	</form>
	
</body>
</html>
```


UsersVo.java (DB 의 Users 테이블에 존재하는 정보를 담을 클래스)
```JAVA
package users;

public class UsersVo {
	private String id; // PK
	private String name; // NN
	private String pwd; // NN
	private String email; // UK, NN
	private String phone; // UK, NN
	private String photo;
	private String photoOri;
	private String joinDate; // default sysdate
	
	/*
	 * constructor
	 */
	public UsersVo () { }
	public UsersVo (String id, String name, String pwd, String email, String phone) { // columns of PK or not null
		this.id = id;
		this.name = name;
		this.pwd = pwd;
		this.email = email;
		this.phone = phone;
	}
	
	/*
	 * getter and setter
	 */
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getPwd() {
		return pwd;
	}
	public void setPwd(String pwd) {
		this.pwd = pwd;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getPhone() {
		return phone;
	}
	public void setPhone(String phone) {
		this.phone = phone;
	}
	public String getPhoto() {
		return photo;
	}
	public void setPhoto(String photo) {
		this.photo = photo;
	}
	public String getPhotoOri() {
		return photoOri;
	}
	public void setPhotoOri(String photoOri) {
		this.photoOri = photoOri;
	}
	public String getJoinDate() {
		return joinDate;
	}
	public void setJoinDate(String joinDate) {
		this.joinDate = joinDate;
	}
	
}
```


UsersDao.java (Vo 객체를 활용해 실제 커넥팅과 쿼리를 수행하는 주체)
```JAVA
package users;

import dbConn.DBConn;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

/*
 * DB 의 Member 테이블에 접근하여 CRUD 를 수행하는 클래스
 */
public class UsersDao {
	private Connection conn;
	private String sql;
	private PreparedStatement ps;
	private ResultSet rs;
	
	/*
	 * Constructor - create conn object
	 */
	public UsersDao() {
		this.conn = new DBConn().getConn();
	}
	
	/*
	 * closeRtn - 더 이상 사용하지 않는 자원을 close
	 */
	public void closeRtn() {
		try {
			if (rs != null) rs.close();
			if (ps != null) ps.close();
			if (conn != null) conn.close();
		} catch (Exception ex) {
			ex.printStackTrace();
		}
	}
	
	/*
	 * creat new account
	 */
	public boolean insert(UsersVo vo) {
		boolean result = false;
		
		try {
			conn.setAutoCommit(false);
			// 필수입력사항인 id, name, pwd, email, phone 만 입력
			// photo, photoori 는 추후 개인프로필 수정에서 등록 가능
			sql = "insert into users (id, name, pwd, email, phone) values ( "
					+ "	?, ?, ?, ?, ? ) ";
			
			ps = conn.prepareStatement(sql);
			ps.setString(1, vo.getId());
			ps.setString(2, vo.getName());
			ps.setString(3, vo.getPwd());
			ps.setString(4, vo.getEmail());
			ps.setString(5, vo.getPhone());
			int i = ps.executeUpdate();
			
			if (i > 0) {
				result = true;
				// 로그인 성공 시 커밋
				conn.commit();
			} else {
				// 로그인 실패 시 롤백
				conn.rollback();
			}
			
		} catch (Exception ex) {
			ex.printStackTrace();
			try {
				// 에러 발생 시 롤백
				conn.rollback();
			} catch (Exception ex2) { }
		} finally {
			try {
				closeRtn();
			} catch (Exception ex) {}
		}
		
		return result;
	}
	
	/*
	 * login
	 * 1 이 리턴되면 로그인 성공, 2 가 리턴되면 비밀번호가 맞지 않음, 3 이 리턴되면 아이디가 존재하지 않음
	 */
	public int login(String id, String pwd) {
		int result = 0;
		try {
			
			// 1 이 리턴되면 로그인 성공, 2 가 리턴되면 비밀번호가 맞지 않음, 3 이 리턴되면 아이디가 존재하지 않음
			sql = "select status from ( "
					+ "	select 1 status from users where id = ? and pwd = ? "
					+ "	union all "
					+ "	select 2 status from users where id = ? "
					+ "	union all "
					+ "	select 3 status from dual "
					+ ") where rownum <= 1"; // 1 ~ 3 의 행이 출력됨. 그 중 우선적으로 필터링된 첫번째 행만 캐치
			
			ps = conn.prepareStatement(sql);
			ps.setString(1, id);
			ps.setString(2, pwd);
			ps.setString(3, id);
			rs = ps.executeQuery();
			rs.next(); // ResultSet.next() 메서드를 호출해야 그 값을 불러올 수 있음
			
			result = rs.getInt("status");
			
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				closeRtn();
			} catch (Exception ex) { }
		}
		return result;
	}
	
	/*
	 * modify user info
	 */
	public UsersVo find(UsersVo vo) {
		UsersVo resultVo = new UsersVo();
		
		try {
			sql = "select id, pwd, name from users where "
					+ "	name = ? and email = ? and phone = ? ";
			
			ps = conn.prepareStatement(sql);
			ps.setString(1, vo.getName());
			ps.setString(2, vo.getEmail());
			ps.setString(3, vo.getPhone());
			rs = ps.executeQuery();
			
			if (rs.next()) {
				resultVo.setId(rs.getString("id"));
				resultVo.setPwd(rs.getString("pwd"));
				resultVo.setName(rs.getString("name"));
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				closeRtn();
			} catch (Exception ex) {}
		}
		
		return resultVo;
	}
	
	/*
	 * modify user info
	 */
	public boolean modify(UsersVo vo) {
		boolean b = false;
		
		try {
			
		} catch (Exception ex) {
			ex.printStackTrace();
			try {
				conn.rollback();
			} catch (Exception ex2) { }
		} finally {
			try {
				closeRtn();
			} catch (Exception ex) {}
		}
		
		return b;
	}
	
	/*
	 * appear user info
	 */
	public UsersVo getUserInfo(String id) {
		UsersVo vo = new UsersVo();
		
		try {
			// 일단은 Main 화면에 유저이름 표현하기 위해 이름만 검색함
			sql = "select name from users where id = ?";
			ps = conn.prepareStatement(sql);
			ps.setString(1, id);
			rs = ps.executeQuery();
			
			if (rs.next()) {
				vo.setName(rs.getString("name"));
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				closeRtn();
			} catch (Exception ex) {}
		}
		
		return vo;
	}
	
	/*
	 * delete user info
	 */
	public boolean dropUser(UsersVo vo) {
		boolean b = false;
		
		try {
			
		} catch (Exception ex) {
			ex.printStackTrace();
			try {
				conn.rollback();
			} catch (Exception ex2) { }
		} finally {
			try {
				closeRtn();
			} catch (Exception ex) {}
		}
		
		return b;
	}
	
	/*
	 * rs 를 전달받아 vo 객체에 세팅 후 vo 객체 반환
	 */
	public UsersVo getUsersVo(ResultSet rs) {
		UsersVo vo = new UsersVo();
		
		try {
			
		} catch (Exception ex) {
			ex.printStackTrace();
		}
		
		return vo;
	}
}
```


LoginOk.java (로그인 화면으로부터 넘어온 request 정보 중 id 와 pwd 를 활용해 DAO 로 로그인 검증을 수행하는 주체. 서블릿 파일이다)
```JAVA
package userControl;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import users.UsersDao;

/**
 * Servlet implementation class LoginOk
 */
@WebServlet("/LoginOk")
public class LoginOk extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public LoginOk() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("doGet");
		this.actionDo(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("doPost");
		this.actionDo(request, response);
	}
	
	private void actionDo(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		String id = request.getParameter("id");
		String pwd = request.getParameter("pwd");
		int result = new UsersDao().login(id, pwd);
		
		if (result == 1) {
			response.sendRedirect("LoginResult.jsp");
		} else if (result == 2) {
			response.sendRedirect("Login.html");
		} else if (result == 3) {
			response.sendRedirect("Login.html");
		} else {
			response.sendRedirect("Login.html");
		}
	}

}
```


LoginResult.jsp (로그인에 성공하면 response.sendRedirect 메서드로 보내는 화면)
```JAVA
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login Success</title>
</head>
<body>

	로그인 성공!

</body>
</html>
```



### 참고

- 로그인 화면 단에 관련된 파일은 프로젝트의 WebContent 하위에 위치하고,
- DB 커넥팅과 동적 검증을 수행하는 서블릿 파일들은 프로젝트의 소스 하위에 위치한다
- view 와 controller 를 보다 분명하게 분리하면 완전한 mvc 가 된다











