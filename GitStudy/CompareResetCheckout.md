
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-181231-lightgray.svg?style=flat-square)

참조 : [Git 도구 - Reset 명확히 알고 가기] (https://git-scm.com/book/ko/v2/Git-%EB%8F%84%EA%B5%AC-Reset-%EB%AA%85%ED%99%95%ED%9E%88-%EC%95%8C%EA%B3%A0-%EA%B0%80%EA%B8%B0)

## HEAD, index, working directory

> git 은 총 3 개의 트리로 운영되는 강력한 형상관리 도구이다. 최초 working directory 에서 작업을 수행하고, 그 변경결과를 index 에 스테이징한 후 최종적으로 HEAD 트리에 커밋하는 과정을 거친다. 이 과정에서 각 브랜치와 커밋 형상 객체들를 통해 어느 커밋시점에 대해서건 안정적인 형상관리가 가능하다. (편의상 트리로 부르지만 자료구조의 트리 구조를 의미하는 것은 아니다)

- HEAD		: 마지막 커밋 스냅샷, 다음 커밋의 부모 커밋
- Index		: 다음에 커밋할 스냅샷
- Working dir	: 샌드박스, 현재 작업정보 저장

특히 HEAD 트리는 checkout 명령을 통해 관리되며, reset 명령을 통해 참조하는 브랜치의 커밋시점을 관리한다.

## checkout 과 reset

> checkout 과 reset 은 브랜치 지정과 객체참조를 통해 커밋 버전을 관리하기 위한 강력한 명령어이다.

### checkout	: 이 명령은 현재 작업하는 브랜치를 지정할 수 있다. 또한 -b 옵션과 함께 사용하면, 새로운 브랜치를 생성하면서 체크아웃을 수행한다. (본래 브랜치 생성 명령은 branch 이다)

```
$ git checkout <branch>
$ git checkout -b <branch>
```

* checkout 명령의 관건은 HEAD 트리에 있다. HEAD 트리는 current branch 의 주소를 포인터 형식으로 참조하여 해당 브랜치의 최신 커밋을 의미하게 된다. 그러므로 working directory 에서 작업한 후 add 를 통해 index 에 스테이징 하고 최종적으로 커밋을 한다면 이 브랜치의 최신 커밋이 변경될 것이다(정확히는 최신 커밋 객체가 생성되고 HEAD 브랜치가 최신 커밋 객체를 가리키게 된다). 만약 다른 커밋 시점을 가리키는 브랜치를 참조하도록 하면 최신 시점이 아닌 다른 시점을 의미하게 된다 (이때 최신커밋을 참조하는 브랜치의 정보는 보이지 않는다. 다시 돌아가면 볼 수 있다). HEAD 는 index 에 있는 다음 커밋정보의 부모 커밋으로 지칭된다.

### reset	: reset 명령은 언뜻 chechout 과 같이 특정 브랜치를 참조만 함으로써 해당 커밋 시점을 가리키도록 하는 것같이 보이지만, 엄밀히 말하면 참조하는 브랜치 자체를 이동시킬 수 있다. 그것은 각 커밋 시점에 대한 객체가 존재하고, 특정 브랜치는 이러한 객체 중 하나를 참조하는 구조이기 때문에 가능하다.

```
$ git reset <커밋객체주소>
$ git reset --soft <커밋객체주소>
$ git reset --mixed <커밋객체주소>
$ git reset --hard <커밋객체주소>
```

* --soft 옵션은 HEAD 브랜치가 지정된 커밋객체주소를 참조하도록 한다. 이때 HEAD 와 index 간 이력에 차이가 발생한다.
* --mixed 옵션은 HEAD 브랜치가 지정된 커밋객체주소를 참조하도록 하는 것은 물론, index 도 동일한 객체주소를 참조하도록 한다. 아무런 옵션 없이 실행한 reset 명령은 --mixed 옵션에 해당하는 지점까지 작업한다.
* --hard 옵션은 위험하다. HEAD, index 를 넘어 working directory 까지 지정한 객체의 시점으로 변경시킬 수 있기 때문이다. 이것의 결과는 다시 되돌릴 방법이 없다. 다만, 작업공간에서 사라져버린 최신 커밋객체가 어딘가 존재한다면(예를 들어 다른 브랜치가 최신커밋객체를 참조하고 있거나 정 안되면 그것의 주소값이라도 알고 있다면), reflog 를 이용해서 복워할 수 있다.


[백준2839]: https://www.acmicpc.net/problem/2839


