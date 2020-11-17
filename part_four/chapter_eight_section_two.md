## Chapter 8. Object References, Mutability, and Recycling

### Identity, Equality, and Aliases


charles와 lewis는 동일한 객체를 참조(refer)한다.
*lewis* 와 *charles* 는 **aliases** 이다.

```python
charles = {'name': 'Charles L. Dodgson', 'born': 1832}
lewis = charles  # lewis is an alias for charles
lewis is charles  # True; 두 개의 변수명은 동일한 객체를 참조하고 있음 (same identity)
id(charles), id(lewis)  # (4300473992, 4300473992), 동일한 객체 id 확인
lewis['balance'] = 950  # Adding an item to lewis is the same as adding an item to charles
```


동일한 데이터 값을 가지는 **alex** label의 객체를 생성해보자.
*alex* 는 *charles* 와 동등(equal)하지만, *alex is not charles*

```python
alex = {'name': 'Charles L. Dodgson', 'born': 1832, 'balance': 950}  # alex refers to an object that is a replica of the object assigned to charles
alex == charles  # 두 객체의 동등성(equality)을 확인한다. dict 클래스의 정의된 __eq__ 메소드를 사용.
alex is not charles  # True, 두 객체는 '동등(equal)'하지만, 개별(distinct) 객체이므로 동일성(identity)을 확인하였을 때, 동일하지 않음을 확인할 수 있다. (different object id)
```


*The Python Language References - 3.1. Objects, values and types* (https://docs.python.org/3/reference/datamodel.html#objects-values-and-types)

> Every object has an identity, a type and a value. An object’s identity never changes once it has been created; you may think of it as the object’s address in memory. The ‘is’ operator compares the identity of two objects; the id() function returns an integer representing its identity.

객체의 고유성 또는 정체성을 나타내는 *id* 는 객체가 생성될 시 할당 받아서, 객체의 생애주기 동안 유지된다.

<br>

### Choosing Between == and is

**==** operator는 객체가 들고 있는 데이터의 값을 비교하는 반면, **is** operator는 객체의 정체성을 비교한다.


**is** operator은 두 객체의 integer id만을 비교하는 것이기 때문에, special methods를 찾아서 호출할 일이 없다. 반면, **a == b**는 a.\_\_eq__(b)를 의미하는 문법이다. **object**로부터 상속받은 **\_\_eq\_\_** method는 마찬가지로 객체 id를 비교하지만, 각 built-in 타입 객체들은 각자의 타입에 맞는 값 동등성 비교를 위하여 **\_\_eq\_\_** method를 오버라이드(override)한다.

<br>

### The Relative Immutability of Tuples

**tuple** 은 *immutable* 한 collection이지만, tuple이 참조하고 있는 객체가 mutable이면 해당 객체의 값은 변경될 수 있다. 다시 말해, tuple의 *immutablity* 는 tuple의 data structure 자체에만 해당되고, 들고 있는 객체에는 해당하지 않는다.


```python
t1 = (1, 2, [30, 40])  # t1 is immutable, but t1[-1] is mutable (list)
t2 = (1, 2, [30, 40])
t1 == t2  # True, t1 and t2 are equal
id(t1[-1])  # 4302515784
t1[-1].append(99)  # Modify the t1[-1] list in place
t1  # (1, 2, [30, 40, 99])
id(t1[-1])  # 4302515784; the identity of t1[-1] has not changed, only its value
t1 == t2  # False
```