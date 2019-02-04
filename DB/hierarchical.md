
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190131-lightgray.svg?style=flat-square)

# 게시판 글과 답글, 댓글을 위한 기본적인 계층형 쿼리 정리

- 총 두 가지 방법이 있다.
- 게시판 자체를 계층형을 고려하여 구성하거나[(1)](#계층형-게시판-만들기), **start with-connect by** 문법을 활용해 애초에 계층형으로 쿼리를 실행하는 방식[(2)](#계층형-쿼리-사용하기) 이다
- 후자의 방법은 부모와 자식 간의 계층 관계를 명시하면 그에 따른 정렬 결과를 나타낸다
- 각 계층 그룹별로 정렬하고자 한다면, **siblings** 키워드를 사용한다
- 이하 예제에서 사용할 테이블명은 **BOARD**, 시퀀스명은 **SEQ_BOARD** 이다

## 계층형 게시판 만들기

### 게시판 테이블 필수 구성요소

- 계층형 정렬을 위해서는 다음의 컬럼들이 필요하다
	* **serial** (자신의 시리얼)
	* **gSerial** (그룹 시리얼)
	* **pSerial** (바로 윗 부모 시리얼)
	* **depth** (같은 그룹 내 정렬을 위한 번호)
	* **indent** (답글 수준 또는 들여쓰기를 위한 깊이)
- 기본 정렬은 **gSerial** 별로 **desc** 한다 (최신글 순)
- **depth** 의 경우, 가장 오래된 답글이 상단에 오도록 정렬한다 (오래된 답글 순)
	* 새 답글을 등록할 때 참조하는 글의 **indent** 가 0 이라면(최상위 글) 그룹 내 전체 글 개수를 구하고 그것으로부터 +1 된 **depth** 를 삽입하는 과정이 필요하다 (그룹 맨 아래 추가)
	* 만약 답답글이라면(참조하는 글의 **indent** > 0) 참조하는 글 이하 모든 답글들의 **depth** 를 1 씩 증가시키고 그 중간에 답답글을 끼워넣는 쿼리를 실행한다 (같은 그룹 내)
	* 그냥 최신 답글 순이라면 일괄적으로 참조하는 글 바로 아래에 추가하고 나머지는 1 씩 증가시키면 되므로 상대적으로 더 쉽다 (답답글도 마찬가지)
- **indent** 의 경우, 부모 글의 indent 에서 1 을 더하며, 화면에 나타낼 때는 각 수준에 따라 들여쓰기한다
- 필수컬럼 외 id, pwd, title, content 등의 요소들은 상황에 따라 추가한다
- 정렬은 **gSerial desc, depth asc** 하고 행 넘버를 추가한 다음의 쿼리를 사용한다

**query_sort**
```SQL
select * from (
    select rownum rno, s.* from (
        select * from board
            order by gSerial desc, depth asc
    ) s
);
```

### 최상위 글 등록하기

- 최상위 글이므로 **gSerial** 은 **serial** 과 같고,
- 나머지 **pSerial, depth, indent** 는 모두 0 이다

**query_insert**
```SQL
insert into board (serial, id, title, content,
		gSerial, pSerial, depth, indent, bDate)
	values (seq_board.nextval, 'hong', seq_board.currval || ' - hihi', seq_board.currval || ' - 안녕하세요~',
		seq_board.currval, 0, 0, 0, sysdate);
```

- 이 쿼리대로 우선 5 개 글을 등록하고 정렬한 결과는 다음과 같다 (최신글 순)

![mainContent](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/hierarchical_fiveMainContent.PNG)

### 최상위 글에 대한 답글 등록하기

- 답글도 새 글이므로 새로운 시리얼(**serial**) 을 부여하고, 나머지 요소들은 자신이 참조하는 부모 글의 요소들을 기반으로 결정한다
- 그룹 시리얼(**gSerial**) 은 부모 글의 그룹 시리얼(**gSerial**),
- 부모 시리얼(**pSerial**) 은 부모 글의 시리얼(**serial**),
- 그룹 내 정렬을 위한 **depth** 는 같은 그룹 내 모든 글 수를 카운트(count(*)) 하고 그 카운트 대로 삽입하면 된다 (최상위 글의 **depth** 가 인덱스 0 이므로 카운트 결과는 자동으로 인덱스 +1 효과)
- **indent** 는 참조하는 글의 **indent** + 1 (최상위 글에 대한 답글이므로, 0 + 1)
- 결과적으로 같은 그룹 내 모든 글 수를 카운트하는 쿼리와 새로운 답글을 삽입하는 쿼리 총 두 개가 필요하다

**boardDao.java**
```JAVA
...

public boolean insertReply (HttpServletRequest request) {

	boolean b = false;

	// 참조하는 부모 글의 시리얼과 그룹 시리얼, indent, depth
	int serial = -1;
	int gSerial = -1;
	int indent = -1;
	int depth = -1;
	String sql = "";

	try {
		serial = Integer.parseInt(request.getParameter("serial"));
		gSerial = Integer.parseInt(request.getParameter("gSerial"));
		indent = Integer.parseInt(request.getParameter("indent"));
	} catch (Exception ex) { ex.printStackTrace(); }

	if (indent == 0) { // 참조하는 글이 최상위 글이라면, depth 최하단에 삽입
		try {
			conn.setAutoCommit(false);
			// 1. 같은 그룹의 모든 글 수를 카운트(cnt)
			// 최상위 글 한 개는 무조건 존재하므로, cnt 결과는 항상 1 이상
			sql = " select count(*) cnt from board where gSerial = ? ";
			ps = conn.prepareStatement(sql);
			ps.setInt(1, gSerial);
			rs = ps.executeQuery();

			if (rs.next()) {
				depth = rs.getInt("cnt"); // cnt 값이 5 라면 그룹의 마지막 인덱스는 4 이므로 추가 글의 depth 에는 5 그대로 삽입하면 된다
			}

			rs.close();
			ps.close();

			// 2. 새로운 답글 등록
			sql = " insert into board (serial, id, title, content, "
				+ "	gSerial, pSerial, depth, indent, bDate)"
				+ " values (seq_board.nextval, 'hong', seq_board.currval || ' - hihi', seq_board.currval || ' - 안녕하세요~', "
				+ "	?, ?, ?, ?, sysdate) ";
			ps = conn.prepareStatement(sql);
			ps.setInt(1, gSerial);
			ps.setInt(2, serial);
			ps.setInt(3, depth); // cnt 결과값 그대로 삽입
			ps.setInt(4, indent + 1); // 답글이므로 +1
			int result = ps.executeUpdate();

			if (result > 0) {
				b = true;
				conn.commit();
			}
		} catch (Exception ex) {
			try {
				conn.rollback();
				ex.printStackTrace();
			} catch (Exception ex) { }
		} finally {
			try {
				closeSet();
			} catch (Exception ex) { ex.printStackTrace(); }
		}
	} else if (indent > 0) {
		...
	}

	return b;
}

...
```

- 테스트를 위해 3 번 글에 대한 답글 세 개를 등록하고 정렬한 결과는 다음과 같다

![reply01](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/hierarchical_reply01.png)

- 최상위 글들은 최신순으로 정렬되었고, 3 번 글에 대한 답글들은 오래된 순으로 정렬된 것을 확인할 수 있다
- 최상위 글에 대한 1차 답글이므로 **indent** 는 모두 1 이다

### 답글의 답글 등록하기

- 1차 답글의 삽입 원리와 같다
- 다만 답글의 답글 또한 같은 부모를 참조하는 답답글이 여러 개 있을 경우 최신순이 아닌 오래된 순으로 정렬해야 하므로,
- 일단 참조하는 부모 글의 **depth** 를 가져오고, 그것에 같은 부모를 공유하는 답답글들의 개수를 카운트 해서 더한 결과 + 1 이 자신의 **depth** 가 된다
- 자신의 **depth** 이하 모든 답글들의 **depth** 는 1 씩 증가시키고 자신은 그 사이에 삽입되도록 한다
- 결과적으로 참조하는 글의 **depth** 를 얻은 뒤,
- 같은 부모를 공유하는 답답글들의 개수를 파악하는 쿼리와 자기 자신 이하 모든 답글들의 **depth** 를 1 씩 증가시키는 쿼리, 자신을 삽입하는 쿼리 총 세 개가 필요하다
- 답글의 답글이므로 **indent** 는 1 보다 크다

**boardDao.java**
```JAVA
...

public boolean insertReply (HttpServletRequest request) {

	boolean b = false;

	// 참조하는 부모 글의 시리얼과 그룹 시리얼, indent, depth
	int serial = -1;
	int gSerial = -1;
	int indent = -1;
	int depth = -1;
	String sql = "";

	try {
		serial = Integer.parseInt(request.getParameter("serial"));
		gSerial = Integer.parseInt(request.getParameter("gSerial"));
		indent = Integer.parseInt(request.getParameter("indent"));
		depth = Integer.parseInt(request.getParameter("indent")); // 참조하는 부모 글의 depth 필요
	} catch (Exception ex) { ex.printStackTrace(); }

	if (indent == 0) {
		...
	} else if (indent > 0) { // 참조하는 글이 답글이라면, 해당 답글을 부모로 하는 답답글들 중 최하단에 삽입. 그 이하 답글들의 depth 는 +1
		try {
			conn.setAutoCommit(false);
			// 1. 같은 부모 답글을 공유하는 모든 글 수를 카운트(cnt) 후 자기 자신의 depth 산출
			// 기존 답답글이 없다면 cnt = 0 일 수 있음
			sql = " select count(*) cnt from board where pSerial = ? ";
			ps = conn.prepareStatement(sql);
			ps.setInt(1, serial);
			rs = ps.executeQuery();

			if (rs.next()) {
				depth = depth + rs.getInt("cnt") + 1; // 자기 자신의 depth = 부모 답글의 depth + 답답글의 개수 + 1
			} else {
				depth ++; // cnt 결과값이 없다면?
			}

			rs.close();
			ps.close();

			// 2. 자기 자신 이하 모든 답글들의 depth + 1 (같은 그룹 내)
			sql = " update board set depth = depth + 1 "
				+ " where gSerial = ? and depth >= ? "; // 같은 그룹이면서 depth 가 자신과 같거나 큰 답답글들을 모두 밀어냄

			ps = conn.prepareStatement(sql);
			ps.setInt(1, gSerial);
			ps.setInt(2, depth);
			int result = ps.executeUpdate();

			if (result > 0) {
				System.out.println("자기 자신의 depth 와 같거나 큰 답답글들을 밀어냄");
			}

			ps.close();

			// 3. 새로운 답답글 등록
			sql = " insert into board (serial, id, title, content, "
				+ "	gSerial, pSerial, depth, indent, bDate)"
				+ " values (seq_board.nextval, 'hong', seq_board.currval || ' - hihi', seq_board.currval || ' - 안녕하세요~', "
				+ "	?, ?, ?, ?, sysdate) ";
			ps = conn.prepareStatement(sql);
			ps.setInt(1, gSerial);
			ps.setInt(2, serial);
			ps.setInt(3, depth); // 1. 의 결과값 삽입
			ps.setInt(4, indent + 1); // 답답글이므로 +1 (2 이상이 된다)
			int result = ps.executeUpdate();

			if (result > 0) {
				b = true;
				conn.commit();
			}
		} catch (Exception ex) {
			try {
				conn.rollback();
				ex.printStackTrace();
			} catch (Exception ex) { }
		} finally {
			try {
				closeSet();
			} catch (Exception ex) { ex.printStackTrace(); }
		}
	}

	return b;
}

...
```

- 테스트를 위해 3 번 글을 그룹으로 하는 답글 중 6 번 시리얼을 부모 답글로 삼는 답답글 두 개 (시리얼 9, 10) 를 추가하고 정렬한 결과는 다음과 같다

![reply02](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/hierarchical_reply02.png)

- 보는 바와 같이 3 번 글에 대한 1 차 답글인 7 번, 8 번 답글은 밀려나고 6 번 답글의 답답글 두 개가 사이에 추가되었다
- 여기서도 오래된 글 순으로 정렬되었고, 답답글이므로 **indent** 는 2 이다

### 글 삭제

- 최상위 글이던 답글이던 자신을 참조하는 답답글이 존재한다면 삭제되지 않도록 조치한다

boardDao.java
```JAVA
...

public boolean delete (int serial) {
	boolean b = false;
	// 삭제하고자 하는 글을 참조하는 답글이 존재하는지 조회
	String sql = " select count(*) cnt from board where pSerial = ? ";
	int cnt = 0;

	try {
		ps = conn.prepareStatement(sql);
		ps.setInt(1, serial);
		rs = ps.executeQuery();

		if (rs.next()) {
			cnt = rs.getInt("cnt");
		}

		rs.close();
		ps.close();

		// 조회 결과가 없을 시 삭제 구문 실행
		if (cnt == 0) {
			conn.setAutoCommit(false);
			sql = " delete from board where serial = ? "
			ps = conn.prepareStatement(sql);
			ps.setInt(1, serial);
			int result = ps.executeUpdate();

			if (result > 0) {
				b = true;
				conn.commit();
			}
		}
	} catch (Exception ex) {
		try {
			conn.rollback();
			ex.printStackTrace();
		} catch (Exception ex) { }
	} finally {
		try {
			closeSet();
		} catch (Exception ex) { ex.printStackTrace(); }
	}

	return b;
}

...
```

- 반환값이 **true** 면 삭제 완료, **false** 면 참조글이 존재하거나 에러이므로 확인해본다

#### 자신을 참조하는 모든 답답글들까지 연쇄삭제 하려면?

- 만약 참조 여부에 상관 없이 삭제하고자 한다면, 자기 자신을 참조하는 모든 답글들을 보존하거나 연쇄적으로 삭제할지 결정한 후 적절한 쿼리를 추가해야 한다
- 연쇄삭제를 결정했다면 삭제 시 참조 기준을 단순 부모 시리얼로 하면 안 된다.
- 계층적으로 참조되기 때문에 특정 답답글이 자신의 하위에 속해있으면서도 직속 부모 시리얼로 참조하지 않고 있을 수 있기 때문
- 그렇다고 그룹 시리얼로 정할 수도 없다. 같은 그룹이면서 자신과 같은 **indent** 이거나(자신을 참조하지 않는다는 의미) 자신보다 상위 답글들이 존재할 수 있으므로 안 된다
- 해결방안
	* **같은 그룹 내 모든 글들은 depth 순으로 정렬된다는 특성을 이용한다**
	* **조건** : 같은 그룹이면서, **depth** 가 자신보다 높은 동시에 자신의 다음 답글 (같은 부모를 참조한다는 것 외에 연관성이 없는 같은 **indent** 수준의 답글) 보다 낮은 답답글들
	* **쿼리** : 3 번 그룹의 6 번 답글을 삭제한다고 가정. 자신의 **depth** 는 1 이며 다음 답글 7 번과 자신의 답답글 두 개가 존재한다
	* 위 조건을 적용해 **delete** 쿼리를 작성하면 다음과 같다

**query_delete**
```SQL
delete from board
	where (gSerial = (select gSerial from board where serial = 6)) -- 같은 그룹 내
		and (
			depth between (select depth from board where serial = 6) -- depth 가 자기 자신의 depth 부터 ~
				and (
					(select min(depth) from board where gSerial = (select gSerial from board where serial = 6) -- 자신과 같은 그룹이면서
						and depth > (select depth from board where serial = 6) -- depth 는 자신보다 높고
						and indent = (select indent from board where serial = 6) -- indent 수준은 자신과 같은, 즉 같은 수준의 자신을 참조하지 않는 다음 답글들
					) - 1 -- 중의 최소값인 depth 에서 1 을 뺀 depth
				)
		); -- 그런 depth 까지 포함되는 모든 답답글들을 삭제한다
```

- Oracle Sql Developer 에서 위 쿼리를 실행하고 테이블을 정렬 조회하면, 원하는 답글과 그것의 답답글들이 연쇄적으로 삭제된 것을 볼 수 있다

```
3개 행 이(가) 삭제되었습니다.
```
![deleting reply content in cascade](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/hierarchical_delete_in_cascade.png)

### 그럼 댓글은?

- 댓글은 댓글을 위한 별도의 테이블을 만들고, (ex. **comment**)
- 각 글들이 게시판 (**BOARD**) 의 특정 글을 외래키로 참조하도록 하면 된다
- 나머지 구성은 답글의 논리적 순서와 완전히 동일하게 한다
- 다만, 게시글을 수정하거나 삭제할 때 제약조건이 더 많아질 것이므로 충분히 주의할 것

## 계층형 쿼리 사용하기

- 삭제하기 이전의 **board** 테이블을 **start with-connect by** 문법으로 동일하게 정렬하면 다음과 같다

**hierarchical_query01**
```SQL
select * from board
	start with pserial = 0
	connect by prior serial = pserial
	order siblings by gserial desc, depth asc;
```

- 정렬 결과는 동일하다

![계층형 쿼리 사용01](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/hierarchical_query01.png)

- 문법
	* **start with** : 계층화를 위한 루트 노드 지정. 서브쿼리 사용 가능하다
	* **connect by** : 각 컬럼 중 부모와 자식 관계를 규정. 서브쿼리 사용 불가
	* **order siblings by** : 결과 데이터를 정렬하되, 계층화 되었으므로 패밀리(형제 노드)끼리 분리해서 정렬
- 구문 해석
	* 루트 노드는 부모 노드가 없는 최상위 행으로 지정 (**pserial** = 0).
	* **pserial** (우변) 을 부모로 삼는 자식 노드 **serial** (좌변) 의 관계 지정
	* **gserial desc, depth asc** 순으로 정렬하되, **pserial** = 0 인 루트 노드로 형성된 각 계층별로 나눠서 함 (**siblings**)

### **lpad** 함수로 들여쓰기

- 계층별 그룹핑을 확실히 보기 위해 5 번 시리얼 글에 1차 답글 세 개와 2차 답답글 두 개를 추가한 후 들여쓰기의 결과를 확인한다

**hierarchical_query02**
```SQL
select serial, level, lpad(' ', (level-1), ' ') || id id, title, content, gserial, pserial, depth, indent, bdate from board
	start with pserial = 0
	connect by prior serial = pserial
	order siblings by gserial desc, depth asc;
```

- **connect by** 절을 사용하면 컬럼에 **level** 이 발생한다. 이는 각 행의 계층 수준을 의미한다
- **lpad** 로 정렬할 때는, 이 **level** 값에 따라서 공백 문자열이 먼저 채워지고, 그것에 덧붙여 아이디가 위치하도록 조치한다
- 예를 들어 최상위인 레벨 1 은 (1 - 1) = 0 칸의 공백이 채워진 후, 1차 답글 수준인 2 레벨은 (2 - 1) = 1 칸의 공백이 채워진 후 아이디가 위치하게 된다

![계층형 쿼리 사용02](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/hierarchical_query02.png)

- 5 번 최상위 글에 대한 1차 답글 세개, 2차 답글 두개, 3차 답글 두 개가 정렬되며 각 **level** 값에 따라서 들여쓰기된 것을 볼 수 있다
- 3 번 최상위 글에 대한 각 답글들도 **level** 에 따라 정렬 후 적절히 들여쓰기 되었다
- 사실상 **indent** 자체가 **level** 을 의미하므로, **indent** 순으로 들여쓰기 해도 상관 없다








