
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190122-lightgray.svg?style=flat-square)

## JSP 게시판 만들기 - dao 로 CRUD 모듈화 하기, 페이징 처리

### 모듈화



### 페이징 처리하기 (member 목록에 대해서)

#### 강사님 방식

* pageCompute() 메서드를 활용해 각 변수에 해당하는 정보를 자동 계산함
* 변수 (페이지 개념 + 블럭 개념)
	- listSize : 각 페이지 당 출력 개수
	- blockSize : 하단에 나타나는 페이지의 크기
	- startPage : blockSize 에 따른 첫 페이지 번호
	- endPage : blockSize 에 따른 마지막 페이지 번호
	- startNo : listSize 에 따른 각 페이지 첫 멤버 번호
	- endNo : listSize 에 따른 각 페이지 마지막 멤버 번호

	- totSize: 전체 멤버 사이즈
	- totPage : 전체 페이지
	- totBlock : 전체 블럭
	- nowPage : 현재 페이지, 각 페이지번호를 클릭하면 그 번호가 현재 페이지가 됨
	- nowBlock : 현재 블럭
* 구하는 방식
	- totSize : select count(*) from member where [condition];
	- totPage : (int) Math.ceil(totSize / (double)listSize), 나머지가 존재한다면 올림수가 되도록 ceil 사용함
	- totBlock : (int) Math.ceil(totPage / (double)blockSize), 나머지가 존재한다면 올림수가 되도록 ceil 사용함
	- nowPage : (int) Math.ceil(nowPage / (double)blockSize)
	- nowBlock : 

	- endNo : nowPage * listSize, 이 값이 전체 건수보다 많아진다면 totSize 로 보정해야 함
	- startNo : endNo - listSize + 1

	- endPage : nowBlock * blockSize, 이것도 전체 블럭 사이즈보다 커진다면 totBlock 으로 보정해야 함
	- startPage : endPage - blockSize + 1
* 페이지와 블럭의 사이즈에 따라 페이지 버튼의 구조가 달라짐

* list() 메서드는 검색어와 현재 페이지(최초 실행 시에는 1이 투입됨)를 매개변수로 입력받아 pageCompute() 실행 결과에 따른 쿼리를 수행함
* pageCompute() 는 list() 메서드와 동일한 조건의 쿼리 검색결과에 따른 변수 처리를 수행함


MemberDao.java
```JAVA
public class MemberDao {

	...

	public Map<String, MemberVo> listAll (String search, int nowPage) {
		this.nowPage = nowPage;
		pageCompute(search);
		
		Map<String, MemberVo> memberMap = new HashMap<String, MemberVo>();
		// pageNo 에 따른 rno 계산
		// page		: 1, 2, 3, 4,  5,  6,  7
		// startNo	: 1, 4, 7, 10, 13, 16, 19
		// 차이		: 0, 2, 4, 6,  8,  10, 12
		// int startNo = pageNo + (2 * (pageNo - 1));
		// int lastNo = startNo + 2;
		
		try {
			// 아이디, 최초등록일은 수정 불가
			// 비번은 확인용이고, 비번변경은 추가 페이지에서 처리
			final String sql = " select * from ( "
								+ "		select rownum rno, s.* from ( "
								+ "			select * from member "
								+ "			order by name "
								+ "		) s "
								+ "	) where rno between ? and ? ";
			ps = conn.prepareStatement(sql);
			ps.setInt(1, startNo);
			ps.setInt(2, endNo);
			
			rs = ps.executeQuery();
			
			while (rs.next()) {
				MemberVo vo = new MemberVo();
				vo.setId(rs.getString("id"));
				vo.setIrum(rs.getString("name"));
				vo.setPhone(rs.getString("phone"));
				vo.setEmail(rs.getString("email"));
				
				memberMap.put(String.valueOf(rs.getInt("rno")), vo);
			}
			
		} catch (Exception ex) {
			ex.printStackTrace();
		} finally {
			try {
				closeSet();
			} catch (Exception ex) { }
		}
		
		return memberMap;
	}
	
	public void pageCompute (String search) {
		try {
			final String sql = " select count(*) cnt from member ";
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			
			if (rs.next()) {
				totSize = rs.getInt("cnt");
				totPage = (int) Math.ceil(totSize / (double)listSize);
				totBlock = (int) Math.ceil(totPage / (double)blockSize);
				nowBlock = (int) Math.ceil(nowPage / (double)blockSize);
				
				endNo = nowPage * listSize;
				startNo = endNo - listSize + 1;
				if (endNo > totSize) endNo = totSize;
				
				endPage = nowBlock * blockSize;
				startPage = endPage - blockSize + 1;
				if (endPage > totPage) endPage = totPage;
			}
			
			System.out.println("listSize: " + listSize);
			System.out.println("blockSize: " + blockSize);
			System.out.println("nowPage: " + nowPage);
			System.out.println("totSize: " + totSize);
			System.out.println("totPage: " + totPage);
			System.out.println("totBlock: " + totBlock);
			System.out.println("nowBlock: " + nowBlock);
			System.out.println("endNo: " + endNo);
			System.out.println("startNo: " + startNo);
			System.out.println("endPage: " + endPage);
			System.out.println("startPage: " + startPage);
			System.out.println("===========================");
		} catch(Exception ex) {
			ex.printStackTrace();
		}
	}

}
```

