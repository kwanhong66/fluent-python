## Chapter 5. First-Class Functions

### Higher-Order Functions

함수를 인자로 받거나 결과로 함수를 반환하는 함수를 *higher-order function* 이라고 한다.

앞서서 보았듯이, **map** 함수가 *higher-order function* 의 예시로서 함수를 인자로 받는 경우다.
다른 예시로서 **sorted** 함수가 있는데, key 인자로 함수를 받아서 정렬하는 아이템 각각에 적용을 한다.

* 단어 리스트의 길이 기준 정렬
```python
friuts = ['strawberry', 'fig', 'apple', 'cherry', 'raspberry', 'banana']
sorted(friuts, key=len)
['fig', 'apple', 'cherry', 'banana', 'raspberry', 'strawberry']
```

* 반전(reverse)된 철자 기준 정렬
```python
def reverse(word):
	return word[::-1]
reverse('testing')  # 'gnitset'
sorted(friuts, key=reverse)
['banana', 'apple', 'fig', 'raspberry', 'strawberry', 'cherry']
```

---

함수형 프로그래밍 패러다임에서 가장 알려진 higher-order function은 *map*, *filter*, *reduce*, 그리고 *apply* 이다. *apply* 함수는 파이썬 2.3에서 deprecated 되었고 파이썬 3에서는 삭제되었다.

*map*, *filter*, *reduce* 함수는 아직 있지만 대부분의 경우에는 더 나은 대체 방안이 있다.


### Modern Replacements for map, filter, and reduce

 *map* 함수와 *filter* 함수는 아직 파이썬 3의 built-in으로 있지만, list comprehension과 generator expression의 도입으로 중요도가 떨어지게 되었다.

 listcomp와 genexp는 map과 filter의 조합이 하는 일을 수행하면서도 가독성도 더 좋다.

* map과 filter의 조합으로 생성한 factorial list와 대체 방안인 listcomp와 비교
```python
list(map(fact, range(6)))  # [1, 1, 2, 6, 24, 120]
[fact(n) for n in range(6)]  # [1, 1, 2, 6, 24, 120]
list(map(factorial, filter(lambda n: n % 2, range(6))))  # [1, 6, 120]
[factorial(n) for n in range(6) if n % 2]  # [1, 6, 120]
```

 파이썬 3에서는 map과 filter가 generator (a form of iterator)를 반환한다. 따라서, generator expression으로 바로 교체 가능하다.

 *reduce* 함수는 파이썬 2에서는 built-in 이었지만, 파이썬 3에서는 **functools** 모듈에 포함되었다.
 대부분의 사용 경우인 합산(summation)에서 **sum** built-in 함수갸 더 나은 성능을 보인다.

* reduce와 sum을 이용하여 99까지의 합산
```python
from functools import reduce
from operator import add
reduce(add, range(100))  # 4950
sum(range(100))  # 4950
```

Higher-order function을 사용하기 위해서는 작은 단위의 one-off 함수가 필요한 경우가 있다. 이 경우에 필요한 것이 익명 함수(anonymous functions)이다.