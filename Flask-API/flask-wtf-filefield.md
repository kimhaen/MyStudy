
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190529-lightgray.svg?style=flat-square)

# Flask-WTF 폼으로 File 업로드 시 발생한 이슈

> 는 내 주의부족 + 실수이다..

### 계속해서 빈 값이 넘어간다..?

- ```enctype=multipart/form-data``` 옵션을 설정하지 않으면 무조건 빈 값이 넘어간다는 것..

### 프로젝트 내 파일 저장 경로 문제

- 일단 브라우저로부터 전송된 file 이름을 구한다. 그리고 그것의 filename 을 벡자이크 **utils.secure_filename** 으로 감싼다.
    * file = form.profile.data
    * filename = secure_filename(file.filename)
- **app.instance_path** : 프로젝트의 루트 경로를 반환한다. 그러나 확인해보니 말단에 'instance' 라는 문자열을 포함하고 있어서, 그것을 기준으로 split 함.
- **os.path.join** 으로 적절한 경로를 조립한다.
- **os.path.exists 로 조립된 경로의 파일이 이미 존재하는지 확인 후,
    * 존재하지 않으면 그대로 두고
    * 존재하면 **secrets.token_hex(16)** 으로 생성된 해시문자열을 더해 파일명을 변경한다.
- 최종적으로 **file.save([최종경로])** 로 실제 경로에 지정한 파일명으로 저장한다.

### 로직 모듈화 및 db 저장

- 뷰 함수를 포함하는 **app.py** 는 브라우저 요청에 대한 라우팅을 수행한다.
- 이상적으로는 한 모듈이나 함수는 하나의 컨셉이나 기능을 수행하도록 코드를 작성해야 하기 때문에,
- 파일 저장 및 업로드하는 로직은 별도의 **service** 모듈에서 수행하는 것이 타당하다.
- 뷰 함수는 service 로직의 결과를 수용하여 적절한 response 를 반환하도록 한다.
- **mongodb** 를 사용하므로, service 에서는 데이터를 dict 모델에 담아 validation 을 수행한 후 db 에 그대로 저장한다.