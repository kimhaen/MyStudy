
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-181218-lightgray.svg?style=flat-square)

# 시퀀스 알아보기

- 시퀀스는 독자적인 테이블 형태로 존재하며, 일반적으로 다른 테이블의 Primary Key 값을 자동으로 생성하기 위해 사용된다
- 그러므로 시퀀스가 적용된 특정 테이블에 새로운 행을 추가할 때는, PK 값에 시퀀스가 적용되도록 쿼리를 짜줘야 한다

## 시퀀스 문법

**시퀀스 생성**

```SQL
CREATE SEQUENCE [SEQ_NAME]
	START WITH [시작값]
	INCREMENT BY [증가값]
	MAXVALUE [최대값] | NOMAXVALUE
	MINVALUE [최소값] | NOMINVALUE
	CYCLE | NOCYCLE
	CACHE | NOCACHE;
```

**시퀀스 수정**
- 시작값은 수정 불가

```SQL
ALTER SEQUENCE [SEQ_NAME]
	INCREMENT BY [증가값]
	MAXVALUE [최대값] | NOMAXVALUE
	MINVALUE [최소값] | NOMINVALUE
	CYCLE | NOCYCLE
	CACHE | NOCACHE;
```

**시퀀스 삭제**

```SQL
DROP SEQUENCE [SEQ_NAME];
```





