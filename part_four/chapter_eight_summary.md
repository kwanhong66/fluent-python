## Chapter 8. Object References, Mutability, and Recycling

### Chapter summary

두 개의 변수가 immutable 객체를 참조한다면(a == b is True), 실제로는 immutable 객체의 값은 변하지 않기 때문에 문제가 될 일이 없다. 
단 한 가지의 예외는 tuple이나 frozensets과 같은 immutable collections이 mutable items의 참조를 가지고 있는 경우에 발생한다. 하지만, 이런 시나리오는 발생하지 않는 것이 일반적이다.

Python에서 변수가 참조를 가져서 발생하는 실제적인 결과들이 많다.

* 단순 할당 (Simple assignment)은 복제본을 생성하지 않는다.
* += 또는 \*= 와 같은 증가 할당 (Augmented assignment)은 좌측변수가 immutable 객체를 참조하고 있으면 새로운 객체를 생성하고, mutalbe 객체를 참조하고 있으면 해당 객체에서 값을 수정한다.
* 기존에 있던 변수에 새로운 값을 할당하는 것은 변수가 참조하고 있던 객체를 바꾸는 것이 아니다. 해당 변수는 다른 객체에 bind 되는 것이고, 이를 rebinding이라고 한다.
* 함수의 파라미터는 aliases를 전달된다. 이와 같은 방식에서는 함수 내부에서 전달받은 mutable 객체를 변경하게 될 수 있다. 이것을 막을 방법은 immutable 객체를 사용하거나 내부에서 지역적으로 복제본을 만드는 방법 뿐이다.
* Mutable 객체를 함수의 파라미터의 기본값으로 사용하는 것은 위험하다.