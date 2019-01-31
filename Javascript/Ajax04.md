
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190130-lightgray.svg?style=flat-square)

# AJAX 04

## DOM 이어서

### Element Interface

- Element 인터페이스는 Node 인터페이스를 상속받아 만들어졌으며 일반적으로 element 나 html tag 로 불려지는 요소들의 속성을 제어할 수 있다
- 속성이라 함은 input > type, name, value, id, class, ... 와 같은 것을 의미하는데,
- Element 인터페이스의 활용으로 이들을 동적으로 핸들링할 수 있다

```
	목적 노드를 선택하고 (result) > createAttribute 로 원하는 노드 위치에 속성 생성 (att)
		> 생성한 속성의 속성값 지정 (att.nodeValue) > 선택한 노드에 속성 세팅 (result.setAttributeNode(att))
		> 이 과정들이 버튼 클릭 이벤트시 수행되도록 조치
		> 삭제 시 result.removeAttribute('class')
```

interface_ele.jsp
```JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Interface Element Page</title>
<style>
	#domElementTest #result {
		width: 400px;
		height: 40px;
		border: 1px solid #121212;
	}
	.color_m {
		background-color: #7777ff;
	}
	.color_w {
		background-color: #ff7777;
	}
</style>
</head>
<body id='bodyResult'>

	<div id='domElementTest'>
		<h2>Interface Element Page</h2>
		<div id='info'>
			setAttrubyte() | getAttribute() 함수를 활용한 엘리먼트 속성 변경<br/>
			남자 버튼은 파란색, 여자 버튼은 빨간색
		</div><br/>
		<label>
			<input type='radio' name='gender' />남자
		</label>
		<label>
			<input type='radio' name='gender' />여자
		</label>
		<label>
			<input type='radio' name='gender' />속성삭제
		</label>
		
		<h3>Result Area</h3>
		<div id='result'></div>
	</div>
	
	<script>
		var zone = document.getElementById('domElementTest');
		var buttons = zone.getElementsByTagName('input');
		var bodyResult = document.getElementById('bodyResult');
		var result = document.getElementById('result');
		
		buttons[0].onclick = function () {
			var att1 = document.createAttribute('class'); // 속성 생성
			var att2 = document.createAttribute('class'); // 속성 생성
			att1.nodeValue = 'color_m'; // 속성값 지정
			att2.nodeValue = 'color_w'; // 속성값 지정
			bodyResult.setAttributeNode(att1); // 원하는 위치에 속성 세팅
			result.setAttributeNode(att2); // 원하는 위치에 속성 세팅
		}
		buttons[1].onclick = function () {
			var att1 = document.createAttribute('class'); // 속성 생성
			var att2 = document.createAttribute('class'); // 속성 생성
			att1.nodeValue = 'color_w'; // 속성값 지정
			att2.nodeValue = 'color_m'; // 속성값 지정
			bodyResult.setAttributeNode(att1); // 원하는 위치에 속성 세팅
			result.setAttributeNode(att2); // 원하는 위치에 속성 세팅
		}
		buttons[2].onclick = function () {
			bodyResult.removeAttribute('class');
			result.removeAttribute('class');
		}
	</script>

</body>
</html>
```

### Element add/remove

- element 속성뿐 아니라 element 자체로 추가/제거 할수 있다
- 이벤트 발생 시 동적으로 태그 자체가 변화하도록 조치하는 것인데,
- ajax 의 이 기능을 사용해서 화면의 일부가 동적으로 반응하는 웹페이지를 만들 수 있다

```JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Element Add or Remove Page</title>
<style>
	#main {
	
	}
	#main #item1 {
		width: 200px;
		height: 300px;
		border: 1px solid black;
		margin: 10px 0 5px 30px;
	}
	#main #item2 {
		width: 200px;
		height: 300px;
		border: 1px solid black;
		margin: 5px 0 0 30px;
	}
</style>
</head>
<body>

	<div id='domElementAddTest'>
		<h2>Element Add or Remove Page</h2>
		<div id='info'>항목을 선택하면 동적으로 select 태그와 option 태그를 생성</div>
		
		<div id='main'>
			<div id='item1'>
				<select size='5' id='item1_sel'>
					<option>산이름</option>
					<option>강이름</option>
					<option>과일이름</option>
				</select>
			</div>
			<div id='item2'>
				<select size='10' id='item2_sel'></select>
			</div>
		</div>
	</div>
	
	<script>
		var sel1 = document.getElementById('item1_sel');
		var sel2 = document.getElementById('item2_sel');
		
		sel1.onchange = function () {
			/* console.log('changed!');
			console.log(sel[sel.selectedIndex].text); */
			switch (sel1.selectedIndex) {
			case 0:
				mnts();
				break;
			case 1:
				rivers();
				break;
			case 2:
				fruits();
				break;
			}
		}
		sel2.onchange = function () {
			
		}
		
		function mnts () {
			var m = ['백두산', '한라산', '치악산', '설악산', '속리산', '금강산', '내장산', '북한산'];
			sel2.length = 0; // select 박스 내용을 초기화
			for (var i = 0; i < m.length; i++) {
				var op = new Option(m[i], m[i]);
				sel2.options[i] = op;
			}
		}
		function rivers () {
			var r = ['한강', '두만강', '낙동강', '금강', '소금강', '압록강', '요단강', '섬진강'];
			sel2.length = 0;
			for (var i = 0; i < r.length; i++) {
				var op = new Option(r[i], r[i]);
				sel2.options[i] = op;
			}
		}
		function fruits () {
			var r = ['딸기', '사과', '오렌지', '배', '귤', '메론', '참외', '바나나', '수박', '두리안', '복숭아', '석류', '망고'];
			sel2.length = 0;
			for (var i = 0; i < r.length; i++) {
				var op = new Option(r[i], r[i]);
				sel2.options[i] = op;
			}
		}
	</script>

</body>
</html>
```

- 이 예제에서 sel1 select 태그 내 각 option 요소들을 클릭하면
- 해당하는 내용이 sel2 의 select 태그 내에 동적으로 나타난다
- 이것은 정확한 노드를 선택해 이벤트 발생 시 각 함수가 실행되도록 함으로써 가능하다

## Ajax 를 활용해 실제 DB 로부터 데이터를 조회해 동적 화면 구성하기 (부서 및 직원 정보 조회)

- 결과적으로 Ajax 방식으로 서버에 요청을 보내고 db 커넥션으로 얻은 결과값을 Json 방식으로 얻어오면 된다
- 가급적 json 을 사용하는 이유는 막대한 정보를 최소한의 용량으로 컨트롤하기 좋기 때문이다
- 이 예제에서는 jsp 화면에서 ajax 로 부서 및 직원에 대한 조회요청을 날리면 서블릿에서 dao 로 조회후 json 방식으로 반환한다
- 한번 쭉 읽어볼것

employee_info.jsp
```JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Information of Emp</title>
<style>
	#infoEmp li {
		list-style: none;
	}
	#infoEmp .item {
		overflow: scroll;
		display: inline-block;
		width: 200px;
		height: 150px;
		margin-right: 10px;
		border: 1px solid black;
		float: left;
	}
	#infoEmp #emp_info {
		overflow: scroll;
		display: inline-block;
		font-size: 9pt;
		background-color: #fff;
		width: 200px;
		height: 300px;
		margin-right: 10px;
		border: 1px solid black;
		float: left;
	}
</style>
</head>
<body>

	<div id='infoEmp'>
		<h2>Information of Emp</h2>
		<p>Ajax 를 활용한 employee 정보 조회</p>
		
		<div id='main'>
			<select id='dept' class='item' size='10'>
			</select>
			<select id='emp' class='item' size='10'>
			</select>
			<div id='emp_info'>
				- 결과
			</div>
		</div>
	</div>
	
	<script>
		function get (id) {
			return document.getElementById(id);
		}
		var dept = get('dept');
		var emp = get('emp');
		var result = get('emp_info');
		var xhr = new XMLHttpRequest();
		
		// 페이지 로드 시 모든 부서번호와 부서명을 가져옴
		funcDept ();
		function funcDept () {
			xhr.open('post', 'getDept.emp');
			xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
			xhr.send();
			xhr.onreadystatechange = function () {
				if (xhr.readyState == 4 && xhr.status == 200) {
					var data = xhr.responseText;
					var list = JSON.parse(data);
					
					for (var i = 0; i < list.length; i++) {
						var op = new Option(list[i].deptName, list[i].deptId);
						dept.options[i] = op;
					}
				}
			}
		}
		
		// 부서 선택 시 해당 부서의 모든 직원정보 가져옴
		dept.onchange = function () {
			emp.length = 0;
			var deptId = -1;
			var index = dept.selectedIndex;
			deptId = dept[index].value;
			
			xhr.open('post', 'getEmp.emp');
			xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
			xhr.send("deptId=" + deptId);
			xhr.onreadystatechange = function () {
				if (xhr.readyState == 4 && xhr.status == 200) {
					var data = xhr.responseText;
					var list = JSON.parse(data);
					
					for (var i = 0; i < list.length; i++) {
						var op = new Option(list[i].eName, list[i].empId);
						emp.options[i] = op;
					}
				}
			}
		}
		
		// 직원 선택 시 해당 직원의 모든 정보 가져옴
		emp.onchange = function () {
			var empId = -1;
			var index = emp.selectedIndex;
			empId = emp[index].value;
			// alert('emp key : ' + emp[index].text + ', value : ' + empId);
			
			xhr.open('post', 'getEmpInfo.emp');
			xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
			xhr.send("empId=" + empId);
			xhr.onreadystatechange = function () {
				if (xhr.readyState == 4 && xhr.status == 200) {
					var data = xhr.responseText;
					var list = JSON.parse(data);
					var str = '<h4>결과</h4>';
					
					str += '<ul>';
					str += '<li> 사원 번호 : ' + list[0].empId + '</li>';
					str += '<li> 사원명 : ' + list[0].eName + '</li>';
					str += '<li> 이메일 : ' + list[0].email + '</li>';
					str += '<li> 전화번호 : ' + list[0].phone_number + '</li>';
					str += '<li> 급여 : ' + list[0].salary + '</li>';
					str += '<li> 매니저 아이디 : ' + list[0].mId + '</li>';
					str += '<li> 부서 아이디 : ' + list[0].deptId + '</li></ul>';
					
					result.innerHTML = str;
				}
			}
		}
	</script>

</body>
</html>
```

