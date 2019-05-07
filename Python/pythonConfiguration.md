![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190423-lightgray.svg?style=flat-square)

# Python 사용하기

- 파이썬은 매우 심플한 문법으로 누구나 쉽게 따라할 수 있다.
- 그만큼 빠른 개발 및 유지보수가 가능하다.
- 파이썬은 인터프리터 언어이다.
- 데이터 분석(빅데이터), 머신러닝이나 ai 분야에서 많이 사용된다.
- 설치부터 학습, 프로젝트 만드는 데까지 심플하기 때문에 기존 사용언어가 있다면 굳이 오래 붙잡고 있을 필요가 없다.
- 어느정도 학습한 이후에는 반복 사용으로 숙달되기만 하면 됨

## 설치 및 환경설정

- 공식 사이트에서 os 및 bit 수에 맞는 버전을 다운받아 설치 > 환경변수에 추가하여 py 명령을 어디서든 사용할 수 있게 한다.
- 현재 3.x 버전은 패키지 내에 기본 개발 툴인 idle 과 패키지 관리자인 pip 모듈을 포함하고 있으므로 바로 사용하면 됨.
- pip 모듈을 사용하려면, python 설치 디렉토리 하위의 Scripts 디렉토리를 환경변수에 등록하면 편하다.

## virtualenv 구성하기

- 파이썬 커뮤니티에서는 수많은 api 의 다양한 버전이 존재하고, 그것들 간의 원활한 의존성 관리가 중요한 이슈이다.
- 나만의 프로젝트를 만들 때나 협업할 때 api 모듈의 버전호환성 및 의존성 이슈를 해결하기 위해 venv 를 사용한다.
- virtualenv 는 쉽게 말해 특정 프로젝트를 위한 독립적인 패키지 환경을 구축하는 것이다.
- 여기에서는 파이썬 버전에서부터 참조하는 각 모듈의 버전을 원하는 대로 설정할 수 있으며, 각 venv 나 원본 python 설치경로에 대해서는 항상 독립적이다

```bash
~$ virtualenv [~경로/venv 이름]
~$ cd [~경로/venv]
~$ ./Scripts/activate.bat

...

~$ [venv] pip install flask

...

~$ [venv] pip freeze > requirements.txt
```

- 이 명령으로 원하는 경로에 새로운 가상 환경을 세팅할 수 있다.
- 새로 만든 가상환경 경로로 이동하여 ./Scripts/activate.bat 파일을 실행하면 가상환경이 구동되고,
- 해당 환경 내부에 설치된 pip 모듈을 이용해 필요한 외부 모듈들을 적절한 버전과 의존성을 고려하여 설치한다 (install)
- 일련의 모듈 설치 명령을 고정시키기 위해 pip freeze 명령으로 requirments.txt 파일을 생성한다.
- 추후 pip install requirements.txt 명령을 실행하면 모든 필요모듈들이 자동으로 설치된다.


## pycharm 과 연동

- 파이참은 젯브레인 사에서 개발한 인텔리제이와 비슷한 구성의 통합 개발 툴이다.
- 공식 사이트에서 내가 사용하는 python 버전에 적합한 커뮤니티 버전을 다운 및 설치한다(프로페셔널 버전은 유료).
- 기본설정 후, 새로운 프로젝트를 생성하거나 기존의 프로젝트를 로드한다(open)
- 특정 virtualenv 에 종속된 프로젝트라면, 인터프리터 환경설정에서 해당 venv 의 파이썬 exe 파일을 등록하면 된다.




