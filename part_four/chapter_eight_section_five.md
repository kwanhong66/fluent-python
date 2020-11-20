## Chapter 8. Object References, Mutability, and Recycling

### del and Garbage collection

**del** statement는 객체 자체가 아니라, 객체에 붙은 이름을 삭제한다.

객체는 del로 인하여 garbage collect 된다. 이는 객체가 참조 되지 않는 상태(unreachable)이거나, 객체를 참조하던 마지막 변수가 삭제되었을 때이다.

---

CPython의 garbage collection의 주요 알고리즘은 reference counting이다. 객체는 각자의 reference count를 유지하고 있으며, 이것이 0이 되면 객체는 즉시 제거된다. Python의 다른 구현에서는 조금 더 복잡한 garbage collector를 사용한다. 이 garbage collector는 reference couting만 사용하지 않는다.

* 객체가 reference point가 더 이상 없을때
```python
import weakref
s1 = {1, 2, 3}
s2 = s1
def bye():  # <1>
    print('Gone with the wind...')
ender = weakref.finalize(s1, bye)  # <2>
ender.alive  # True
del s1
ender.alive  # True  <3>
s2 = 'spam'  # Gone with the wind...  <4>
ender.alive  # False
```

<1>: 객체가 파괴되는것을 보기 위해, 이 함수를 객체의 메소드로 bound하지 않음 <br>
<2>: bye 함수를 s1이 참조하는 객체에 콜백(callback)으로 등록 <br>
<3>: del은 객체의 참조만 삭제 <br>
<4>: {1, 2, 3} 객체에 마지막 참조였던 s2를 다른 객체에 rebind. {1, 2, 3} 객체는 unreachable이 되어서 삭제된다.


### Weak References

참조(reference)가 존재하고 있다면, 객체는 메모리에 살아있게 된다. 객체의 참조 갯수(reference count)가 0이 되면 garbage collector가 해당 객체를 처리한다.

Weak reference는 객체의 참조 갯수(reference count)를 증가시키지 않는다. 그러므로, weak reference는 객체가 garbage collect 되는 것을 막지 못한다. 캐시(cache)와 같이 더 이상 필요하지 않은데, 살아있지 않고 제거되길 원하는 경우에 유용하다.


* weak reference는 callable이고 referent가 있다면 참조된 객체 반환
```python
import weakref
a_set = {0, 1}
wref = weakref.ref(a_set)
wref  # <weakref at 0x100637598; to 'set' at 0x100636748>
wref()  # {0, 1}  <1>
a_set = {2, 3, 4}  # <2>
wref()  # {0, 1}  <3>
wref() is None  # False  <4>
wref() is None  # True
```

<1>: Console session이라서 wref() 호출 결과는 _ 변수에 bind. <br>
<2>: a_set는 더 이상 {0, 1}를 참조하지 않아서, 참조 갯수는 감소한다. _ 변수는 여전히 해당 객체를 참조한다. <br>
<3>: wref()를 호출하면 여전히 {0, 1}을 반환한다. <br>
<4>: Expression이 계산(evalutate)되었을 때, {0, 1}은 살아 있었지만 _ 변수가 결과값(False)에 bind되어서 (console session) {0, 1}에 대한 strong reference는 더 이상 없다.

---
**weakref.ref** class는 저수준(low-level) 인터페이스이기 때문에, 직접 이것을 사용하는 것보다 **WeakKeyDictionary**, **WeakValueDictionary**, **WeakSet** 그리고 **finalize**를 사용하는것이 권장된다.

### The WeakValueDictionary Skit

* 종류 별 치즈를 표현하기 위한 단순한 class 구현
```python
class Cheese:

    def __init__(self, kind):
        self.kind = kind

    def __repr__(self):
        return 'Cheese(%r)' % self.kind
```


* weak reference는 callable, 참조된 객체가 있다면 해당 객체 반환
```python
import weakref
stock = weakref.WeakValueDictionary()
catalog = [Cheese('Red Leicester'), Cheese('Tilsit'),
            Cheese('Brie'), Cheese('Parmesan')]
for cheese in catalog:
    stock[cheese.kind] = cheese
sorted(stock.keys())  # ['Brie', 'Parmesan', 'Red Leicester', ' Tilsit']
del catalog
sorted(stock.keys())  # ['Parmesan']  <1>
del cheese
sorted(stock.keys())  # []
```

<1>: catalog list를 del 하였는데, 'Parmesan' cheese 객체만 남아있다.

임시 변수(temporary variable)의 참조가 어떤 객체가 예상과 다르게 더 살아있게 만드는 경우도 있다. 지역 변수의 경우 함수가 return을 하고나면 제거되기 때문에 문제가 되지 않는다. 

위의 예제에서는 **for** loop를 돌면서 **cheese** 변수는 global이기 때문에, list의 마지막 cheese 객체를 아직 참조하고 있다.


### Limitations of Weak References

Python의 모든 객체가 target 또는 referent의 weak reference가 될 수 있는 것은 아니다. List와 dict와 같은 인스턴스는 inferent가 될 수 없고, 각각의 subclass를 사용해야 한다.

```python
class MyList(list):
    """list subclass whose instances may be weakly referenced"""

a_list = MyList(range(10))
# a_list can be the target of a weak reference
wref_to_a_list = weakref.ref(a_list)
```