## Chapter 8. Object References, Mutability, and Recycling

### Copies are shallow by default

Python에서 어떤 객체의 복사본(copy)이 필요할 때, 단순히 assignment('=')을 사용하면 객체에 변수 이름으로도 된 label을 bind 하는것 뿐이다. 동일한 객체를 참조하는 변수가 여러개(alias)가 될 뿐, 별도의 '복사본' 객체를 생성한 것이 아니다. 다루는 객체가 immutable이라면 차이가 무의미하지만, mutable이라면 상황에 맞게 복사를 다르게 수행해야 한다.

list와 같은 built-in mutable collection은 해당 타입의 생성자(constructor)를 사용하여 복사하는것이 가장 쉬운 방법이다. <br>
(list의 경우 **[:]** 사용하여 전체 복사 가능)

* list: list()
* dict: dict()

```python
l1 = [3, [55, 44], (7, 8, 9)]
l2 = list(l1)  # list(l1) creates copy of l1
l2  # [3, [55, 44], (7, 8, 9)]
l2 == l1  # The copies are equal; same value
l2 is l1  # False; refer to two different objects
```

위의 코드에서 list 생성자로 별도의 객체를 생성하여 복사하였지만, list container 자체만 별도 생성되었을 뿐 list가 들고 있던 아이템들은 동일한 객체를 참조하고 있다. (shallow copy)

---

생성자나 [:]를 사용하는 것은 *shallow copy* 를 수행하게 된다. 이는 동일한 객체를 참조하는 것이기 때문에 메모리를 절약하게 되고, 복사하는 아이템이 immutable이면 문제가 없다.

```python
l1 = [3, [66, 55, 44], (7, 8, 9)]
l2 = list(l1)  # l2 is a shallow copy of l1
l1.append(100)  # Appending 100 to l1 has no effect on l2
l1[1].remove(55)  # Removing 55 from inner list l1[1] affects l2 because l2[1] is bound to the same list as l1[1]
print('l1:', l1)
print('l2:', l2)
l2[1] += [33, 22]  # for a mutable objec like list referred by l2[1], the operator += changes the list in place
l2[2] += (10, 11)  # += on a tuple creates a new tuple and rebinds the variable l2[2]. The tuples of the two lists are no longer the same object
print('l1:', l1)
print('l2:', l2)
```


```shell
l1: [3, [66, 55, 44], (7, 8, 9), 100]
l2: [3, [66, 44], (7, 8, 9)]
l1: [3, [66, 44, 33, 22], (7, 8, 9), 100]
l2: [3, [66, 44, 33, 22], (7, 8, 9, 10, 11)]
```

<br>

### Deep and shallow copies of arbitrary objects

**copy** 모듈은 **deepcopy** 와 **copy** 와 같은 임의의 객체의 깊은 복사 및 얕은 복사를 하는 함수를 제공한다.

<임의의 객체의 Bus class>
```python
class Bus:

    def __init__(self, passengers=None):
        if passengers is None:
            self.passengers = []
        else:
            self.passengers = list(passengers)

    def pick(self, name):
        self.passengers.append(name)

    def drop(self, name):
        self.passengers.remove(name)
```


위의 Bus class로 bus 객체(bus1)를 만들고 두 가지 복제를 만든다. 
두 가지 복제본은 shallow copy (bus2)와 deep copy (bus3)이다.

```python
import copy
bus1 = Bus(['Alice', 'Bill', 'Claire', 'David'])
bus2 = copy.copy(bus1)
bus3 = copy.deepcopy(bus1)
id(bus1), id(bus2), id(bus3)  # (4301498296, 4301499416, 4301499752); create three distinc Bus instances
bus1.drop('Bill')
bus2.passengers  # ['Alice', 'Claire', 'David']
id(bus1.passengers), id(bus2.passengers), id(bus3.passengers)  # (4302658568, 4302658568, 4302657800); bus1 and bus2 share the same list object (shallow copy)
bus3.passengers  # bus3 is a deep copy of bus1
```

deepcopy를 사용하는 경우, 불필요한 객체까지 복사가 되는 경우가 있다. 이 경우에는 \_\_deepcopy\_\_() special method를 상황에 맞게 구현하여 사용하는 것이 좋다.