
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190504-lightgray.svg?style=flat-square)

# 공부 스케줄

## 190525 (sat) - flask-board 보수

- url 매핑 수정
- 브라우저 요청 방식 수정
- 비동기 처리로 전환 (게시글 리스트 페이징처리 같은거)

## 190524 (fri) - flask-board : 매크로+모달, 페이지네이션, 회원관리 적용하기 2

- macro + modal 적용함
    * target_id, title, content, btn_first(선택), btn_second(디폴트 close) 변수 적용
    * 모달은 bootstrap modal 을 customizing 함
    * 투입한 target_id 에 해당하는 모달을 그것과 연계된 버튼을 통해 활성화한다.
    * 현재 모달 구현 템플릿은 signin, signup, delete_account 세 개이다.
- 게시판에 대해서 Pagination 을 적용함
    * 이전에 만들었던 것처럼 Pagination 클래스 기반의 독립적인 인스턴스를 만들어 활용함
        * nowpage, pagesize, blocksize
        * totalcount, maxpage, maxblock, nowblock
        * endpage, startpage
    * mongodb 특성을 활용해 nowpage, collection_name 을 투입해 필요한 데이터들을 계산함
    * mongodb 에서는 skip(), limit() 함수를 통해 보다 쉬운 명령으로 원하는 결과를 얻을 수 있음
        * skip 은 전달받은 수 만큼 건너뛰고 조회, limit 은 한 번에 조회되는 도큐먼트의 갯수를 제한한다.
        * find 함수에 key-value 쌍의 인자를 넣으면 그것에 맞게 필터링된다.
        * sort 함수에 정렬하고자 하는 field 명을 넣고 1 (오름차순), -1 (내림차순) 정렬 가능하다
        * pretty 함수는 조회결과를 json 형식으로 보기좋게 출력한다.
    * 그렇게 조회된 결과와 pagination 변수들을 활용해 프론트 화면에 잘 그린다.
- 보수 사항 추가
    * 브라우저 요청 url 에서 불필요한 get 요청을 안정적인 post 방식으로 바꾸기
    * 페이지 이동이나 부분적인 요청 시 서버 부하를 줄이기 위한 방법을 고민해볼 것 (비동기 처리, ajax)
- 더 공부할 부분
    * mongodb cursor
    * crud 및 원활한 쿼리를 위한 세부문법 학습
    * pymongo 에서의 유용한 함수 살펴보기
    * 다른 사람의 잘 만들어진 프로젝트 구조, 모듈 등을 보고 배우기
- account authentication : 책이나 레퍼런스 보고 따라서 만들어보기 (주말간 해보기)

## 190523 (thur) - flask-board 회원관리 적용하기

- flask 에서 사용되는 session 과 cookie 에 대해 학습한 후
- 예제를 보고 프로젝트에 어떻게 적용할지 구상하고 적용해보기
    * flask request 객체 개념정리 : request object, cookies, sessions
    * 회원정보로 디비에 저장될 required field 구상
        - username, pwd, nickname, register_date 정도..?
- 보수 사항
    * 댓글 구현
    * 관리자 구현
    * html 코드 파싱
    * url id 값 제거
    * 메인 화면 구성
    * 파일 업로드
    * Jinja2 매크로 적용

## 190522 (wed) - flask-board:crud 완료

1. board 와 유사한 todo 로직 추가/병합
    - 본래 board 와 todo 페이지를 따로 만들고자 했으나,
    - 특정 스크립트 부분만 빼면 거의 모든 코드가 동일하기 때문에
    - board 관련 페이지만으로 todo 기화면까지 구현하도록 함
    - 이를 위해 각 요청에 대한 플래그를 서버로부터 받아서
    - 그 이후 흐름은 해당 로직에 맞게 처리되도록 함
2. 보수할 사항
    - url 에 get 방식으로 나타나는 id 값 제거하기
    - db 에 저장된 html 코드 string 을 view 페이지에서 보여줄 때 코드 그대로 나타나는 현상 수정하기
    - app.py 에서 공통 코드 합치기
        * board 와 todo 라우팅에 있어서 uri 만 다르고 함수 로직은 동일하기 때문에 코드 간략화의 가능성이 있다
        * service 모듈의 인스턴스명도 board 에서 service 로 더 추상화한다.
    - 회원관리를 위한 세션 및 쿠키 공부 후 적용하기
    - 댓글 기능 구현하기
    - 회원관리, 관리자 기능 및 페이지 추가하기
    - 메인 화면에 타임라인 추가, 게시글 및 todo 리스트 요약해서 보여주기
3. 기타
    - jinja2 매크로 만들어 적용하기 (중복 코드 제거)

## 190521 (tue) - flask-board

- 어제까지 write 액션을 위한 모듈 로직 수정을 진행함
- 각 uri 및 app 모듈의 매핑 함수명들을 restful(?) 에 최대한 맞춰보며 네이밍 함
- 오늘할거
    * crud 완성하기
    * 회원관리를 위한 세션 및 쿠키 공부 후 적용

## 190520 (mon) - (저녁) flask-board crud 만들기

- 근무시간동안 퍼블리싱 얼추 했으니,
- 일단 기본적인 crud 부터 만들어보자.
- 다 못하면 내일 이어서 하기!
- 회원관리나 추가적인 부분은 그 다음에 하기
- collection fields
    * _id, title, content, author, created, updated
    * _id 는 자동등록, 작성자, 등록 및 수정일도 자동등록
    * 제목, 글 내용만 쓰면됨

## 190513 (mon) - pipenv 정리, 아무것도 안보고 flask/mongodb 게시판 만들기

## 190507 (tue) 부터 뭘해보지?

- flask 로 api 서버 만들어 서비스 해보기
- 구글로 검색되는 다른 사람들 프로젝트 따라해보고,
- 내 아이디어로 스스로 만들어보기!

## 190502 (thur) - python, flask, mongodb, git/github, aws

- 5월 3일 금요일부터 주말간 파이썬 책 예제 프로젝트 끝내기
- **포토로그** : flask, 몽고디비, aws elastic beanstalk 로 프로젝트 끝내보기!
- 안되면 ec2 인스턴스로 해보기

