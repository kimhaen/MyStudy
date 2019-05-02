
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190502-lightgray.svg?style=flat-square)

- 참조: [hero0926](https://hero0926.tistory.com/29)

# MongoDB 정리

> MongoDB 는 대표적인 document oriented database 이다. RDBMS 의 스키마 구조가 없으며, 대용량 데이터의 지속적인 분산처리에 유리하다.

## MongoDB 와 Redis 비교를 중심으로 본 NoSQL 에 대한 이해

> Not Only SQL !!
- 기존의 RDBMS 와 달리 비관계형 **key-value 스토어**이다.
- 특징
	* schema (table-column 규격 등) 가 없다.
	* 고로 join 이 불가능!
	* 트랜잭션을 지원하지 않음 - 종류에 따라 다르지만, mongo 는 지원하려는 추세라고 한다.
		- 'may not provide full ACID ...'
	* 분산처리(수평적 확장)을 쉽게 제공한다. 이는 NoSQL 류의 일반적인 목적이다.
- 언제 쓰나?
	* 아주 많은 양의 데이터에 대한 효율적이고 빠른 분산처리가 요구될 때.
	* 데이터의 안정성 - 서버 장애 발생 시에도 데이터 유실이나 서비스 중지가 없는 구조 - 에 유리하다.
	* Redis 는 key-value 지향형, MongoDB 는 document 지향형 데이터베이스이다.

## MongoDB

- 도큐먼트 지향 디비로, bson (json 과 유사한 데이터 구조) 방식의 데이터를 저장한다.
- 문서를 기본 저장 단위로 이용하면서, 내장 문서와 배열을 이용해서 복잡한 계층구조를 하나의 레코드(열)로 표현할 수 있다.
- 스키마가 없으므로 큰 제약 없이 필드 추가/제거 및 변경이 자유롭다.
- 즉, 데이터 구조 설계를 위해 들이는 비용이 줄어든다.
- 고성능에 속도가 빠르며(굉장히 빠르다고..), Scale Out 이 가능하다.
- join 및 transaction 을 지원하지 않으며, 테이블 제약조건에 대한 처리도 없다. 속도저하를 불러오는 복잡한 과정이 사라진 것.

## 기본적인 쿼리 예제

```SQL
-- Databases 보기
show dbs

-- daesungra 라는 데이터베이스 선택하기
use daesungra

-- 선택한 db 의 collections 확인하기
show collections

-- "kys" collection 에 document 생성
db.kys.**insert**({name:"aaa", age:**29**})

-- 조건 없이 해당 collection 의 모든 document 검색
db.kys.**find**();

-- name 이 "aaa" 인 documents 를 검색
db.kys.**find**({name:"aaa"});
```

- 아래 주소는 RDBMS 의 쿼리를 MongoDB 쿼리로 확인 가능한 사이트이다. (sql to mongodb mapping chart)
	* [MongoDB-docs](http://docs.mongodb.org/manual/reference/sql-comparison/)




