
# Basic Example

Because a decorator can return any sort of object this allows the decorator to insert a layer of logic to be run on every call. The decorator function is free to return either the original function or a new proxy object that saves the original function passed to the decorator to be invoked indirectly after the extra logic layer runs.

```python
>>> class tracer:
...   def __init__(self, func):
...     self.calls = 0
...     self.func = func
...   def __call__(self, *args):
...     self.calls += 1
...     print(f"call {self.calls} to {self.func.__name__}")
...     return self.func(*args)
...
>>> @tracer
... def spam(a,b,c):
...   return a + b + c
...
>>> print(spam('a', 'b', 'c'))
call 1 to spam
abc
>>> print(spam('a', 'b', 'c'))
call 2 to spam
abc
>>> print(spam('a', 'b', 'c'))
call 3 to spam
abc
```

# Decorators with unnamed callables

This is allowed from Python 3.9 forward.

```python
import functools

def normal(func):
    return func

def shout(func):
    @functools.wraps(func)
    def shout_decorator(*args, **kwargs):
        return func(*args, **kwargs).upper()
    
    return shout_decorator

DECORATORS = {
    "normal": normal,
    "shout": shout
}

@DECORATORS["normal"]
def first():
    return "It was the best of times"

@DECORATORS["shout"]
def second():
    return "it was the worst of times"

if __name__ == "__main__":
    print(first())
    print(second())
```

It produces the below output

```text
It was the best of times
IT WAS THE WORST OF TIMES
```

#TODO: Need to do a write up and link to functools and explain how we are using it here.