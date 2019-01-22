
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190122-lightgray.svg?style=flat-square)

## JSP 게시판 만들기 - dao 로 CRUD 모듈화 하기, 페이징 처리

### 모듈화



### 페이징 처리하기 (member 목록에 대해서)

#### 강사님 방식

* pageCompute() 메서드를 활용해 각 변수에 해당하는 정보를 자동 계산함
* 변수 (페이지 개념 + 블럭 개념)
	- listSize : 각 페이지 당 출력 개수
	- blockSize : 하단에 나타나는 페이지의 크기
	- startPage : blockSize 에 따른 첫 페이지 번호
	- endPage : blockSize 에 따른 마지막 페이지 번호
	- startNo : listSize 에 따른 각 페이지 첫 멤버 번호
	- endNo : listSize 에 따른 각 페이지 마지막 멤버 번호

	- totSize: 전체 멤버 사이즈
	- totPage : 전체 페이지
	- totBlock : 전체 블럭
	- nowPage : 현재 페이지, 각 페이지번호를 클릭하면 그 번호가 현재 페이지가 됨
	- nowBlock : 현재 블럭
* 구하는 방식
	- totSize : select count(*) from member where [condition];
	- totPage : (int) Math.ceil(totSize / (double)listSize), 나머지가 존재한다면 올림수가 되도록 ceil 사용함
	- totBlock : (int) Math.ceil(totPage / (double)blockSize), 나머지가 존재한다면 올림수가 되도록 ceil 사용함
	- nowPage : (int) Math.ceil(nowPage / (double)blockSize)
	- nowBlock : 

	- endNo : nowPage * listSize, 이 값이 전체 건수보다 많아진다면 totSize 로 보정해야 함
	- startNo : endNo - listSize + 1

	- endPage : nowBlock * blockSize, 이것도 전체 블럭 사이즈보다 커진다면 totBlock 으로 보정해야 함
	- startPage : endPage - blockSize + 1
* 페이지와 블럭의 사이즈에 따라 페이지 버튼의 구조가 달라짐

































