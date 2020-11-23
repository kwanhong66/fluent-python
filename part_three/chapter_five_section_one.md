## Chapter 5. First-Class Functions

파이썬에서 함수는 first-class 객체이다.

프로그래밍 언어 이론 연구자들은 "first-class object"을 다음과 같이 정의한다.

* 런타임에 생성 (Created at runtime)
* 변수에 할당되거나 자료구조의 원소
* 함수의 인자(argument)로 전달
* 함수의 결과로 반환 가능

파이썬에서는 integers, strings 그리고 dictionaries이 first-class 객체의 예시이다.
파트 3에서는 함수를 객체로 다루는 실제 적용 방식과 그에 따른 영향을 살펴본다.

---

### Treating a Function Like an Object

아래 예제에서 파이썬 함수가 객체라는 것을 알 수 있다.

* 함수를 생성하고 테스트
```python
def factorial(n):
	'''return n!'''
	return 1 if n < 2 else factorial(n-1)

factorial(42)  # 14050061177528...
fatorial.__doc__  # 'returns n!'
type(factorial)  # <class 'function'>
```

함수를 만들고, 호출하고, **\_\_doc\_\_** 속성(attribute)를 읽고, 함수 객체가 **function** 큻래스의 instance인 것을 확인했다.

* 함수 객체가 "first class"라서 가능한 것들을 보여주는 예제
```python
fact = factorial  # <1>
fact  # <function factorial at 0x...>
fact(5)  # 120
map(factorial, range(11))  # <map object at 0x...>. <2>
list(map(fact, range(11)))  # [1, 1, 2, 6, 24, 120, 720, 5040, ...]
```

<1>: 변수에 할당하여 그 이름으로 호출 가능
<2>: map 함수의 인자로 전달 가능

함수가 first-class이기 때문에 프로그래밍을 functional style로 작성할 수 있다.
함수형 프로그래밍의 트레이드마크는 **higher-order functions** 이다.