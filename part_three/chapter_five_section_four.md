## Chapter 5. First-Class Functions

### The Seven Flavors of Callable Objects

객체가 callable 인지 확인하려면, **callable()** built-in 함수를 사용한다.
Python Data Model의 문서에서는 아래와 같은 일곱개의 callable 타입을 소개한다.

* User-defined functions
	- def statements 또는 lambda expressions으로 생성

* Built-in functions
	- len 또는 time.strftime 과 같은 C언어로 구현된 함수

* Built-in methods
	- dict.get과 같은 C언어로 구현된 method

* User-defined functions
	- def statements 또는 lambda expressions으로 생성

* Methods
	- class의 body에 정의한 함수

* Classes
	- class는 불려질 때, **\_\_new\_\_** 메소드를 호출하여 instance를 생성한다. 그리고 **\_\_init\_\_** 로 instance를 초기화하고, 마지막으로 호출자(caller)에게 반환한다.
	- 파이썬에서는 **new** operator가 별도로 없기 때문에, 클래스를 호출하는 것은 함수를 호출하는 것과 같다.

* Class instances
	- class가 **\_\_call\_\_** method를 정의하면, 해당 클래스의 instance는 함수로 불려진다.

* Generator functions
	- **yeild** 키워드를 사용하는 함수 또는 메소드.


### User-Defined Callable Types

파이썬의 실제 객체뿐만 아니라, 임의의 파이썬 객체 또한 함수처럼 행동하게 할 수 있다.
**\_\_call\_\_** 메소드를 구현(implement)하기만 하면, 그것이 가능해진다.

* *BingoCage* 는 셔플된 list에서 아이템을 꺼낸다
```python
import random

class BingoCage:

	def __init__(self, items):
		self._items = list(items)  # <1>
		random.shuffle(self._items)

	def pick(self):
		try:
			return self._items.pop()
		except:
			raise LookupError('pick from empty BingoCage')

	def __call__(self):  # <2>
		return self.pick()
```

<1>: **\_\_init\_\_** 은 어떠한 iterable도 받는다. 
	list 생성자로 전달받은 인자의 local copy를 만들어 예기치 않은 side effect를 방지한다. <br>
<2>: bingo.pick()을 **\_\_call\_\_** 메소드에 정의하여 instance가 함수처럼 동작

---
* A simple demo of *BingoCage*
```python
bingo = BingoCage(range(3))
bingo.pick()  # 1
bingo()  # 0
callable(bingo)  # True
```

호출 때마다 내부 상태 (internal state)를 유지해야 하는 class는 위와 같이 **\_\_call\_\_** 를 구현하여 function-like 객체로 다루는 것이 간편한 방법이다.

**Closure**와 **decorators** 는 위와 달리 전혀 다른 방식으로 내부 상태를 가진 함수를 만든다.