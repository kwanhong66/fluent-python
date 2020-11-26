## Chapter 5. First-Class Functions

### Retrieving Information About Parameters

Bobo HTTP 마이크로 프레임워크 예제를 통해 function introspection하는 방식을 알아본다.

* Bobo 프에임워크는 hello 메소드가 person 인자를 요구한다는 것을 알고, HTTP 요청에서 이를 받는다.
```python
import bobo

@bobo.query('/')
def hello(person):
    return 'Hello %s!' % person
```

**bobo.query** 데코레이터(decorator)는 **hello** 와 같은 일반 함수를 프레임워크의 동작 방식으로 통합시킨다. 데코레이터는 이후 챕터에서 다룬다.
요점은 Bobo가 **hello** 함수를 introspect하여 이 함수가 person이라는 이름의 파라미터가 필요하고, HTTP 요청으로부터 해당 이름으로 값을 받아 **hello** 에게 넘겨준다는 점이다.

Bobo 프레임워크는 어떻게 **hello** 함수가 어떤 파라미터 이름을 요구하고, 그 파라미터가 기본값을 가지는지 무엇을 보고 알 수 있을까?

---

함수 객체에서 다음과 같은 속성(attribute)으로 함수의 메타 정보를 알 수 있다.

* **\_\_defaults\_\_**: positional 과 keyword arguments의 기본값 정보 tuple을 가지고 있음
* **\_\_kwdefaults\_\_**: keyword-only arguments의 기본값 정보
* **\_\_code\_\_**: 함수의 메타데이터와 바이트코드로 컴파일된 함수 body 정보; arguments의 이름 정보

* 원하는 길이 근처의 공백에서 clipping 하는 함수를 정의한 *clip.py*
```python
def clip(text, max_len=80):
    """Return text clipped at the last space before or after max_len"""
    end = None
    if len(text) > max_len:
        space_before = text.rfind(' ', 0, max_len)
        if space_before >= 0:
            end = space_before
        else:
            space_after = text.rfind(' ', max_len)
            if space_after >= 0:
                end = space_after
    if end is None:  # no spaces were found
        end = len(text)
    return text[:end].strip()
```


* 함수 인자의 정보를 추출
```python
from clip import clip
clip.__defaults__  # (80,)  <1>
clip.__code__  # <code object clip at 0x...>
clip.__code__.co_varnames  # ('text', 'max_len', 'end', 'space_before', 'space_after')  <2>
clip.__code__.co_argcount  # 2  <3>
```

위의 예제에서 알 수 있듯이, 위와 같은 방법은 함수의 정보가 파악하기 어렵게 정리된다.

<1>: **\_\_defaults\_\_** 의 tuple에서는 기본값이 위치로만 확인된다. 대응하는 인자와의 연결을 확인하기 위해서는 뒤에서부터 scan이 필요 <br>
<2>: 인자 이름 뿐만 아니라, 함수 body의 지역 변수(local variable)까지 포함된다. <br>
<3>: **\_\_code\_\_.co_varnames** 에서 처음 N번째 값들이 인자(argument) 이름이다. N 값은 **\_\_code\_\_.co_argcount** 으로 알 수 있다.

위와 같이 단순히 attribute를 접근해서 함수의 정보를 살펴보는 것은 비효율적; 
**inspect** 모듈을 사용하는 것이 더 나은 방법이다.

* function signature 추출 예제
```python
from clip import clip
from inspect import signature
sig = signature(clip)
sig  # <inspect.Signature object at 0x...>
str(sig)  # '(text, max_len=80)'
for name, param in sig.parameters.items():
    print(param.kind, ':', name, '=', param.default)
# POSITIONAL_OR_KEYWORD : text = <class 'inspect._empty'>
# POSITIONAL_OR_KEYWORD : max_len = 80
```

**inspect.signature** 은 **inspect.Signature** 객체를 반환한다. 해당 객체의 **parameters** 속성에서 **inspect.Parameters** 객체와 이름의 순서를 가진 매핑을 알 수 있다.

* 
```python
import inspect
sig = inspect.signature(tag)
my_tag = {'name': 'img', 'title': 'Sunset Boulevard', 
          'src': 'sunset.jpg', 'cls': 'framed'}
bound_args = sig.bind(**my_tag)
bound_args  # <inspect.BoundArguments object at 0x...>
for name, value in bound_args.arguments.items():
    print(name, '=', value)
# name = img
# cls = framed
# attrs = {'title': 'Sunset Boulevard', 'src': 'sunset.jpg'}
del my_tag['name']
bound_args = sig.bind(**my_tag)  # TypeError: 'name' parameter lacking default value
```

**inspect.Signature** 객체의 **bind** 메소드는 임의의 갯수의 인자를 받아, 함수의 signature에 있는 parameters와 binding을 해준다. Binding은 기존의 함수 인자 매칭 규칙을 따른다.