EmpVo.java
```JAVA
package ajax.dao;

public class EmpVo {
	/*
	 * field
	 */
	private int employee_id;
	private String first_name;
	private String email;
	private String phone_number;
	private int salary;
	private int manager_id;
	
	private int department_id;
	private String department_name;
	
	/*
	 * return json
	 */
	public String toJson () {
		String str = String.format("{'empId':'%d',"
							+ " 'eName':'%s',"
							+ " 'email':'%s',"
							+ " 'phone_number':'%s',"
							+ " 'salary':'%d',"
							+ " 'mId':'%d',"
							+ " 'deptId':'%d',"
							+ " 'dName':'%s'}",
							employee_id, first_name, email, phone_number, salary, manager_id, department_id, department_name);
		str = str.replace("\'", "\""); // json 은 큰따옴표로 묶어야 함..
		
		return str;
	}
	
	/*
	 * getter & setter
	 */
	public int getEmployee_id() {
		return employee_id;
	}
	public void setEmployee_id(int employee_id) {
		this.employee_id = employee_id;
	}
	public String getFirst_name() {
		return first_name;
	}
	public void setFirst_name(String first_name) {
		this.first_name = first_name;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getPhone_number() {
		return phone_number;
	}
	public void setPhone_number(String phone_number) {
		this.phone_number = phone_number;
	}
	public int getSalary() {
		return salary;
	}
	public void setSalary(int salary) {
		this.salary = salary;
	}
	public int getManager_id() {
		return manager_id;
	}
	public void setManager_id(int manager_id) {
		this.manager_id = manager_id;
	}
	public int getDepartment_id() {
		return department_id;
	}
	public void setDepartment_id(int department_id) {
		this.department_id = department_id;
	}
	public String getDepartment_name() {
		return department_name;
	}
	public void setDepartment_name(String department_name) {
		this.department_name = department_name;
	}
}
```

