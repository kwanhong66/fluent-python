## Chapter 7. Function Decorators and Closures

### When Python Executes Decorators

데코레이터는 꾸며진 함수(decorated function)가 정의되고 나서 바로 실행된다는 것이 핵심 특징이다.
대개 파이썬이 모듈을 로드하는 *import time* 시점이다.

* registration.py module
```python
registry = []  # <1>

def register(func):  # <2>
	print('running register(%s)' % func)  # <3>
	registry.append(func)
	return func  # <4>

@register
def f1():
	print('running f1()')

@register
def f2():
	print('running f2()')

def f3():
	print('running f3()')

def main():
	print('running main()')
	print('registry ->', registry)
	f1()
	f2()
	f3()

if __name__ == "__main__":
	main()  # <5>
```

<1>: registry list는 @register가 데코레이트한 함수의 참조값을 가진다. <br>
<2>: register는 함수를 인자로 받는다 <br>
<3>: 어떤 함수가 데코레이트 되었는지 출력한다. <br>
<4>: 함수를 반환해야 한다; 여기서는 전달받은 함수 인자를 그대로 반환한다. <br>
<5>: main() 함수는 registration.py이 스크립트로 실행될 때 호출된다.

* *registration.py* 의 실행 결과는 아래와 같다.
```
$ python3 registration.py
running register(<function f1 at 0x100631bf8>)
running register(<function f2 at 0x100631c80>)
running main()
registry -> [<function f1 at 0x100631bf8>, <function f2 at 0x100631c80>]
running f1()
running f2()
running f3()
```

모듈의 다른 어떠한 함수보다 register가 먼저 실행된 점을 주목해야 한다.
register는 호출되면, 데코레이트할 함수 객체를 인자로 받는다.
모듈이 로드되고 난 후에, registry는 f1과 f2 두 개의 데코레이트된 함수 참조값을 가지게 된다.

위의 예제가 강조하려는 요점은 함수 데코레이터는 모듈이 임포트 되고 나서 바로 실행되고, 데코레이트 된 함수는 명시적으로 호출되었을 때만 실행된다는 점이다. 이 점은 Pythonista들이 부르는 *import time*과 *runtime*의 차이점을 강조한다.

---

데코레이터가 실제 코드에서 일반적으로 도입되는 경우를 고려하면, 위의 예제는 아래와 같은 두 가지 이유에서 예외적인 경우이다.

* 위의 예제에서는 데코레이터 함수와 데코레이트 된 함수와 같은 모듈에 정의되어있다.
	- 실제 적용에서는 데코레이터는 대개 하나의 모듈에 정의되고 다른 모듈에 있는 함수에 적용된다.

* register 데코레이터는 인자로 전달받은 함수를 그대로 반환한다.
	- 실제로는 대부분의 데코레이터는 내부 함수를 정의하고 그것을 반환한다.