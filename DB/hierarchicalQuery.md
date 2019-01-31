
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190131-lightgray.svg?style=flat-square)

# 게시판 글과 답글, 댓글을 위한 기본적인 계층형 쿼리 정리

- 총 두 가지 방법이 있다.
- 게시판 자체를 계층형을 고려하여 구성하거나, start with-connect by 문법을 활용해 애초에 계층형으로 쿼리를 날리는 방식이다

## 계층형 게시판 설계

- 우선 편의상 A 형과 B 형으로 나누는데, 전자는 정교한 계층화 후자는 간결한 계층화를 위함이다
- 일반적으로 A 형을 쓴다고 보면 된다 (학습도 A 형으로 할 것)

### A 형 구성요소 (컬럼)

- serial (혹은 bNo)
- id (작성자)
- pwd
- subject
- content
- bDate
- hit (조회수)
- grp (그룹번호)
- deep (계층 수준)
- 여기서 B 형 게시판은 pSerial 을 활용해 부모자식 관계를 표현한다






