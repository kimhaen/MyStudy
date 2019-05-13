
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190513-lightgray.svg?style=flat-square)

# Jinja2 템플릿 엔진에서 dict.dict.element 접근시 에러

> Jinja2 템플릿 엔진은 데이터 표현 구문({{  }})을 통해 dict.element 방식으로 dict 에 손쉽게 접근할 수 있다.

> python 에서 dict 타입의 value 값은 mutable 하고, json 과 호환되는 모든 데이터 타입의 투입이 가능하다. 즉, list 의 dict, dict 의 dict 도 투입 가능하다는 말이다.

#### 예를 들어, 다음과 같은 dict 가 존재할 때 Jinja2 엔진은 곧바로 접근이 가능하다.
```python
sample = { _id: 1, name: 'gildong', age: 29 }
```
```text
출력 : {{ sample._id }} / {{ sample.name }} / {{ sample.age }}
```
결과 -
```text
출력 : 1 / gildong / 29
```

#### 그러나, **dict.dict.element** 방식도 가능할까?
- dict 자료형의 특성상 다음과 같은 정의가 가능하다.
```python
sample2 = { _id: 2, profile: { name: 'saejong', age: 22 } }
```
- 이것을 Jinja2 템플릿 엔진을 통해 동일하게 접근해보면,
```text
출력 : {{ sample._id }} / {{ sample.profile.name }} / {{ sample.profile.age }}
```
- 다음과 같은 에러가 발생한다.
```text
'dict object' has no attribute 'profile'
```
- 해석은 딕셔너리 객체가 'profile' 이름에 해당하는 원소 속성을 가지고 있지 않다는 말인데,
- 이게 무슨말인지 원인을 알 수가 없다.

#### 해결
- 근본적인 원인은 알 수 없으나 결론적으로 말하자면, Jinja2 Template engine 의 **제어 구문**으로 감싸면 접근이 가능하다.
- 이런 식으로
```python
sample2 = { _id: 2, profile: { name: 'saejong', age: 22 } }
```
```text
{% if sample.profile %}
    출력 : {{ sample._id }} / {{ sample.profile.name }} / {{ sample.profile.age }}
{% endif %}
```
(for 구문으로도 접근 가능하다)<br/>
결과 -
```text
출력 : 2 / saejong / 22
```

#### 정리
- Jinja2 템플릿 엔진은 dict 자료형에 대해서 dict.element 방식으로 직접접근이 가능했으나,
- dict 의 특성에 따라 dict.dict.element 방식으로 접근하게 될 때에는 원인 모를 에러가 발생했다. (두 번째 dict 타입을 찾지 못하는?)
- 이때, 템플릿 엔진의 {%%} 제어구문으로 감싸주면 에러 없이 접근이 가능하다. (원인은 알 수 없다. dict 존재유무를 확정지어줘야 하는 건가??)


