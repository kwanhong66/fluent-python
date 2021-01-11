## Chapter 3. Dictionaries and Sets

`dict` 타입은 프로그램에서 널리 사용될 뿐만 아니라, 파이썬 구현의 핵심 요소이다. 모듈 네임스페이스(Module namespaces), 클래스와 인스턴스 속성, 함수 키워드 인자(Function keyword arguments)는 dictionaries가 적용된 파이썬의 핵심 구조들 중 일부다.

`dict`는 핵심적인 역할을 하기 때문에 높은 수준으로 최적화 되어있다. 파이썬의 `dict`가 높은 성능을 가지는 이유는 `Hash tables`에 기반을 두기 때문이다.

이번 챕터에서 `dict`와 마찬가지로 `hash tables`로 구현된 `set` 타입도 다룬다. `dictionaries`와 `sets`를 구현할 때, `Hash tables`의 동작 방식을 아는 것이 중요하다.

---

### Generic Mapping Types

`collections.abc` 모듈은 `dict`나 유사한 타입의 인터페이스를 형태화하는 ABC인 `Mapping`과 `MutableMapping`을 제공한다.

표준 라이브러리의 모든 mapping types은 기본 dict를 구현에 사용한다. 따라서, 키(key)가 `hashable` 해야 한다는 한계점은 동일하다.

* **What is hashable?**
    - 생애주기 동안 바뀌지 않은 해시 값을 가진 객체를 hashable하다고 한다. (*\_\_hash\_\_* 메소드 필요)
    - 다른 객체와 비교할 수 있어야 한다. (*\_\_eq\_\_* 메소드 필요)
    - 가장 기본 단위(atomic)의 immutable type인 `str`, `bytes`, `numeric types`는 모두 hashable
    - `frozenset` 또한 hashable (frozenset의 정의에 따라 원소들이 hashable)
    - `tuple`는 모든 아이템이 hashable일 경우에만 hashable

* tuple의 hashability
```python
tt = (1, 2, (30, 40))
hash(tt)  # 8027212646858338501
tl = (1, 2, [30, 40])  # TypeError: unhashable type: 'list'
tf = (1, 2, frozenset([30, 40]))
hash(tf)  # -4118419923444501110
```

이와 같은 기본 원칙에 따라, dictionaries를 여러 가지 방법으로 만들 수 있다.

* Several ways of building dictionaries

```python
a = dict(one=1, two=2, three=3)
b = {'one': 1, 'two': 2, 'three': 3}
c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))  # zip returns iterator of tuples
d = dict([('two', 2), ('one', 1), ('three', 3)])
e = dict({'three': 3, 'one': 1, 'two': 2})
a == b == c == d == e  # True
```


### dict Comprehensions

파이썬 2.7부터 `listcomps`와 `genexps`이 `dict` comprehension에 적용되었다. `dictcomp`는 임의의 iterable로부터 `key:value` 상을 생성하여 dict 객체를 만든다.

* 동일한 tuple list로부터 두 개의 dictionaries를 만드는 dict comprehension 사용을 보여준다.
```python
DIAL_CODES = [  # <1>
    (86, 'China'),
    (91, 'India'),
    (1, 'United States'),
    (62, 'Indonesia'),
    (55, 'Brazil'),
    (92, 'Pakistan'),
    (880, 'Bangladesh'),
    (234, 'Nigeria'),
    (7, 'Russia'),
    (81, 'Japan')
]
country_code = {country: code for code, country in DIAL_CODES}  # <2>
country_code  # {'China': 86, 'India': 91, 'Bangladesh': 880...} 
{code: country.upper() for country, code in country_code.items() if code < 66}  # {1: 'UNITED STATES', 55: 'BRAZIL', 62: 'INDONESIA', 7: 'RUSSIA'}
```

<1> 쌍을 가진 리스트는 dict 생성자에 바로 사용할 수 있다. <br>
<2> 쌍의 순서가 바뀌었다.
