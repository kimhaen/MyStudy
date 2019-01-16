
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190114-lightgray.svg?style=flat-square)

참조: 최범균의 JSP 2.3 웹 프로그래밍

## JSP 웹앱 개발

### 경로 문제

- 웹 프로젝트를 진행하다보면 경로를 잘못 지정해 404 에서가 나는 경우가 많다.
- 경로설정에는 절대경로, 상대경로 방식이 있으므로 충분히 이해할 것
- 개발시엔 전체 기준이 되는 메인 페이지를 지정하고 그것으로부터 각 페이지의 경로를 고려하여 개발한다
- 위에 톰캣 컨테이너와 프로젝트 의 웹 프로젝트 세팅 설정으로 Context Root 경로를 고정하고 그것을 기준으로 절대경로 개발해도 된다

### 버퍼 활용

- page 디렉티브로 설정한다
- 일반적으로 8k 사이즈가 사용되며
- 출력 내용이 이 사이즈를 넘어갈 시 autoFlush = true 옵션을 사용한다

### 개발한 웹앱 배포하기

- 현재는 eclipse 에서 하고 있지만, 개발 소스를 배포했을 때는 어떤 환경에서 그것이 활용될지 알 수 없다
- 게다가 eclipse-tomcat container 환경은 제대로 된 웹서버 환경이라고 볼 수 없다
- 로컬에서 개발을 마치거나 유지보수가 이루어졌을 때 그 결과를 

#### 배포 과정 (외부 톰캣 활용-웹서버)

- 톰캣 installer 로 설치 후 ID, PWD 지정 (계정 찾기는 conf 폴더 내 tomcat-users.xml 파일을 확인한다)
- eclipse 톰캣 컨테이너 중지 (동일한 포트번호를 사용한다고 가정)
- configure tomcat 실행 후 service status 를 start 로 설정
- 톰캣 컨테이너가 구동되면, Tomcat Manager 실행 후 로그인 한다 >> 외부 톰캣 서버 구동(웹서버 구동)
- 서버 컨테이너 메인 화면을 보면, 현재 컨테이너에 적재된 웹 프로젝트들이 보인다 (여기서 구동, 중지, 제거 등)
- 여기서 war 파일을 Deploy 하는 것
	- export 로 war 파일 생성
	- 이클립스 해당 프로젝트에서 export-war export 클릭
	- war 를 생성하고자 하는 경로를 지정하고 finish 클릭 (소스 포함할건지, 오버라이트 할건지 옵션 선택)
	- 이제 톰캣 매니저 페이지의 Deploy 항목에 생성한 war 파일을 추가하고 구동시키면 된다
- 여기까지가 이클립스-톰캣 에 관계없이 외부 서버에서 웹앱을 구동시키는 절차이다























