
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190131-lightgray.svg?style=flat-square)

# 게시판 글과 답글, 댓글을 위한 기본적인 계층형 쿼리 정리

- 총 두 가지 방법이 있다.
- 게시판 자체를 계층형을 고려하여 구성하거나[1](#계층형-게시판-만들기), start with-connect by 문법을 활용해 애초에 계층형으로 쿼리를 날리는 방식[2](#계층형-쿼리-사용하기)이다
- 후자의 방법은 바로 윗 부모의 시리얼을 알고 적절하게 쿼리를 날리면 오라클이 알아서 정렬해 준다
- 부모가 같은 게시글 집합에서는 그냥 시리얼 넘버로 정렬(asc or desc)하면 된다
- 이하 예제에서 사용할 테이블명은 **BOARD**, 시퀀스명은 **SEQ_BOARD** 이다

## 계층형 게시판 만들기

### 게시판 테이블 필수 구성요소

- 계층형 정렬을 위해서는 다음의 컬럼들이 필요하다
	* serial (자신의 시리얼)
	* gSerial (그룹 시리얼)
	* pSerial (바로 윗 부모 시리얼)
	* depth (같은 그룹 내 정렬을 위한 번호)
	* indent (답글 수준 또는 들여쓰기를 위한 깊이)
- 필수컬럼 외 id, pwd, title, content 등의 요소들은 상황에 따라 추가하면 됨

### 메인 글 등록하기

- 메인 글이므로 gSerial 은 serial 과 같고,
- 나머지 pSerial, depth, indent 는 모두 0 이다

```SQL
insert into board (serial, id, title, content,
		gSerial, pSerial, depth, indent, bDate)
	values (seq_board.nextval, 'hong', seq_board.currval || ' - hihi', seq_board.currval || ' - 안녕하세요~',
		seq_board.currval, 0, 0, 0, sysdate);
```

- 이 쿼리대로 우선 5 개 글을 등록한다

![mainContent](https://github.com/daesungRa/MyStudy/blob/master/imgs/db/hierarchical_fiveMainContent.PNG)

### 답글 등록하기

### 답글의 답글 등록하기

### 글 삭제

## 계층형 쿼리 사용하기






