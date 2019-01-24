
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190124-lightgray.svg?style=flat-square)

# 게시판 웹앱을 위한 DB 계정 생성 및 테이블 생성

## sysdba 로 사용자 생성하기

- cmd 에서 sqlplus 에 sysdba 권한으로 접속해서 사용자를 생성한다
- 생성 시 식별자(비밀번호), 기본(default) 테이블스페이스, 임시 테이블 스페이스를 지정한다
- 계정이 생성되면, grant 명령으로 세션 생성, 테이블 생성, 뷰 생성 권한을 부여하고,
- 지정한 테이블 스페이스에 대한 메모리 할당량을 부여한다 (보통 50m, 100m)
- 만약 이외의 테이블 스페이스에서도 테이블 생성 할당량을 부여하고자 한다면, 모든 테이블 스페이스에 대한 권한을 부여하면 된다

```SQL
SQL> sqlplus sys as sysdba;

SQL> show user;
USER id "SYS"

SQL> create user daesungra identified by 0000
	default tablespace users
	temporary tablespace temp;
User created.

SQL> alter user daesungra quota 100m on users;
User altered.

SQL> grant create session, create table, create view to daesungra;
Grant succeeded.

SQL> grant unlimited tablespace to daesungra;
Grant succeeded.
```

## 생성한 계정으로 접속해 테이블 생성하기

- 생성할 테이블은 게시판을 위한 board, 댓글을 위한 comments 이다

```SQL
SQL> conn daesungra;
Enter password:
Connected.

SQL> select * from tab;

TNAME	TABTYPE		CLUSTERID
---------------------------------

SQL> create table board (
	no number constraint BOARD_NO_PK primary key,
	id varchar2(30),
	title varchar2(30),
	content varchar2(100),
	gNo number,
	step number,
	indent number,
	count number,
	bDate date default sysdate
	);
Table created.

SQL> create table comments (
	no number,
	bNo number,
	id varchar2(30),
	content varchar2(50),
	cDate date default sysdate,
	CONSTRAINT COMMENTS_BNO_FK foreign key (bNo)
	REFERENCES board (no)
	);
Table created.
```

- 이로써 게시판을 위한 테이블 생성이 완료되었다
- 테이블 생성의 결과는 다음과 같다

![board.png](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/table_board.PNG)
![comments.png](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/table_comments.PNG)






