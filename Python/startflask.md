![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190507-lightgray.svg?style=flat-square)

# 플라스크 프로젝트 시작하기

> Python 3.7 / Flask / MongoDB / summernote editor 등등

- [flask 공식 도큐먼트](http://flask.pocoo.org/docs/1.0/)와 [mongodb 연동 예제](https://api.mongodb.com/python/current/)들을 참조한다
- 기본 베이스는 과장님이 만든 [flask 예제 프로젝트 - fork](https://github.com/daesungRa/flask)
- 포커스는 flask 프로젝트 구성 및 요청 처리과정, 원격지 mongodb 에 접근하여 데이터를 관리하는 방식을 알아가는 것에 있다.

## Flask 기본 개요

- "micro" ?? : 하나의 py 파일에 모든 웹앱 요소를 때려박는다거나 기능적으로 부족한 부분이 있다는 의미가 아니라, "**Flask aims to keep the core simple but extensible**" 임을 의미한다.
- 환경 설정 및 컨벤션에 대한 내용은 [이전 글](https://github.com/daesungRa/MyStudy/blob/master/Python/flaskconfig.md)을 참조한다.
- 그렇다고 꼭 컨벤션에 얽매일 필요는 없다. 본인의 코딩 스킬이 향상되었다면, 프로젝트를 자유롭게 디자인해볼 수도 있는 것이다.

## MongoDB 기본 개요

- 몽고디비는 기존의 RDBMS 의 비용 및 확장성 문제 보완을 위해 만들어진 대표적인 NoSQL 이다.
- 스키마 구조가 없기 때문에 초대량의 데이터를 매우 빠론 속도로 처리할 수 있고, 자유로운 확장 및 마이그레이션이 가능하다. 일반적으로 master-slave 구조로 3 개 이상의 데이터서버를 구축해 상호보완적으로 운용한다.
- 그러나 데이터 수정이 잦고 복잡한 쿼리가 필요하거나 안정적이며 일관성 있는 데이터 처리가 요구될 때는 불리한 측면이 있다. 그리고 빅데이터를 다루는 만큼 하드웨어적인 디스크 성능도 중요하고, 부분적인 데이터 유실 가능성이 존재한다.
- 기본 단위는 document, 즉 문서이며 테이블에 해당하는 개념은 collection 이다.
- 이 collection 내에 원하는 방식의 JSON(혹은 BSON) 데이터를 저장 및 조회한다.
- python 프로젝트에서 MongoDB 를 사용하기 위해서는 디비 서버로부터 클라이언트 커넥션을 얻어오기 위한 **PyMongo 모듈**이 필요하다.

## 프로젝트 설명



