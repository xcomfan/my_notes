
# Creating a Dictionary

```python
>>> bob1 = dict(name='Bob', job='dev', age=40)
>>> bob1
{'name': 'Bob', 'job': 'dev', 'age': 40}
>>> bob2 = dict(zip(['name','job','age'],['Bob','dev',40]))
>>> bob2
{'name': 'Bob', 'job': 'dev', 'age': 40}
>>> dict([('name','Bob'), ('age',40)])
{'name': 'Bob', 'age': 40}
```

# Avoiding error on missing key

## Using get method

```python
>>> D = dict(A=1, B=2, C=3)
>>> D
{'A': 1, 'B': 2, 'C': 3}
>>> # get returns None by default
>>> value = D.get('X')
>>> print(value)
None
>>> # you can use a default value with get
>>> D.get('X',0)
0
>>> # manual way to set default
>>> value = D['X'] if 'X' in D else 0
>>> value
0
```

## Using defaultdict from collections module

You can use a default dict and pass a function to the constructor for generating a default value.

```python
>>> d = defaultdict(gen_default_value)
>>> d["a"] = 1
>>> print(d)
defaultdict(<function gen_default_value at 0x7fa949dd9510>, {'a': 1})
>>> print(d["x"])
my default value
```

You an also use a lambda shorthand to avoid defining the default function

```python
>>> l_dct = defaultdict(lambda: 'my default value')
>>> l_dct['abc']
'my default value'
```

There are build in generators

```python
a_dct = defaultdict(int) # The default is 0
a_dct = defaultdict(float) # the default is 0.0
a_dct = defaultdict(str) # the default is ""
a_dct = defaultdict(list) # the default is []
a_dct = defaultdict(tuple) # the default is ()
a_dct = defaultdict(bool) # the default is False
a_dct = defaultdict(set) # the default is set()
a_dct = defaultdict(frozenset) # the default frozenset()
a_dct = defaultdict(dict) # the default is {}
```

# Dictionary Operations

```python
>>> DC = {'Batman':1, 'Superman':2, 'Wonder Woman':3}
>>> # Get dictionary values as a list
>>> list(DC.values())
[1, 2, 3]
>>> type(DC.values())
<class 'dict_values'>

>>> # Get all items in dictionary as a list
>>> list(DC.items())
[('Batman', 1), ('Superman', 2), ('Wonder Woman', 3)]
>>> type(DC.items())
<class 'dict_items'>

>>> Marvel = {'Hulk':1, 'Iron Man':2, 'Captain America':3}
>>> # Concatenate dictionaries
>>> DC.update(Marvel)
>>> DC
{'Batman': 1, 'Superman': 2, 'Wonder Woman': 3, 'Hulk': 1, 'Iron Man': 2, 'Captain America': 3}

>>> # Remove single item by key
>>> DC.pop('Wonder Woman')
3 # the value is returned when you pop
>>> DC
{'Batman': 1, 'Superman': 2, 'Hulk': 1, 'Iron Man': 2, 'Captain America': 3}
```

## Iterate over key values pairs of a dictionary

```python
>>> DC = {'Batman': 1, 'Superman': 2, 'Hulk': 1, 'Iron Man': 2, 'Captain America': 3}
>>> for key, value in DC.items():
...   print(f'key is {key} value is {value}')
...
key is Batman value is 1
key is Superman value is 2
key is Hulk value is 1
key is Iron Man value is 2
key is Captain America value is 3
```

# Merging Dictionaries

```python
>>> pycon = {2016: "Portland", 2018: "Cleveland"}
>>> europycon = {2017: "Rimini", 2018: "Edinburgh", 2019: "Basel"}
>>> merged = {**pycon, **europycon}
>>> merged # merged is a new dictionary with contents of both.
{2016: 'Portland', 2018: 'Edinburgh', 2017: 'Rimini', 2019: 'Basel'}
>>
>> pycon.update(europycon)
>>> pycon # updates pycon with content of europycon
{2016: 'Portland', 2018: 'Edinburgh', 2017: 'Rimini', 2019: 'Basel'}
>>> pycon = {2016: "Portland", 2018: "Cleveland"} # reset pycon to orig 
>>> # using the wallrus operator NOTE: watch where parenthesis are
>>> (merged := pycon.copy()).update(europycon)
>>> merged
{2016: 'Portland', 2018: 'Edinburgh', 2017: 'Rimini', 2019: 'Basel'}
>>> # using the new (from python 3.9 onward) pipe operator
>>> pycon | europycon
{2016: 'Portland', 2018: 'Edinburgh', 2017: 'Rimini', 2019: 'Basel'}
>>> # |= operator will do an update
>>> pycon |= europycon
>>> pycon
{2016: 'Portland', 2018: 'Edinburgh', 2017: 'Rimini', 2019: 'Basel'}
>>> # these new operators also work on dictionay like objects
>>> from collections import defaultdict
>>> europe = defaultdict(lambda: "", {"Norway": "Oslo", "Spain": "Madrid"})
>>> africa = defaultdict(lambda: "", {"Egypt": "Cairo", "Zimbabwe": "Harare"})
>>> # merging regular wasy creates a dict
>>> {**europe, **africa}
{'Norway': 'Oslo', 'Spain': 'Madrid', 'Egypt': 'Cairo', 'Zimbabwe': 'Harare'}
>>> # using pipe operator we maintain object type as default dict
>>> europe | africa
defaultdict(<function <lambda> at 0x100ef9ee0>, {'Norway': 'Oslo', 'Spain': 'Madrid', 'Egypt': 'Cairo', 'Zimbabwe': 'Harare'})
>>>
>>> # update a dictionay with types that go into a dictionay constructor such as a list of tupples.
>>> libraries = {"collections": "Container datatypes", "math": "Math functions"}
>>> libraries |= [("graphlib", "Topological sort tools")]
>>> libraries
{'collections': 'Container datatypes', 'math': 'Math functions', 'graphlib': 'Topological sort tools'}
>>> # order of operations is important. Second value wins on conflict. Note what happens with Georgia
>>> aisia = {"Georgia": "Tbilisi", "Japan": "Tokyo"}
>>> usa = {"Missouri": "Jefferson City", "Georgia": "Atlanta"}
>>> aisia = {"Georgia": "Tbilisi", "Japan": "Tokyo"}
>>> aisia | usa
{'Georgia': 'Atlanta', 'Japan': 'Tokyo', 'Missouri': 'Jefferson City'}
>>> usa | aisia
{'Missouri': 'Jefferson City', 'Georgia': 'Tbilisi', 'Japan': 'Tokyo'}
```

In above "dictionary like" means the following:

* `UserDict`
* `ChainMap`
* `OrderedDict`
* `defaultdict`
* `WeakKeyDictionary`
* `WeakValueDictionary`
* `_Environ`
* `MappingProxyType`

The `|` and `|=` dictionary support has not been added to the abstract classes `Mapping` and `MutalbeMapping`

