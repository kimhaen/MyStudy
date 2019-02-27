
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190227-lightgray.svg?style=flat-square)

# 마이바티스 심화

## 동적 SQL

- sqlMap 에서 쿼리를 작성할 때 상황에 따라 가변적인 조건이 적용되도록 동적 sql 을 생성할 수 있다
- 물론 애초에 분기에 따른 별도의 쿼리를 작성하면 되지만, 가급적 db 내에서 보다 적은 실행횟수로 원하는 결과를 얻는 것이 효율적이다
- 예시를 보자

### if

```XML
	...

	<select id = 'if' parameterType = 'board.BoardVo' resultMap = 'String'>
		SELECT * FROM BOARD
		<if test = 'month != null'>
			WHERE TO_CHAR(mDate, 'MM') = #{month}
		</if>
	</select>

	...
```

- 위 예제는 입력 파라미터 내에 month 변수의 내용이 존재한다면 if 태그 내에 속한 where 절이 포함되어 실행되도록 작성한 쿼리이다
- 여기서 조건 태그는 jstl 의 코어 라이브러리와 유사하므로 사용상 문법에 주의할 것
- 조건분기는 비슷한 방식으로 choose - when - otherwise 태그를 활용할 수도 있다

### where

- 혹은 조건절을 where 태그로 감싸면 보다 간결하면서도 포괄적인 쿼리 작성이 가능하다

```XML
	...

	<select id = 'if' parameterType = 'board.BoardVo' resultMap = 'String'>
		SELECT * FROM BOARD
		<where>
			<if test = 'worker != null'>
				worker = #{worker}
			</if>

			<if test = 'month != null'>
				AND TO_CHAR(mDate, 'MM') = #{month}
			</if>
		</where>
	</select>

	...
```

- where 절에 해당하는 여러 분기를 하나의 영역에 작성할 수 있으며,
- 쿼리 사이 사이 존재하는 WHERE 와 같은 부분적인 키워드들은 생략되었다

### foreach

- myBatis 의 가장 강력한 기능 중 하나이다
- 만약 입력 파라미터의 개수가 정해지지 않았을 때 그것들을 조건삼아 쿼리를 작성한다면,
- 마이바티스의 foreach 기능을 사용하면 가변적인 파라미터를 그 개수만큼 모두 적용할 수 있다
- 입력 파라미터가 List 형식으로 입력된다면, 그 구분인자는 index='index' 임을 명심할 것 (Map 컬렉션은 index='Key' 이다)
- 적용 결과는 각각 내부적으로 'OR' 키워드로 구분된다
- 결과적으로 foreach 기능을 사용하면, 입력 정보의 개수나 크기를 고려하지 않아도 된다

```XML
	...

	<select id='foreach_list' parameterType='bean.DynamicVo' resultMap = 'dynamicMap'>
		select * from board
		<where>
			<if test='worker != null'>
				worker in
				<foreach collection='workerList' item='data' index='index'
					open="(" close=")" separator=",">
					#{data}
				</foreach>
			</if>
		</where>
	</select>

	...
```

### 주의사항

- config xml 파일 설정 시 주의사항
	* 내부 mapper 에 여러 xml 매핑 파일들이 적용되어 있을 때,
	* 각 매퍼끼리는 상관성이 없더라도 하나의 매퍼에서 에러가 발생하면 config 내의 모든 매퍼를 사용할 수 없다
	* 이 때는 기능이나 에러 가능성에 따라 confinuration 설정을 별도로 작성하면 된다









