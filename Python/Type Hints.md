
#TODO This page needs an organization and content update pass.

# List of integers or None

```python
from typing import List, Union
def example(values: List[Union[int, None]]) -> None:
    pass
```

From python 3.9 onward you can use list directly as a type hint.

```python
radius: float = 3.9
radii: list[float] = [3.9, 2.4]
```

# Class init method that takes instance of same class as an argument.

```python
# This is for Python 3.10 for 3.11 you can just import Self from typing
from typing_extensions import Self
def example(value: Self = None) -> return Self
    pass
```

# Annotated Type Hints

This features is available from Python 3.9 onward.

You can provide annotations to arguments to give some extra context as in the example below.

```python
#speed_units.py
FPS_TO_MPH = 3600 / 5280

def speed(distance:"feet", time:"seconds") -> "miles per hour":
    return distance / time * FPS_TO_MPH
```

```python
>>> from speed_units import speed
>>> speed(10,2)
3.4090909090909087
>>> speed.__annotations__
{'distance': 'feet', 'time': 'seconds', 'return': 'miles per hour'}
```

You can also provide type hints which is the most common use case for type hints.

```python
#speed_types.py
FPS_TO_MPH = 3600 / 5280

def speed(distance:float, time:float) -> float:
    return distance / time * FPS_TO_MPH
```

Notice how the annotation output below is different that the example above.

```python
>>> from speed_types import speed
>>> speed(10, 2)
3.4090909090909087
>>> speed.__annotations__
{'distance': <class 'float'>, 'time': <class 'float'>, 'return': <class 'float'>}
```

You can use the `typing.Annotated` class to combine the two sets of information:

```python
#speed_units.py
from typing import Annotated

FPS_TO_MPH = 3600 / 5280

Feet = Annotated[float, "feet"]
MPH = Annotated[float, "miles per hour"]

def speed(distance:Feet, time:Annotated[float, "seconds"]) -> MPH:
    return distance / time * FPS_TO_MPH
```

```python
>>> from speed_both import speed
>>> speed(10, 2)
3.4090909090909087
>>> speed.__annotations__
{'distance': typing.Annotated[float, 'feet'], 'time': typing.Annotated[float, 'seconds'], 'return': typing.Annotated[float, 'miles per hour']}
>>> speed.__annotations__["distance"].__metadata__
('feet',)
>>> from typing import get_type_hints
>>> get_type_hints(speed)
{'distance': <class 'float'>, 'time': <class 'float'>, 'return': <class 'float'>}
>>> get_type_hints(speed, include_extras=True)
{'distance': typing.Annotated[float, 'feet'], 'time': typing.Annotated[float, 'seconds'], 'return': typing.Annotated[float, 'miles per hour']}
>>>
```


## Annotation Type Factory

If you are doing a log of annotating setting up a factory as in the example below may be a good idea.

```python
from typing import Annotated

class AnnotationFactory:
    def __init__(self, type_hint):
        self.type_hint = type_hint

    def __getitem__(self, key):
        if isinstance(key, tuple):
            return Annotated[(self.type_hint, ) + key]
        
        return Annotated[self.type_hint, key]
    
    def __repr__(self):
        return f"{self.__class__.__name__}({self.type_hint})"
    
FPS_TO_MPH = 3600 / 5280

Float = AnnotationFactory(float)

def speed(distance:Float["feet"], time:Float["seconds"]) -> Float["MPH"]:
    return distance / time * FPS_TO_MPH
```

