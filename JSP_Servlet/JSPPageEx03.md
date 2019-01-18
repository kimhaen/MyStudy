
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190117-lightgray.svg?style=flat-square)

## JSP 게시판 - modify, modify_result, view 는 modify 화면으로 대치

### 할거

- modify 화면 : 삭제버튼 추가 (btnDelete)
- modify 결과 화면, delete 화면 작성

### 지금까지 정리

- 프로젝트명 : [MyWebProject]
- 메인 화면 : index.jsp
- 로그인, 로그아웃 화면 : login.jsp, logout.jsp
- (메뉴) JSP 항목 : index_jsp.jsp
	* 메인 화면이자 개발 경로설정의 기준
- (메뉴) control 항목 : control.jsp
	* 멤버에 대한 CRUD 로직 호출
- 멤버 입력 화면 : insert.jsp, insert_result.jsp, idCheck.jsp
	* 아이디 중복체크 (idCheck.jsp) 및 각 항목 무결성 체크 이후
	* insert_result.jsp 화면에서 DB 에 등록함
	* 기본 실패메시지, 성공여부에 따라 메시지 수정 후 화면에 표현
- 멤버 조회 화면 : list.jsp
	* 멤버 검색 기능 (post 방식으로 페이지 재요청)
	* 전체 혹은 검색된 멤버의 간략 list 표현
	* id 를 클릭하면 post 방식으로 view 페이지 (여기선 modify.jsp 로 통합) 가 나타남
- 멤버 수정, 삭제 화면 : modify.jsp, modify_result.jsp
	* 여기서 선택된 멤버의 정보를 view 하고, 수정도 가능하게 함 (취소를 클릭하면 이전 페이지로 돌아감)
	* 동일한 무결성 체크를 수행하고, id 및 최초등록일과 비밀번호 수정은 불가함
	* 삭제 로직도 이 페이지에 포함. delete.jsp 페이지로 넘어가서 비밀번호 검증 후 맞으면 삭제 틀리면 실패
- 시계 컴포넌트 : dateObj.jsp (어디든 적재 가능)
- CSS : index_large.css, index_mini.css, member.css
	* 인덱스 화면, 멤버 관련 화면의 css 처리
- Javascript : member.js
	* member 와 관련된 페이지에 대한 스크립트 처리 (insert, view, update, delete)
- 모든 경로의 기준은 index.jsp 화면임. 요청에 따라 각 컴포넌트 및 화면들이 index 에 적재되는 방식

### 개선점
	* 비밀번호 변경 로직 추가 필요
	* 새로운 멤버 등록의 DB 커넥션 로직은 아직 만들지 않음(수정 로직을 참고해서 만들 것)
	* 비밀번호 검증 후 삭제 로직은 만들었는데, 비밀번호 검증 후 수정 로직은 아직 만들지 않음 (고로 그냥 수정됨)
### 프로젝트 링크

[MyWebProject]
[MyWebProject]:(https://github.com/daesungRa/MyWebProject)






























