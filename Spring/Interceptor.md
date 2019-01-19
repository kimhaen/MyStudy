
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190117-lightgray.svg?style=flat-square)

# Interceptor

> CRUD 로직을 개발하다 보면 특정 작업을 처리하기 전이나 후, 아니면 모든 트랜잭션 수행 이후 시점에 공통적으로 수행해야 할 로직이 있을 수 있다<br/>
> 물론 이러한 공통 로직을 일일히 코딩할 수도 있겠으나, 반복작업은 최대한 지양하는 것이 생산성 및 유지보수 측면에 유리하므로 스프링에서는 Interceptor 를 사용한다

## 기본 개념

- 말 그대로 요청된 메인 로직 수행 시 특정 시점에 작업의 흐름을 가로채(인터셉트) 자신의 로직을 먼저 실행하는 컴포넌트이다
- 일반적으로 정보 수정 및 삭제 로직에서 세션의 유효성 체크할 때 많이 사용된다