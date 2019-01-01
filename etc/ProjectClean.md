
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190101-lightgray.svg?style=flat-square)

# Project Clean in eclipse

> 코드상에 문제가 없는데도(없어 보이는데도) 빌드상의 에러가 발생하는 경우가 있다. 이 때는 각 파일이나 프로젝트에서 validate 기능을 사용하여 에러검증을 할 수도 있으나, 그것마저 여의치 않으면 clean 기능을 사용한다. 드러나지 않은 충돌 및 의존성 문제가 발생하면 가끔 이런 일이 생긴다.

- 컴파일 상의 문제가 발생한다면, (특히 ClassNotFoundException)
- Project > Clean... 을 클릭하면 Project Explorer 상의 모든 프로젝트 혹은 지정한 프로젝트에 대해서 재빌드를 시도한다
- 이는 기존에 컴파일되면서 생성된 class 파일 및 모든 구동정보를 삭제하고 새롭게 빌드하는 것을 의미한다
- (정말 코드상의 문제가 없었다면) 올바르게 컴파일 후 재빌드되어 결과가 도출됨을 확인할 수 있다



