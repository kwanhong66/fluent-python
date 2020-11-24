## Chapter 5. First-Class Functions

### Anonymous Functions

**lambda** 키워드는 파이썬 expression 안에서 익명 함수(anonymous function)를 만든다.

**lambda** 의 body에서는 오로지 expression만 가능하고, assignment 또는 while, try와 같은 statement는 불가능하다.


* lambda를 이용하여 reversed spelling 기준으로 정렬
```python
friuts = ['strawberry', 'fig', 'apple', 'cherry', 'raspberry', 'banana']
sorted(friuts, key=lambda word: word[::-1])
['banana', 'apple', 'fig', 'raspberry', 'strawberry', 'cherry']
```

익명 함수는 higher-order functiondml 인자(argument)로 사용되는 경우를 제외한 다른 경우에서는 거의 사용되지 않는다.
문법적인 제약 때문에 lambda가 읽기 어렵거나 동작하지 않는 중대한 문제가 발생한다.

lambda는 def statement와 같이 함수 객체를 만들어주는 'syntactic sugar' 일 뿐이다.