EmpDao.java
```JAVA
package ajax.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.List;

import com.myweb.memberBean.DBConn;

public class EmpDao {
	
	Connection conn = null;
	PreparedStatement ps = null;
	ResultSet rs = null;
	
	public EmpDao () {
		this.conn = new DBConn().getConn();
	}
	
	private void closeSet () {
		try {
			if (rs != null) rs.close();
			if (ps != null) ps.close();
			if (conn != null) conn.close();
		} catch (Exception ex) {
			ex.printStackTrace();
		}
	}
	
	public List<DeptVo> getDept () {
		List<DeptVo> list = new ArrayList<DeptVo>();;
		final String sql = " select DEPARTMENT_ID, DEPARTMENT_NAME from departments order by department_id asc ";
		
		try {
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			
			while (rs.next()) {
				DeptVo vo = new DeptVo();
				vo.setDepartment_id(rs.getInt("DEPARTMENT_ID"));
				vo.setDepartment_name(rs.getString("DEPARTMENT_NAME"));
				list.add(vo);
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			closeSet();
		}
		
		return list;
	}

	// true 면 deptId 로 emp 의 아이디와 이름만 조회
	// false 면 empId 로 emp 의 모든 정보 조회
	public List<EmpVo> getEmp (String Id, boolean flag) {
		List<EmpVo> list = new ArrayList<EmpVo>();;
		String sql = "";
		
		if (flag) {
			sql = " select * from employees where department_id = ? ";
		} else {
			sql = " select * from employees where employee_id = ? ";
		}
		
		try {
			ps = conn.prepareStatement(sql);
			ps.setString(1, Id);
			rs = ps.executeQuery();
			
			while (rs.next()) {
				EmpVo vo = new EmpVo();
				vo.setEmployee_id(rs.getInt("employee_id"));
				vo.setFirst_name(rs.getString("first_name"));
				vo.setEmail(rs.getString("email"));
				vo.setPhone_number(rs.getString("phone_number"));
				vo.setSalary(rs.getInt("salary"));
				vo.setManager_id(rs.getInt("manager_id"));
				vo.setDepartment_id(rs.getInt("department_id"));
				// vo.setDepartment_name(rs.getString("department_name"));
				list.add(vo);
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			closeSet();
		}
		
		return list;
	}
}
```

EmpServlet.java
```JAVA
package com.myweb.ajaxServlet;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import ajax.dao.DeptVo;
import ajax.dao.EmpDao;
import ajax.dao.EmpVo;

/**
 * Servlet implementation class EmpServlet
 */
@WebServlet("*.emp")
public class EmpServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
    
    public EmpServlet() {
        super();
    }

	/**
	 * doGet
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doPost(request, response);
	}

	/**
	 * doPost
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("utf-8");
		response.setCharacterEncoding("utf-8");
		PrintWriter out = response.getWriter();
		String jsonResult = "";

		String page = request.getRequestURI();
		page = page.substring(1, page.lastIndexOf("."));
		System.out.println("page: " + page);
		
		switch (page) {
		case "getDept":
			jsonResult = "";
			jsonResult = getDept();
			System.out.println("json result: " + jsonResult);
			
			out.print(jsonResult);
			break;
		case "getEmp":
			String deptId = request.getParameter("deptId");
			System.out.println("deptId: " + deptId);
			jsonResult = "";
			jsonResult = getEmp(deptId);
			System.out.println("json result: " + jsonResult);
			
			out.write(jsonResult);
			break;
		case "getEmpInfo":
			String empId = request.getParameter("empId");
			System.out.println("empId: " + empId);
			jsonResult = "";
			jsonResult = getEmpInfo(empId);
			System.out.println("json result: " + jsonResult);
			
			out.print(jsonResult);
			break;
		}
	}

	// 부서명과 부서코드 (최초 페이지 로드될때 실행)
	public String getDept () {
		EmpDao dao = new EmpDao();
		StringBuilder sb = new StringBuilder("[");
		List<DeptVo> list = dao.getDept();
		
		for (int i = 0; i < list.size(); i++) {
			String json = list.get(i).toJson();
			sb.append(json);
			sb.append(", ");
		}
		
		String result = sb.substring(0, sb.lastIndexOf(","));
		result += "]";
		
		return result;
	}
	
	// 해당부서의 직원명과 사번 (각 부서명을 클릭하면 실행)
	public String getEmp (String deptId) {
		EmpDao dao = new EmpDao();
		StringBuilder sb = new StringBuilder("[");
		List<EmpVo> list = dao.getEmp(deptId, true);
		
		for (int i = 0; i < list.size(); i++) {
			String json = list.get(i).toJson();
			sb.append(json);
			sb.append(", ");
		}
		
		String result = sb.substring(0, sb.lastIndexOf(","));
		result += "]";
		
		return result;
	}
	
	// 해당직원의 세부정보
	public String getEmpInfo (String empId) {
		EmpDao dao = new EmpDao();
		StringBuilder sb = new StringBuilder("[");
		List<EmpVo> list = dao.getEmp(empId, false);
		
		sb.append(list.get(0).toJson());
		for (int i = 0; i < list.size(); i++) {
			String json = list.get(i).toJson();
			sb.append(", ");
			sb.append(json);
		}
		
		sb.append("]");		
		return sb.toString();
	}
	
}
```








