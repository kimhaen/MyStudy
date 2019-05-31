
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190504-lightgray.svg?style=flat-square)

# 스케줄

## 190531 (fri) - 회식의 날.. / flask-board

- signup, signin 을 별도의 페이지로 바꿈 (기존에는 모달이었음)
- mariadb 사용 >> 보류..
- 별도의 member_service.py 모듈 사용
- 모달 쓰지 말고 별도의 페이지로 구현하자
	* 사용자 UX 에만 지장이 없다면 굳이 모달로 하지 않아도 됨
	* 이전 페이지 redirect 를 위해
	* 잘못된 폼에 대한 정보를 보여주기 위해
	* 라우팅 시 불필요한 값을 넘겨주지 않아도 됨

#### todo list

- **권한 관리**
    * field명: 타입 == 'auth': int
    * 일반사용자(1), 관리자(2), 
- **계정 보안**
    * 비밀번호 해싱해서 저장하는 방법은 뭘까?
    * 혹은 security 모듈이 있을까?
    * 추가적으로 salt 값도 포함할 수 있을까?
    * auth 붙이기
        - 직접 붙이기
        - **외부 auth api 연동하기**
        - **auth 를 위한 API 서버 만들기**
    * **sign in** 유지하기
        - 세션으로 브라우저 실행 혹은 session time out 시간 동안 유지되도록 할 수 있음
        - 그러나 브라우저 껐다 켜거나 session time out 과 관계 없이 접속 상태가 유지될 수 있게 하려면?
        - 로그인 성공 시, 별도의 access token 을 클라이언트에 내려주면 가능함
        - 해당 access token 의 제한 사항은 따로 지정할 것 
- **이전 페이지 정보 기억했다가 되돌려주기**
    * **flask.request.referrer()** 모듈을 사용하면 됨!
- 기타
    * 댓글
    * 관리자
    * 좋아요, 팔로우
    * 메인화면 구성 (공지, 타임라인, 게시글 요약 리스트)
- flask 강의 따라하기

## 190530 (thur) - flask-board / 사용자용 mariadb 활용하기-계정등록 로직

- signup form 전송정보를 db 까지 저장해보기
	* member_service.py 를 통해 각 모듈 활용하여 구성한다
	* collection 이름은 members
	* field 목록 : username, pwd, email, nickname, profile, auth, created
	* 관리자용 계정 하나 만들고,
	* 나머지 일반유저는 브라우저를 통해 등록하기
	* auth : 1(일반), 2(관리자), 3(수퍼관리자)
- 프로필 페이지 만들기 (페이지로? 모달로?)
- 저녁에 할일
    * flask tutorial 강의 따라하기(1,2,3,4 강 까지)

## 190528 (tue) - flask-board, flask-wtf 적용, ajax pagination

#### flask-wtf??

- flask-wtf 는 안전하고 효율적인 html form 양식을 제공한다. 보안의 측면에서 csrf attack 으로부터 safe 하며, 사용자가 추가적으로 작업할 부분은 없다(on/off 가능)
- 원하는 form 양식에 대한 customizing 이 가능하며, 매우 쉽게 재사용할 수 있다.
- 설정한 대로 사용자 입력 데이터의 무결성 및 제약조건을 자동으로 체크하여 블럭한다.
- 잘못 입력한 정보에 대한 적절한 메시지도 반환 가능하다.
- 사용 방식은 서버 단에서 form 양식을 만들어 템플릿에 반환한다.

#### 내 프로젝트에 적용하기

- 그냥 서버로부터 customizing 된 form 을 받아와서 바로 적용하면 쉽지만,
- 나는 singin, signup 페이지를 modal+macro 로 사용하고 있기 때문에
- 이 두 페이지에 대해서는 아예 해당 form 을 base.html 페이지 내에 모달로 그려놓고 시작하려고 한다.
	* 기존의 signin.html 과 signup.html 을 사용하지 않고 base.html 에서 모달로 그려놓고 시작한다는 의미.
	* 그러려면 거의 모든 뷰 함수에서 개별적으로 form 을 반환해줘야 하는 코드반복의 문제가 발생한다!!

- 이 코드반복의 문제를 해결하기 위해,
	1. decorator 사용하기
	2. global g 객체 사용하기 (feat. Application Context)
	3. app.before_request 사용하기

- 1 번은 성공, 2 번도 성공했지만 모든 request 에 구분 없이 적용된다는 점이 존재함
- 3 번은 실패
	* 일단 application context 는 하나의 스레드(사용자 세션을 의미하는 듯?)에 한 번의 request 에만 적용되는듯 하다
	* 같은 세션이어도 서로 다른 요청에서는 새롭게 초기화된다.
	* 다만, application context 의 global g 객체는 한 번의 request 동안에 전역적으로 사용 가능하다.

- **정리**
    * 모든 템플릿이 base.html 을 기반으로 생성되며, account 관련 wtform 은 이곳에 렌더링 되니,
    * 최대한 코드를 줄이기 위해 **before-request** 를 사용하는 것이 적절하다.
    * 만약 모든 페이지에 적용되지 않고 선택적인 요소라면, **decorator** 를 사용하는 것이 더 나을 것이다.
    * 또한, 만약 가능하다면 전역 객체로 한 번 선언해 두고 필요에 따라 호출해서 사용하는 게 가장 좋지 않을까 싶다.

- 추가할 사항
    * 사용자 입력정보 체크 후 서버로 넘기기
    * 잘못된 입력이라면 메시지 표현하기
    * 디비까지 저장
    * 저장 결과 메시지 표현하기

#### 페이지 ajax 처리

- 불필요한 ajax 처리는 안하느니만 못하다는 전제.
- 부분적으로 갱신되는 정보에 대해서는 페이지 전체를 리로드하지 않아도 되게 하는 이점이 있음.
- 화면 내 용적이 크거나 메인 컨텐츠의 경우에는 그냥 페이지 이동시키는 것이 낫다.

- 어쨌든, flask 에서 ajax 테스트를 위해 게시판 페이징 처리를 ajax 로 구현했다
	* 처음 로드는 get 방식으로 1 페이지 전체가 그려지도록 함
	* 이후 페이지 이동은 ajax-post 방식으로 부분적 비동기 처리
	* 캐시에 페이지 이동기록이 남지 않기 때문에 페이지 리로드 시에는 최초 1 페이지로 돌아감

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