- 위 코드에서는 전체검색을 수행하기 위해 listAll 로 명명하고 쿼리는 조건 없이 전체검색 되도록 함
- 매개변수로 투입되는 search 변수는 일단 사용되지 않지만, 조건검색 시에는 사용됨

list.jsp
```HTML
<%@page import="java.util.Map"%>
<%@page import="java.util.List"%>
<%@page import="com.myweb.memberBean.MemberVo"%>
<%@page import="com.myweb.memberDao.MemberDao"%>
<%@page import="java.sql.ResultSet"%>
<%@page import="java.sql.PreparedStatement"%>
<%@page import="com.myweb.memberBean.DBConn"%>
<%@page import="java.sql.Connection"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>회원 목록 조회</title>
<link rel='stylesheet' href='./css/member.css' />
<script src='./js/member.js'></script>
</head>
<body>

	<!-- 상단에 검색 바 -->
	<!-- 본문에 No, 아이디, 성명, 연락처, 이메일 찍어주기 -->
	<!-- 하단에 페이징 처리 (맨처음, 이전, ..., 다음, 맨끝 -->
	<div id='list'>
		<div id='title'>
			<img src='./images/damuni.jpg' id='damuni'/>
			<h1>회원 목록 조회</h1>
		</div>
		<form name='member' method='post'> <!-- action 속성을 생략하면 자기 자신을 호출한다 -->
			<input type='search' name='search' value="${param.search}"/>
			<input type='hidden' name='searchHidden' value='' />
			<input type='hidden' name='id' value='' />
			<input type='button' name='btnSearch' value='검색' />
			<input type='button' name='btnSearchAll' value='전체검색' />
			<input type='hidden' name='nowPage' value='${(param.nowPage == null)? 1 : param.nowPage}' />
		</form>
		
		<div id='subject'>
			<span class='no'>No</span>
			<span class='id' style='cursor: default;'>ID</span>
			<span class='name'>NAME</span>
			<span class='phone'>PHONE</span>
			<span class='email'>EMAIL</span>
		</div>
		<div id='memberList'>
			<%
				String search = "";
				int nowPage = 1;
				MemberDao dao = new MemberDao();
				
				if (request.getParameter("nowPage") != null) {
					nowPage = Integer.parseInt(request.getParameter("nowPage"));
				}
				
				if (request.getParameter("search") != null && !request.getParameter("search").equals("")) {
					search = request.getParameter("search");
					Map<String, MemberVo> map = null;
					MemberVo vo = null;
					
					// 전체검색 클릭시 자동으로 "SelectAll" 이 입력됨
					if (search.equals("SelectAll")) {
						map = dao.listAll(search, nowPage);
					} else {
						// map = dao.list(search, 1, 3);
					}
					
					if (map.size() == 0) {
						out.print("<script>alert('검색된 데이터가 없습니다')</script>");
					}
					
					int no = dao.startNo;
					for (int i = 0; i < map.size(); i++) {
						vo = map.get(Integer.toString(no));
			%>
						<div class='row'>
							<span class='no'><%=no %></span>
							<span class='id'><a href='#' onclick='modify(this.innerHTML)'><%=vo.getId() %></a></span>
							<span class='name'><%=vo.getIrum() %></span>
							<span class='phone'><%=vo.getPhone() %></span>
							<span class='email'><%=vo.getEmail() %></span>
						</div>
			<%
						no++;
					}
				} else {
			%>
					<div class='row'>
						<span class='no'></span>
						<span class='id'></span>
						<span class='name'></span>
						<span class='phone'>검색어를 입력해 주세요!</span>
						<span class='email'></span>
					</div>
			<%
				}
			%>
		</div>
		
		<div id='btnZone'>
			<%if (dao.nowBlock > 1) { %>
				<input type='button' value='HEAD' onclick='movePage(1)' />
				<input type='button' value='PRE' onclick='movePage(<%=dao.startPage - 1%>)' />
			<%} %>
			
			<%for (int j = dao.startPage; j <= dao.endPage; j++) { %>
				<input class='btnCircle' type='button' value=<%=j %> onclick='movePage(<%=j %>)' <%=(dao.nowPage == j)? "disabled" : "" %> />
			<%} %>
			<%if (dao.nowBlock < dao.totBlock) { %>
				<input type='button' value='NEXT' onclick='movePage(<%=dao.endPage + 1 %>)' />
				<input type='button' value='TAIL' onclick='movePage(<%=dao.totPage %>)' />
			<%} %>
		</div>
	</div>
	
	<script>mainList()</script>
	<script>
		
	</script>

</body>
</html>
```

