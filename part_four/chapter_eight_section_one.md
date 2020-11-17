## Chapter 8. Object References, Mutability, and Recycling

### Variables are not boxes

대개 변수를 **값을 담는 상자("variables as boxes")** 에 비유하는데, 이 비유로 인해 객체지향(Object-Oriented) 언어의 reference variables(참조 변수)를 이해하기 어려워진다. 파이썬의 변수는 객체에 붙는 **레이블(label)** 로 생각하는 것이 더 낫다. 객체에 붙는 *sticky_note*라고 생각하기.

변수 a와 b는 동일한 list에 대한 reference를 가지고 있다.

* [1, 2, 3]이라는 list에 a와 b라는 label이 붙음

```python
a = [1, 2, 3]
b = a
a.append(4)
b
```

---

"Variable *s* is assigned to the seesaw"
참조 변수는 변수가 객체에 할당(assign)되었다고 하는 것이 더 정확한 표현 (그 반대는 맞지 않음) <br>
모든 객체는 할당(assignment)전에 생성(create; instantiate)된다.


> 파이썬의 할당(assignemnt)은 '=' 기준으로 항상 오른편부터 읽어야 이해가 된다. 오른편에서는 객체를 생성하거나 가져온다(retrieve). 그 다음에서야, 왼편의 변수가 레이블이 달라붙는 것처럼 객체에 묶인다(bind). *Boxes는 잊어버리자*