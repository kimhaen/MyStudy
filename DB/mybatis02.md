
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190226-lightgray.svg?style=flat-square)

# 마이바티스 이어서

## SqlSession 활용 예제

- SqlSession 을 만들기 위해 생성된 SqlSessionFactory 는 싱글톤 방식으로 사용된다
- 최종적으로 sqlSession 을 통해서 마이바티스의 쿼리들을 사용할 수 있다
- 각 쿼리는 메서드 방식으로 활용되는데, select 의 경우에는 sqlSession.select([namespace].[id], [parameter]) 의 형태로 사용한다
	* namespace 는 논리적으로 분할된 각 파티션을 의미한다
- 다음은 sqlSession 으로 member.xml 에 세팅된 selectId 요소의 쿼리를 사용한 예제이다

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

**MybatisMemberDao.java**
```JAVA
package myBatis;

import javax.servlet.http.HttpServletRequest;

import org.apache.ibatis.session.SqlSession;

import com.myweb.memberBean.MemberVo;

public class MybatisMemberDao {
	private SqlSession sqlSession;
	HttpServletRequest request;
	
	public MybatisMemberDao () {
		sqlSession = MemberFactory.getFactory().openSession();
		
		if (sqlSession == null) {
			System.out.println("sqlSession 생성 중 오류 발생");
		} else {
			System.out.println("sqlSession 생성 성공");
		}
	}
	
	public MybatisMemberDao (HttpServletRequest request) {
		this();
		this.request = request;
	}
	
	public void select (String id) {
		MemberVo vo = null;
		
		if (sqlSession != null) {
			try {
				vo = (MemberVo) sqlSession.selectOne("member.selectId", id);
				this.request.setAttribute("vo", vo);
			} catch (Exception ex) { ex.printStackTrace(); }
		} else {
			System.out.println("sqlSession 이 없습니다");
		}
		
	}
	
	public static void main(String[] args) {
		new MybatisMemberDao();
	}
}

```

- dao 는 기본 생성자를 통해 sqlSession 을 생성하고, 오버로딩된 생성자로 request 객체가 투입되었다면 그것을 전역변수로 받는다
- 이후 dao.select('hong'); 명령을 실행하면 매핑된 쿼리에 따라 hong 아이디의 select 결과를 MemberVo 객체에 담아 request 에 세팅한다

**view.jsp**
```JSP
<%@page import="com.myweb.memberBean.MemberVo"%>
<%@page import="myBatis.MybatisMemberDao"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>MyBatis 를 활용하여 Member 테이블의 데이터 핸들링하기</title>
</head>
<body>

	<div id='myBatis_member'>
		<h2>MyBatis 를 활용하여 Member 테이블의 데이터 핸들링하기</h2>
		
		<%
			String id = "hong";
			MybatisMemberDao dao = new MybatisMemberDao(request);
			dao.select(id); // mybatis 로 select 후 request 객체에 vo 세팅
		%>
		
		<ul>
			<li>아이디: ${requestScope.vo.id }</li>
			<li>이름: ${requestScope.vo.irum }</li>
			<li>암호: ${requestScope.vo.pwd }</li>
			<li>이메일: ${requestScope.vo.email }</li>
			<li>연락처: ${requestScope.vo.phone }</li>
			<li>등록일: ${requestScope.vo.rdate }</li>
			<li>우편번호: ${requestScope.vo.postal }</li>
			<li>주소: ${requestScope.vo.address }</li>
			<li>학년: ${requestScope.vo.grade }</li>
		</ul>
		<img src='/images/${requestScope.vo.photo }' alt="profile image" />
	</div>

</body>
</html>
```

- 뷰 페이지에서는 dao 의 select 호출을 통해 request 객체에 담긴 vo 객체의 데이터들을 화면에 출력한다







