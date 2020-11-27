## Chapter 5. First-Class Functions

### Function Annotations

파이썬 3에서는 함수의 파라미터와 반환값에 메타데이터를 붙일 수 있는 문법을 제공한다.

* Annotated *clip* function
```python
def clip(text: str, max_len: 'int > 0'=80) -> str:
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

함수 선언에서 각 인자는 ':'이 선행하여 annotation expression을 작성할 수 있다.
만약 인자가 기본값을 가진다면, annotation expression은 인자 이름과 기본값 사이에 들어간다.
함수의 반환값에 annotation을 붙이려면, 함수 선언의 ')'와 ':' 사이에 '->'를 선행한 expression을 작성한다.

Annotation은 어떤 처리 없이 단순히 함수의 **\_\_annotation\_\_** 속성에 저장된다.
다시 말해서, annotation은 파이썬 인터프리터(interpreter)에서 어떠한 처리도 하지 않고, IDE, framework, decorators와 같은 tool에서 활용할 수 있는 메타데이터일 뿐이다.

* \_\_annotations\_\_ attribute
```python
from clip_annot import clip
clip.__annotations__
# {'text': <class 'str'>, 'max_len': 'int > 0', 'return': <class 'str'>}
```

* Extracting annotations from the function signature
```python
from clip_annot import clip
from inspect import signature
sig = signature(clip)
sig.return_annotation  # <class 'str'>
for param in sig.parameters.values():
	note = repr(param.annotation).ljust(13)
	print(note, ':', param.name, '=', param.default)
# <class 'str'> : text = <class 'inspect._empty'>
# 'int > 0'.    : max_len = 80
```