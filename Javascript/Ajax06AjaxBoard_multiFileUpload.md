
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190211-lightgray.svg?style=flat-square)

## 게시판 - 여러 개의 첨부파일 추가하기

- 기본적으로 각 기능을 담당하는 메인 페이지를 만들고, (ex. 회원정보, 게시판, 방명록 등)
- 세부 페이지들은 ajax 로 비동기 처리한다. 게시판의 경우 crud 페이지들을 ajax 로 처리

### 여러 개의 첨부파일 추가하기 - insert, modify 등 여러 페이지에서 활용 가능

- 하나의 게시글에 여러 개의 첨부파일이 존재할 수 있으므로, board 테이블 외에 board 를 참조하는 boardAtt 테이블을 별도로 만든다
- 페이지에서 넘어온 여러 개의 파일 정보는 적절한 dao 처리를 통해 board 및 boardAtt 테이블에 각각 저장되어야 한다. 이때 트랜잭션 단위로 커밋되도록 조치한다
- 그것보다 **핵심적인 것**은 뷰단에서 스크립팅을 통해 첨부할 여러 파일을 원활하게 등록, 수정, 삭제하는 것이다

### 파일 첨부 영역을 위한 스크립트

- 













