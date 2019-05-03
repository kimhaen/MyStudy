
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190503-lightgray.svg?style=flat-square)

# git 내부의 캐시정보 삭제하기

- git 을 사용하다 보면 내부의 캐시가 존재해서 변경 사항이 적용되지 않는 경우가 있다 > .gitignore 파일 등..
- 이때 캐시 정보를 삭제하는 커맨드는 다음과 같다

```Bash
git rm -r --cached .
```

- 마지막 목적경로 자리에 원하는 파일이나 디렉토리를 써도 된다.
