
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190103-lightgray.svg?style=flat-square)

# Statement 와 PreparedStatement

> Statement 와 PreparedStatement 는 DBMS 에서 SQL 쿼리를 실행하기 위한 인터페이스이다. DB 로 작성된 쿼리를 전송한다는 점에서 둘은 동일지만, 재사용성의 측면에서 차이가 있다

- 재사용성이라는 차이점을 기술적으로 언명하자면, 캐시의 사용여부이다. 작성된 sql 문을 특정 시점에서 실행한다는 점은 모두 동일하지만, PreparedStatement 는 pstmt 를 생성하면서 '?' 인자를 포함한 채로 일단 실행하고, 그 정보를 캐시에 기억하여 차후 '?' 에 여러 값을 대체하면서 재사용이 가능하다. 말 그대로 우선(pre) 컴파일된 쿼리의 인자만 바꿈으로써 다시 재사용하는 것이다
- 이것은 성능 차이로 귀결되는데, Statement 는 쿼리의 매 실행 시마다 재컴파일을 수행하지만, PreparedStatement 는 한번 컴파일한 후 그것을 재사용하므로 시스템 부하가 적고 속도도 빠르다
- Statement 나 PreparedStatement 는 인터페이스이며, Connection 객체로부터 각각을 생성하거나 얻어와 변수로 참조시키는 방식을 취한다
- try-catch 또는 throws 처리가 필수임

## Statement

Statement 객체가 만들어진 후, 매 실행 시마다 컴파일하여 실행

```JAVA
String sql = "select * from users where id = " + id + ", pwd = " + pwd;
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(sql);
```

## PreparedStatement

PreparedStatement 객체를 만들며 우선 컴파일하고, 그 정보를 캐시에 저장했다가 '?' 인자만 대체하며 재사용한다

```JAVA
String sql = "select * from users where id = ?, pwd = ?";
PreparedStatement pstmt = conn.preparedStatement(sql);
pstmt.setString(1, id);
pstmt.setString(2, pwd);
ResultSet rs = pstmt.executeQuery();
```








