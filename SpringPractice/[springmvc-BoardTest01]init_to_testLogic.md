
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190124-lightgray.svg?style=flat-square)

# Spring MVC 게시판 연습하기 - BoardTest01 (첫번째)

## 오늘까지의 흐름

- 최초 메이븐 프로젝트 생성에서,
- 환경설정 (pom.xml, web.xml, root-context.xml, servlet.xml, mapper 설정)
- controller, service, dao 까지 만들고,
- test1 메서드를 만들어 RequestMapping 경로대로 실행해봄 (역할은 더미 데이터를 DB board 테이블에 입력함)

## 실패 사항

- myBatis 를 위한 mapper 만들어 적용하는 것은 실패함 (아직 공부를 안해서..)
- Logger 찍기는 되기는 하지만, 공부를 좀더 해야겠음 (실패라기엔 뭐함)

## 성공 사항

- myBatis 대신에 c3p0 제품의 Template 에 DataSource 세팅함으로써 간결한 쿼리 로직을 만들어 쉽게 실행함
- 이클립스에서 git 퍼스펙티브로 연동해서 즉각적인 커밋 및 push 가 이루어지도록 함

## 내용

pom.xml







