
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/author-181218-lightgray.svg?style=flat-square)

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

1. DBConnect 클래스


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


2. BoardAVo, BoardBVo, BoardAtt 클래스


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


3. CRUD 및 view, reply 기능을 테스트하는 클래스 - DBTest, BoardATest, BoardBTest






