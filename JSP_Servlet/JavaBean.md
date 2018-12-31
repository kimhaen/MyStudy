
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-181231-lightgray.svg?style=flat-square)

# 자바 빈 사용하기

> 이전 자바 프로젝트(개인 일기장 만들기)에서 자바 빈과 유사한 코딩을 했었다. ValueObject 파일이 그것인데, 이것은 해당 프로그램에서 반복적으로 사용할 공통의 정보 혹은 정보들의 집합을 저장하는 객체이다.

- 자바 빈은 VO 와 같이 웹 페이지에서 반복적으로 사용할 공통의 정보를 저장하는 객체이다.
- 자바 프로젝트에서 VO 는 `Vo vo = new Vo();` 형식으로 사용되었으나, JSP 에서는 JSP 액션 태그로 사용한다 >> `<jsp:[액션태그] 속성="값">`
- 액션 태그에는 **useBean**, **getProperty**, **setProperty** 가 있다

## 자바 빈 예제 - Student.java
```java
package student;

public class Student {
	private String name;
	private int age;
	private int grade;
	private int studentNum;
	
	/*
	 * constructor
	 */
	public Student() { }

	/*
	 * getter and setter
	 */
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getGrade() {
		return grade;
	}
	public void setGrade(int grade) {
		this.grade = grade;
	}
	public int getStudentNum() {
		return studentNum;
	}
	public void setStudentNum(int studentNum) {
		this.studentNum = studentNum;
	}
}
```

* 뭐 특별할 것은 없다.<br>데이터를 저장할 멤버필드와 생성자, getter/setter 로 구성된다.

## 빈 활용하기
```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%-- 빈을 사용하겠다고 명시 --%>
<jsp:useBean id="student" class="student.Student" scope="page"/>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Set Student</title>
</head>
<body>
	
	<%-- 빈의 각 필드에 값 세팅 --%>
	<jsp:setProperty name="student" property="name" value="홍길동"/>
	<jsp:setProperty name="student" property="age" value="18"/>
	<jsp:setProperty name="student" property="grade" value="3"/>
	<jsp:setProperty name="student" property="studentNum" value="198"/>
	
	<%-- 세팅된 값 출력 --%>
	이름: <jsp:getProperty name="student" property="name"/><br/>
	나이: <jsp:getProperty name="student" property="age"/><br/>
	학년: <jsp:getProperty name="student" property="grade"/><br/>
	번호: <jsp:getProperty name="student" property="studentNum"/><br/>
	
</body>
</html>
```

* jsp 파일에서는 java 서블릿 빈 클래스를 기반으로 useBean, setProperty, getProperty 액션 태그를 적절히 활용하면 된다.
* 특히 useBean 태그에서 id 는 객체의 이름(임의지정), class 는 빈 클래스가 존재하는 경로, scope 는 해당 자바 빈이 활용되는 범주를 의미한다
* scope 에는 page, request, session, application 이 있다. 각각 해당 페이지, 요청된 페이지, 웹 브라우저의 생명주기, 웹앱 전체 생명주기를 범주로 갖는다.










