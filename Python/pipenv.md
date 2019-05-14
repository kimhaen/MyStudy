
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190508-lightgray.svg?style=flat-square)

# 가상환경과 virtualenv, pip, pipenv

> [velog - pipenv 포스트](https://velog.io/@doondoony/pipenv-101)에 따르면, 업데이트가 느리고 사용자가 모르는 블랙박스 동작이 많아 pipenv 사용을 만류한다..
그래도 어쨌든 pip 와 virtualenv 의 편리한 결합판이라는 점에서 사용을 해보자 ㅎㅎ

## 가상환경의 필요성

- 계속 설명했듯이 지속적으로 발전하는 파이썬 커뮤니티에서 가상환경의 개념은 매우 중요하다.
- 파이썬 및 패키지 버전에 따른 파이썬 문법, 호환성 및 의존성이 다르거나 복잡할 수 있기 때문이다.
- 특히 협업이나 최종적인 배포를 위해서라면 일관된 가상환경을 구축하는 것은 필수적인 사항이다.
- 가상환경을 구축하면, 우선 글로벌로 설치된 파이썬 관련 구성요소(런타임, 패키지 매니징 툴, 라이브러리 저장소)들이 격리된 공간에 만들어진다(복제).
- 이후 만들고자 하는 파이썬 프로젝트에 따라 해당 가상환경의 구체적인 내용들을 구축해 나가면 된다.
- 이러한 환경 및 내용의 구체적인 구축 과정에서 필요한 것이 패키지 매니징 툴인 pip, pip3, pipenv 등이다.

## pipenv 설치하기

- pipenv 는 virtualenv 와 pip 가 결합된 만큼 전역 위치에 설치한다.
```text
1. python 버전과 pip 버전 체크
    > python 3 라면 pip3 사용, 최신 버전으로 업그레이드
2. 전역에 pipenv 설치
    > pip3 install pipenv
3. 올바르게 설치되었는지 확인
    > pip3 freeze
```
- 여기서 주의할 점은 **sudo** 권한으로 설치하면 안된다는 것이다. 런타임이나 중간 패키지 sudo 권한이 없다면 완전 꼬여버릴 수 있다.

## pipenv 사용하기

- 기본 명령어
    * open, run, shell / check, clean, graph, install, lock, sync, uninstall, update
- **순서** : venv 생성 - venv 진입 및 확인 - 패키지 설치 - py 스크립트 실행 - Pipfile, Pipfile.lock - venv 제거
- 생성, 진입
    * 현재 위치를 기준으로 venv 를 생성하기 때문에, 원하는 디렉토리를 만들고 그 위치로 이동해야 한다.
    * ```pipenv shell``` (가상환경 진입 명령어. 가상환경이 없다면 생성)
    * 순수하게 생성만 하는 명령은 ```pipenv --python 3.7``` 이다.
- 확인
    * pipenv 가 생성되는 방식은 virtualenv 와 유사한데(포함한 거니까 당연ㅡㅡ), 생성 위치는 차이가 있다.
    * 기존 ```virtualenv [venv 이름]``` 하면 현 위치를 기준으로 venv 가 생성되고 그 밑에 **python.exe** 및 모든 라이브러리들이 설치됐지만,
    * ```pipenv shell``` 혹은 ```pipenv --python 3.7``` 방식은 현 위치에 **Pipfile** 만 생성되고 나머지 실행파일이나 라이브러리들은 가상환경만 모아 놓은 특정 위치에 만들어진다. (Pipfile 은 관련 환경설정 및 스크립트 파일)
    * ```pipenv --py``` 하면 가상환경 파이썬 런타임이 새롭게 설치된 진짜 위치를 알 수 있다.
    * ```$HOME/.local/share/virtualenvs/pipenv-tutorial-S-kj1fAT/bin/python``` 보통 이런 곳에 위치한다.
    * 윈도우에서는 ```C:\Users\Adminisrator\.virtualenvs\[생성된 pipenv]``` 폴더 하위에 생성된다.
    * 새로운 pipenv 가 생성되면 이곳에 하나씩 추가되는 식이다.
- 필요한 패키지 설치
    * ```pipenv install [설치할 패키지명]```
    * 만약 **--dev** 옵션을 사용한다면 devDependencies 를 지원하게 된다. (개발용 의존성. default 는 배포용이다)
    * 그러면 Pipfile 에 [dev-packages] 하위에 리스팅 된다.
    ```text
    $ cat Pipfile
    [[source]]
    name = "pypi"
    url = "https://pypi.org/simple"
    verify_ssl = true
    
    [dev-packages]
    requests = "*"  # 여기에
    
    [packages]
    requests = "*"
    
    [requires]
    python_version = "3.7"
    ```
- venv 에서 파이썬 스크립트 실행
    * 현재 파이썬 버전을 출력하는 간단한 app.py 파일 작성
    ```python
    # 📁 app.py
    import sys
    
    def main():
        print(sys.version)
    
    if __name__ == '__main__':
        main()
    ```
    * 실행 결과
    ```text
    (pipenv-tutorial-S-kj1fAT) $ python app.py
    3.7.1 (default, Nov 28 2018, 11:51:47)
    [Clang 10.0.0 (clang-1000.11.45.5)]
    ```
    * 직접실행 말고 pipenv 에서 scripts 로 실행하려면 Pipfile 의 [scripts] 섹션에 실행 스크립트를 작성한다.
    ```text
    (pipenv-tutorial-S-kj1fAT) $ vi Pipfile
    
    [[source]]
    name = "pypi"
    url = "https://pypi.org/simple"
    verify_ssl = true
    
    [dev-packages]
    requests = "*"
    
    [packages]
    requests = "*"
    
    [requires]
    python_version = "3.7"
    
    [scripts]  # 섹션을 만들고
    start = "python app.py"  # 실행할 커맨드를 추가하세요, "" 로 감싸주어야 합니다
    ```
    * 그리고 ```pipenv run start``` 커맨드를 내리면 된다.
    ```text
    (pipenv-tutorial-S-kj1fAT) $ pipenv run start
    3.7.1 (default, Nov 28 2018, 11:51:47)
    [Clang 10.0.0 (clang-1000.11.45.5)]
    ```
- Pipfile, Pipfile.lock 설명
    * 최초 ```pipenv shell``` 명령어를 호출하면 Pipfile 과 Pipfile.lock 두 파일이 생성된다.
    * 전자는 pipenv 로 만들어진 가상환경이 참조하는 환경설정 파일이고(파이썬 버전, 설치한 패키지, 스크립트 등),
    * 후자는 'deterministic-builds', 즉 결정론적 빌드를 구현하는 데 사용한다.
        * deterministic-builds 란, "Be able to get the exact same set of dependencies on multiple machines" 로 정의되는데
        * 의미는 어떤 상황에서도(복수의 머신, 여러 가상환경 혹은 을 의미하는 듯?) 동일하게 site-packages tree 를 유지하며 설치할 수 있도록 보장한다는 것이다.
        * 위키백과의 <a href="" target="_blank">결정론적 알고리즘</a>에서는 "어떤 특정한 입력이 들어오면 언제나 똑같은 과정을 거쳐서 똑같은 결과를 내놓는다"고 설명한다.
    * ```pipenv install [패키지명]``` 으로 원하는 의존성 및 버전의 lib 환경구성을 마쳤다면, **lock** 키워드를 사용하여 **Pipfile.lock** 파일에 업데이트할 수 있다.
        * ```pipenv lock```
        * 이 명령은 기존 **pip3** 모듈에서 ```pip3 freeze > requirements.txt``` 와 유사한 역할을 한다고 보면 된다.
    * 결국 **Pipfile** 에는 환경구성 및 라이브러리, 스크립트 정보가 들어 있고, **Pipfile.lock** 에는 패키지 의존성 정보가 들어 있어서
    * 해당 프로젝트를 새롭게 **fork** 하거나 **pull** 한 로컬에서는 원하는 위치에서 ```pipenv shell``` 혹은 ```pipenv install``` 만으로 모든 환경을 자동구성할 수 있다.
- 만들었던 venv 제거하기
    * ```pipenv --rm```
    ```text
    (pipenv-tutorial-S-kj1fAT) $ pipenv --rm
    Removing virtualenv (/Users/doondoony/.local/share/virtualenvs/pipenv-tutorial-t1c6CZgR) …
    ```
    * 실제 가상환경이 설치되었던 __$HOME/.local/**__ 하위 디렉토리가 삭제되었음을 확인 가능하다.
    * 아니면 이 실제경로를 ```rm -rf``` 명령으로 그냥 삭제해버려도 된다.



