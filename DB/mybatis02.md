
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

## mapper 작성하기 (SqlMap)

- mybatis 는 사용자가 기본적인 환경설정 외에 오직 쿼리작성에만 집중할 수 있도록 한다
- 실제 mapper 의 각 태그 요소들에 원하는 쿼리를 작성하고 동적 변수나 파라미터, 반환 타입 정도만 지정하면 충분하다
- 쿼리 내 동적 변수에는 #{_parameter} 혹은 ${_parameter} 형식을 사용하는데,
	* # 으로 시작하면 작은 따옴표가 붙고 $ 로 시작하면 그렇지 않다
	* 용도는 다르지만, 따옴표가 붙지 않는 $ 방식은 sql injection 공격에 취약할 수 있다
	* 이와 같은 방식으로 명시적인 동적 변수를 자동 사용하려면, getter 혹은 setter 가 정의되어 있어야 한다
	* _parameter 부분은 정해지지 않은 파라미터를 의미한다.
	* 이것 대신 명시적인 변수명을 지정할 수 있지만, 실제 투입 데이터의 타입과 일치하지 않을 가능성이 있으므로 _parameter 사용을 기본으로 한다
- 쿼리는 sql developer 와 같은 곳에서 직접 실행해 보고 붙여넣는 식으로 작성하면 정확하고 편하다

### namespace

- 다른 mapper 들과 구분하기 위해 논리적으로 파티셔닝한 영역.
-  사용시 구분은 자바의 전체 패키지명을 기술함으로써 가능하다. '.' 기준으로 세분화

### resultMap, parameterType, resultType

- resultMap : 반환 데이터 타입을 구조화시킨다. 반환값이 기존의 데이터 타입만으로 구성되지 않는 경우 사용
- parameterType, resultType
	* 각각 입력 파라미터 타입, 반환 타입을 지정한다
	* 타입 유형은 기본형, 객체형, resultMap 형 중의 하나가 된다

### select

- select 의 경우 그 검색결과가 여러 개라면 List 컬렉션을 사용한다
- 반환 데이터를 boolean 형식으로 얻고 싶다면, select 의 결과가 0 또는 1 이 되도록 쿼리를 작성한다


### insert, update, delete

- 위 규칙에 따라서 쿼리를 작성한다
- 파라미터와 반환 타입, 쿼리 내 동적 변수 설정 등에 유의할 것








