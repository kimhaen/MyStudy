
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190326-lightgray.svg?style=flat-square)

# Oracle 과 MySql 비교

- 책상 프로젝트를 하면서 AWS RDS 를 사용 시 요금체계로 인해 Oracle 대신 MySql 을 적용하게 됨
- 그로 인해 기존의 쿼리문을 대거 수정할 수밖에 없었음

## 차이점 목록

### Pagination, 날짜 표현

- 기존 Oracle 문법
```SQL
SELECT * FROM (
	SELECT ROWNUM rno, s.* FROM (
		SELECT * FROM BOARD
			WHERE TO_CHAR(bDate, 'YYMMDD') < TO_CHAR(SYSDATE, 'YYMMDD')
			ORDER BY bDate DESC
	) s
) WHERE rno BETWEEN #{startNo} AND #{endNo};
```

- MySql 문법
```SQL
SELECT s.* FROM (
	SELECT * FROM DBOARD
		WHERE DATE_FORMAT(bDate, '%Y%m%d') < DATE_FORMAT(now(), '%Y%m%d')
		ORDER BY bDate DESC
	) s
	LIMIT #{startNo}, #{listSize};
```

- 여기서 먼저 주목할 점은 날짜 표현인데,
	* 오라클은 TO_CHAR 함수로 문자열 변환을 하고 'YYMMDD H24:mi:ss' 와 같은 방식으로 표현한다
	* MySql 은 DATE_FORMAT 함수로 변환, '%Y%m%d' 방식으로 표현한다
	* 현재 날짜는 SYSDATE / NOW(), CURRENT_TIMESTAMP 를 사용한다
- 다음은 페이징 처리인데,
	* 오라클은 정렬 후 ROWNUM 를 매기고 그것에 따라 페이지의 시작번호와 끝번호를 매긴다면
	* MySql 은 정렬 후 LIMIT 문법으로 바로 페이징 처리를 한다
	* 여기서 주의할 점은 startNo 인데, ROWNUM 은 1부터 차례대로 카운트하므로 startNo = 1 혹은 이전 글의 개수 + 1 인 반면,
	* LIMIT 에서는 인덱스 0에서부터 바로 카운트된다 (이 차이점 때문에 애먹음)

### SEQUENCE 사용

- Oracle 은 시퀀스를 사용한다. 별도의 시퀀스를 생성하고 NEXTVAL, CURRVAL 을 활용한다
- 그러나 MySql 은 시퀀스 사용 없이 애초에 테이블 생성 시 AUTO_INCREMENT 조건을 적용한다



