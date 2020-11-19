## Chapter 8. Object References, Mutability, and Recycling

### Function parameters as references

파이썬의 파라미터 전달(parameter passing)은 *call by sharing*이다.
Call by sharing은 함수 안의 인자(parameter)가 실제 함수에게 전달된 인자(argument)의 alias가 되는 것을 의미한다. 또는, 함수의 formal parameter는 actual parameter(argument)의 참조의 복사본을 얻는다는 것을 의미한다.

*Formal parameters and actual parameters*: [Link](https://stackoverflow.com/questions/45065288/formal-and-actual-parameters-in-a-function-in-python)

이와 같은 scheme은 함수에서 parameter로 전달받은 mutable object를 변경하는 결과가 발생하기도 한다.

(기존 개념과 동일한 것인지 재검증 필요)

* pass by assignment
* pass by object reference

* 함수는 전달받은 mutable object를 의도치 않게 변경할 수 있다.
```python
def f(a, b):
    a += b
    return a
x = 1
y = 2
f(x, y)  # 3
x, y  # (1, 2); nubmer is unchanged
a = [1, 2]
b = [3, 4]
f(a, b)  # [1, 2, 3, 4]
a, b  # ([1, 2, 3, 4], [3, 4]); list is changed
t = (10, 20)
u = (30, 40)
f(t, u)  # (10, 20, 30, 40)
t, u  # ((10, 20), (30, 40))
```

### Mutable types as parameter defaults: Bad idea

Python에서는 함수를 정의할 때, optional paramter의 기본값(default value)을 설정해줄 수 있다. <br>
**하지만, parameter의 기본값으로 mutable object를 사용하는 것을 피해야만 한다.**


* mutable object를 default로 설정하는 것의 위험성
```python
class HauntedBus:
    """A bus model haunted by ghost passengers"""

    def __init__(self, passengers=[]):  # <1>
        self.passengers = passengers

    def pick(self, name):
        self.passengers.append(name)  # <2>

    def drop(self, name):
        self.passengers.remove(name)
```

<1>: passengers 인자값이 전달되지 않는다면, default 값인 empty list object에 bound된다. <br>
<2>: 해당 assignment로 self.passengers가 passengers의 alias가 된다. \_\_init\_\_ 메소드에서 passengers 인자값이 주어지지 않았다면, self.passengers도 마찬가지로 default 값인 empty list를 참조하게 된다.

---


```python
bus1 = HauntedBus(['Alice', 'Bill'])
bus1.passengers  # ['Alice', 'Bill']
bus1.pick('Charlie')
bus1.drop('Alice')
bus1.passengers  # ['Bill', 'Charlie']
bus2 = HauntedBus()  # bus2 starts empty, so the default empty list is assigned to self.passengers
bus2.pick('Carrie')
bus2.passengers  # ['Carrie']
bus3 = HauntedBus()
bus3.passengers  # ['Carrie']; The default is no longer empty
bus3.pick('Dave')
bus2.passengers  # ['Carrie', 'Dave']  # Dave who picked by bus3 appears in bus2
bus2.passengers is bus3.passengers  # True; The problem - both refer to the same list
bus1.passengers  # ['Bill', 'Charlie']; distinct
```

초기 passengers list 값을 전달받지 않은 Bus 인스턴스들은 default값인 empty list를 공유하게 된 것이 문제이다. <br> 
\_\_init\_\_ 메소드가 정의되었을 때(모듈이 로드될때)부터 default value가 계산되어서, \_\_init\_\_ 객체의 **\_\_defaults\_\_** attribute로 할당된다.

이와 같은 mutable deafults 이슈가 있기 때문에, 보통 **None**이 parameter의 기본값으로 사용된다. Parameter 값이 None인지 확인 후, 새로운 빈 list를 생성하여 할당하여 인스턴스 별로 각자의 list 객체를 참조하게 한다.

### Defensive programming with mutable parameters

Mutable parameter를 받는 함수를 작성할 때는, 해당 함수를 호출하는 쬭에서 전달하는 인자의 변경을 예상할 수 있는지 고려해야 한다.

예를 들어, 함수가 dict를 받아서 처리를 하는 과정에서 해당 dict의 변경이 발생한다면, 함수 밖에서 부작용(side-effect)가 보여야하는지는 상황에 따라 다를 것이다.

* TwilightBus class를 사용하는 caller인 client에서 예상하지 못한 결과 예제
```python
basketball_team = ['Sue', 'Tina', 'Maya', 'Diana', 'Pat']
bus = TwilightBus(basketball_team)
bus.drop('Tina')
bus.drop('Pat')
basketball_team  # ['Sue', 'Maya', 'Diana']
```

TwilightBus class로 생성한 객체에서 drop을 하니까, 전달한 mutable list인 basketball_team에도 변경이 발생하였다.

* 전달 인자를 수정하는 위험성을 보여주는 간단한 class
```python
class TwilightBus:
    """A bus model that makes passengers vanish"""

    def __init__(self, passengers=None):
        if passengers is None:
            self.passengers = []
        else:
            self.passengers = passengers  # <1>

    def pick(self, name):
        self.passengers.append(name)

    def drop(self, name):
        self.passengers.remove(name)  # <2>
```

<1> assignment로 self.passengers가 전달받은 실제 인자값(basketball_team)인 passengers의 alias가 된다.

<2> remove나 append와 같은 method를 호출하면, self.passengers가 assignment로 인해 원본 list를 참조하고 있어서 원본에 변경이 발생하게 된다.

* mutable parameter를 함수에서 안전하게 받기
```python
def __init__(self, passengers=None):
    if passengers is None:
        self.passengers = []
    else:
        self.passengers = list(passengers)  # <1>
```

<1> \_\_init\_\_ 에서 self.passengers는 전달받은 인자를 copy(list 생성자로)하여 고유의 list를 참조한다.

<1-1> list 생성자는 어떠한 iterable도 받을 수 있기 때문에, 이와 같은 방법은 tuple, set과 같은 인자가 넘어오더라도 유연하게 대처할 수 있다.