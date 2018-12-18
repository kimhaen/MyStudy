
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

1. BoardA


	![desc BoardA;](
        https://github.com/daesungRa/MyStudy/blob/master/imgs/BoardA.PNG
      )
      
      
2. BoardB


	![desc BoardB;](
        https://github.com/daesungRa/MyStudy/blob/master/imgs/BoardB.PNG
      )
      
      
3. BoardAtt


	![desc BoardAtt;](
        https://github.com/daesungRa/MyStudy/blob/master/imgs/BoardAtt.PNG
      )
      
      

## 코드 구조

1. DBConnection 클래스
