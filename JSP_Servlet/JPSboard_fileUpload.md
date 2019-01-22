
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190122-lightgray.svg?style=flat-square)

## 게시판 파일 업로드

### servlets.com 의 cos.jar 라이브러리 사용 방법

1. 테스트를 위한 form 태그에 enctype 속성 추가
	- enctype='multipart/form-data'
	- 이 방식을 사용하면 request 객체로 직접 값을 받을 수 없음 (어떤 라이브러리든 동일)
	- MultipartRequest 객체를 생성해 그것에 request 객체를 투입함으로써만 가능함
2. 라이브러리 추가
	- [cos.jar] (유료) servlets.com 으로 들어가서 com.oreilly.servlet 항목에서 cos-26Dec2008.zip 를 다운받아
	- cos.jar 파일을 프로젝트 라이브러리에 추가한다 (ContextPath/WEB-INF/lib)
	- 이외 두 가지 라이브러리가 더 있으니 추후 연습해볼 것 (톰캣, servlet3)
3. MultipartRequest class
	- MultipartRequest 클래스는 request 및 필요한 매개변수를 투입받아 인스턴스 (객체) 가 생성되는 순간 파일 업로드가 진행됨 (백그라운드에서 자동으로)
	- new MultipartRequest 시 시스템 파일과 오리지널 파일로 업로드됨
	- 원본 파일명은 중복될 수 있으므로, 실제 시스템에 저장될 때는 숫자를 덧붙여서 시스템 파일명을 생성한다
	- 사용자에게 보여지는 것은 자신이 저장한 원본 파일명이지만, 시스템에는 다른 이름으로 저장된다 (그렇게 코딩하면 됨)
4. WebContent/img 폴더 생성 - 파일 업로드 폴더. 웹앱 내 이미지 파일을 위한 폴더를 생성한다. 이 경로는 매개변수로 투입됨

### MultipartRequest 활용하여 멤버관리 로직 만들기

* FileUpload.java
	- 위에서 설명한 특성들에 기초해서 FileUpload 모듈을 만든다 >> FileUpload.java
	- 매개변수 : 
	- FileUpload.java 는 request 객체를 받아 그것을 인자로 MultipartRequest 인스턴스를 생성, 지정된 경로로 업로드 (저장) 한다
	- 만약 업로드된 파일을 확인하고 싶다면, 업로드 파일이 여러 개일 수도 있으므로 Enumeration 으로 반복출력한다
	- 반환 객체는 게시판에서 회원정보로 재사용하기 위한 MemberVo 이며, 모듈 안에서 request 가 포함된 multipart 인스턴스로 vo 객체를 초기화 후 반환한다
* insult_result.jsp, modify_result.jsp 파일 수정 >> FileUpload.java 를 활용해 vo 객체를 얻어올 것
* modify.jsp 페이지에서 업로드된 이미지 보여주기
* 회원 삭제 시 업로드 파일 삭제
* 회원 프로필 사진 변경 시 기존 파일 삭제
	- 내용은 무조건 바꿨다고 보고, 이미지 file 만 수정되거나 아니거나 두 가지 경우만 생각하기로 함
	- 막 새로운 hidden 태그를 추가하고 file 태그를 수정하고 그렇게 하기보다는
	- 그냥 파일 수정 시에 맞는 sql 과 수정하지 않았을 시의 sql 을 다르게 작성해서 ps 를 실행하자
* 검색어와 현재 페이지 정보를 수정, 삭제 처리 후 목록으로 되돌아올 때 기존 정보를 유지시키기
* 우편번호 검색










































