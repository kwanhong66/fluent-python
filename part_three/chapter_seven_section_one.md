## Chapter 7. Function Decorators and Closures

Function decorators(함수 데코레이터)는 소스 코드 상에서 함수에 표시(mark)를 하여, 함수의 특정 동작을 보강하는 기능이다.
강력한 기능이지만 제대로 다루기 위해서는 클로저(closure)를 이해해야만 한다.

이 챕터의 최종 목표는 함수 데코레이터가 동작하는 방식을 정확하게 설명하는 것이다. 가장 단순한 registration 데코레이터부터 조금 더 복잡한 인자화(parameterized) 데코레이터까지 다룬다.

챕터의 목표를 달성하기 위해 아래의 토픽들을 다룬다.

* 파이썬이 데코레이터 문법을 평가(evaluate)하는 방식
* 파이썬이 변수가 지역적인지 판단하는 방식
* 클로저의 존재 이유와 동작 방식
* nonlocal 로 해결 가능한 문제

---

### Decorators 101

데코레이터는 다른 함수를 인자로 받는 호출 가능 객체(callable)이다. 데코레이터는 전달받은 함수를 처리하여(decorate) 반환하거나, 다른 함수 또는 호출 가능한 객체로 교체하여 반환한다.


* decorate라는 이름의 데코레이터 
```python
@decorate
def target():
	print('running target()')
```

* 위의 코드는 아래의 코드와 같이 작성하여도 동일하다.
```python
def target():
	print('running target()')

target = decorate(target)
```

target 이름은 기존 target 함수를 참조하지 않고, decorate(target)이 반환하는 함수를 참조한다.

* 데코레이터는 대개 함수를 다른 함수로 교체한다.
```python
def deco(func):
	def inner():
		print('running inner()')
	return inner  # <1>

@deco
def target():
	print('running target()')

target()  # running inner()  <2>
target  # <function deco.<locals>.inner at ...>  <3>
```

<1>: deco 함수는 내부의 inner 함수 객체를 반환한다. <br>
<2>: 데코레이트 된 target은 실제적으로는 inner를 실행하게 된다. <br>
<3>: target은 이제 inner를 참조한다는 것을 확인 <br>

엄밀히 말하면 데코레이터는 단순히 편의 문법(syntactic sugar)일 뿐이다.
앞서 보았듯이, 데코레이터는 일반적인 콜러블(callable)처럼 다른 함수를 넘겨 호출할 수 있다.

요약하자면, 첫 번째 주요점은 데코레이터는 꾸미고자 하는 함수를 다른 함수로 교체한다는 점이다. 두 번째는 데코레이터는 모듈이 로드되었을 때 실행된다는 점이다.

