## Chapter 5. First-Class Functions

### Function Introspection
함수 객체는 **\_\_doc\_\_** 이외에도 많은 속성(attribute)을 가지고 있다.

* **dir** 함수 결과
```python
dir(factorial)  # ['\_\_annotations\_\_', '\_\_call\_\_', '\_\_class\_\_', '\_\_closure\_\_', '\_\_code\_\_' ...]
```

대부분의 속성은 파이썬 객체라면 가지고 있는 것들이다.

보통의 user-defined classd의 instance와 같이, 함수는 **\_\_dict\_\_** 속성을 사용하여 할당받은 user attributes을 저장한다.
이 방식은 원시적인(primitive) 형태의 annotation로서 유용하다.

Django framework에서는 **short_description** 를 메소드에 붙여서 관리자(admin)이 해당 메소드를 사용할 때 나타나도록 한다.


* 속성 추가하는 예제
```python
def upper_case_name(obj):
	return ("%s %s" % (obj.first_name, obj.last_name)).upper()
upper_case_name.short_description = 'Customer name'
```


---

일반적인 사용자 정의 (user-defined) 객체에는 없지만 함수에만 있는 특정 속성을 알아본다.

```python
class C: pass
obj = C()
def func(): pass
sorted(set(dir(func)) - set(dir(obj)))  # <1>
# ['\_\_annotations\_\_', '\_\_call\_\_', '\_\_class\_\_', '\_\_closure\_\_', '\_\_code\_\_', 
#  '\_\_defaults\_\_', '\_\_get\_\_', '\_\_globals\_\_', '\_\_kwdefaults\_\_', '\_\_name\_\_',
#  '\_\_qualname\_\_']
```

<1>: set difference를 이용하여, 함수에는 있지만 bare class의 instance에는 없는 attribute list 생성

함수만 가지고 있는 속성 중에서 **\_\_defaults\_\_**, **\_\_code\_\_**, 그리고 **\_\_annotations\_\_** 를 알아본다.

### From Positional to Keyword-Only Parameters

파이썬 함수의 가장 대표적인 특징 중 하나로서, 인자(parameter)를 다루는 방식이 상당히 유연하다는 점을 들 수 있다.
특히, 파이썬 3에서는 keyword-only 인자(arguments)를 사용하여 더욱 유연하게 인자를 다룰 수 있다.

함수를 호출할 때, \* 과 \*\* 을 사용하여 iterables와 mappings을 별도의 인자로 분리하는 'explode' 방식은 서로 밀접하게 관련이 있다.

* *keyword-only arguments* 인 cls는 파이썬에서 class가 예약된 키워드이기 때문에 사용
```python
def tag(name, *content, cls=None, **attrs):
	"""Generate one or more HTML tags"""
	if cls is not None:
		attrs['class'] = cls
	if attrs:
		attr_str = ''.join(' %s="%s"', % (attr, value)
							for attr, value
							in sorted(attrs.items()))
	else:
		attr_str = ''
	if content:
		return '\n'.join('<%s%s>%s</%s>' %
						  (name, attr_str, c, name) for c in content)
	else:
		return '<%s%s />' % (name, attr_str)
```

* *keyword-only arguments* 인 cls는 파이썬에서 class가 예약된 키워드이기 때문에 사용
```python
tag('br')  # '<br />' <1>
tag('p', 'hello')  # '<p>hello</p>'  <2>
print(tag('p', 'hello', 'world'))  # <p>hello</p> \n <p>world</p>
tag('p', 'hello', id=33)  # '<p id="33">hello</p>'  <3>
print(tag('p', 'hello', 'world', cls='sidebar'))  # <p class='sidebar'>hello</p> \n <p class='sidebar'>world</p>  <4>
tag(content='testing', name='img')  # '<img content="testing" />'  <5>
my_tag = {'name': 'img', 'title': 'Sunset Boulevard',
		  'src': 'sunset.jpg', 'cls': 'framed'}
tag(**my_tag)  # '<img class="framed" src="sunset.jpg" title="Sunset Boulevard" />'  <6>
```

<1>: 단일 positional argument만 전달하는 경우; 가장 마지막 else statement에 해당 <br>
<2>: 첫 번째 regular positional argument인 *name* 뒤에 오는 가변 갯수의 인자는 모두 *\*content* 가 tuple로 받는다. <br>
	*\*content* 는 **Variable Length Positional Arguments** <br>
<3>: *cls* 는 **Keyword-Only Arguments with Defaults**.
	함수 호출 시에, 해당 키워드를 명시적으로 사용하지 않아서, **\*\*attrs** 가 dict로 받는다.
	따라서 *if attrs:* statement의 결과가 나옴 <br>
	**\*\*attrs** 는 **Variable Length Keyword Arguments** <br>
<4>: *cls* parameter는 키워드를 사용해서만 전달 가능 (keyword-only argument) <br>
<5>: 첫 번째 positional argument인 *name*은 keyword로도 전달할 수 있다. <br>
	 *content='testing'* 은 **\*\*attrs** 로 capture 된다. <br>
<6>: \*\* 를 사용하여 dict 객체의 모든 아이템을 개별적인 arguments로 전달한다. <br>
	 named parameters(name, cls)를 제외한 나머지는 모두 **\*\*attrs** 에서 capture한다. <br>

---

**Keyword-only arguments** 는 파이썬 3의 새로운 기능이다.
위에서 *cls* parameter는 해당 키워드로 값이 주어질 때만 인자를 받을 수 있다.
(positional arguments로 자동으로 채워지지 않는다.)

* variable arguments (\*) 뒤에 keyword-only arguemnt
```python
def f(a, *, b):
	return a, b
f(1, b=2)  # (1, 2)
```

Keyword-only argument를 함수에 정의하려면, \*가 붙은 인자 뒤에서만 사용할 수 있다. (Variable Length Positional Arguments 뒤에)
위의 예제는 함수에서 arguments 종류별 정의의 일반적인 경우를 보여준다. 이는 다음과 같다.

1. **Known positional arguments** (Regular Positional Arguments)
2. **\*args** (Variable Length Positional Arguments)
3. **Known named arguments** (Keyword-Only Arguments)
4. **\*\*kwargs** (Variable Length Keyword Arguments)