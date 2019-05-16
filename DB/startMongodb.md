
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190516-lightgray.svg?style=flat-square)

- 참조: [zerocho 의 mongodb 정리](https://www.zerocho.com/category/MongoDB/post/579495d0f5586c2c12d30a7a)

# MongoDB 설치 및 구동하기

> 준비물 : MongoDB 설치파일, database 데이터 저장 파일, 적절한 메모리 용량

1. 몽고디비 홈페이지에서 Products - MongoDB Server MSI 파일을 다운받는다. (나에게 맞는 OS 기반)
2. 원하는 경로를 지정하여 설치 - 이곳의 bin 폴더 밑의 디비서버 구동 및 실행 파일을 활용한다.
3. bin 폴더의 mongod.exe 명령으로 서버 구동
    - ```--dbpath``` 옵션으로 다룰 데이터들이 저장될 폴더 경로를 추가 지정한다.
    - 명령어 : ``` > mongod --dbpath [데이터 저장 폴더] ```
4. 구동중인 서버에 접속
    - 같은 bin 폴더 내 mongo.exe 명령을 실행하면 된다.
    - ``` > mongo ```
    - 접속이 되었다면 이제부터 **MongoDB** 를 사용하면 된다!!
5. 기타
    - 몽고디비 서버 접속포트는 기본 **27017** 번 이다.
    - 최초 접속 시 실행해 볼 명령어
        * \> show databases / show dbs
        * \> use [database]
        * \> show collections
        * \> db.[collection].find() 등등
    - **계정 및 보안**
        * 몽고디비는 최초 기본 계정 인증 설정이 되어 있지 않다. 오라클에 비하면 외부공격에 매우 취약할 수 있다.
        * 계정 인증 시스템을 구축하고 활용하려면 별도의 작업이 필요하다.
            - 관리 데이터베이스인 admin 의 관리자 계정을 생성하고 (꼭 만들어줘야 한다) 각 사용자 데이터베이스에 대한 유저들을 생성하는 방식이다.
            - \> use admin
            - \> db.createUser({ user: [username], pwd: [password], roles: [ ... ] })
            - 같은 방식으로 사용자 데이터베이스에 대한 user 를 생성한다.
            - 접속창을 모두 끄고 config 파일에서 auth 설정을 해준 후 다시 접속하면 된다.
            - (마지막 라인에 추가) **auth = true**
            - 그럼 이제 접속할 때 **user, pwd, 사용할 db 이름** 을 포함해서 접속하면 된다.


