
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190201-lightgray.svg?style=flat-square)

# EL, JSTL 에러 및 유의사항 모음

## Scope 관련 유의점

- EL 이나 JSTL  특정 변수나 데이터를 활용하려면 특정 Scope 내에 속성으로 저장된 것을 대상으로 해야 한다
- Scope : pageScope (pageContext), requestScope (HttpServletRequest, response), sessionScope (HttpSession), applicationScope
- 각 스코프에 속성 저장은 setAttribute() 메서드를 사용하고, 속성 사용은 getAttribute() 메서드를 사용한다






