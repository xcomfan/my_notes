
[YAML](YAML.md) parsing is not one of Python's included libraries. There are many community libraries the most popular of which is `PyYAML` based on the YAML 1.1 spec.

You can install `PyYAML` via pip `python -m pip install pyyaml`

# Parsing a YAML document using PyYAML

This is a [basic example](https://github.com/xcomfan/real_python_examples/blob/230acf4693b301649d7a091c13bd296ec1e46efb/working_with_yaml/sweetpotato.py) of using PyYAML to parse a sample [YAML file](https://github.com/xcomfan/real_python_examples/blob/230acf4693b301649d7a091c13bd296ec1e46efb/working_with_yaml/structure.yml).

```python
# sweetpotato.py
import yaml

from pprint import pprint

def show_spud(filename):
    """
    Using rb (read binary) mode as YAML can use UTF encodings which Python
    may not be able to handle
    """
    with open(filename, mode='rb') as file:
        result = yaml.safe_load(file)
        pprint(result, indent=2, sort_dicts=False)
```

```yml
# structure.yml
grandparent:
  parent:
    child:
      name: Bobby
    sibling:
      name: Molly
```

Below is an example of executing this code.

```python
>>> from sweetpotato import show_spud
>>> show_spud("structure.yml")
{ 'grandparent': { 'parent': { 'child': {'name': 'Bobby'},
                               'sibling': {'name': 'Molly'}}}}
>>>
```

# YAML Data Structures

## Strings

Strings can be:

* Unquoted - An unquoted string is considered a literal. If you put a `\n` in it, it will be escaped in Python. You won't get a newline, you will get an `\\n`.

* Single quotes - Is almost a literal. A double single quote is considered as quote.

* Double quotes - Is interpreted, does not process the double-single-quote. A `\n` actually means newline.

### Strings and Special Values

`ture`, `false`, and `null` are special values in YAML. Because YAML allows unquotes strings you get some weirdness here. `True` on its own is not a string but `True that` however would be treated as a string.

To elaborate on this lets use the yaml file below and run it though our show spud code.

```yml
# strings.yml
unquoted: Don't\nYou know?
single: 'Don''t\nYou know?'
double: "Don''t\nYou know?"
special: True
morespecial: True that
```

```python
>>> from sweetpotato import show_spud
>>> show_spud('strings.yml')
{ 'unquoted': "Don't\\nYou know?",
  'single': "Don't\\nYou know?",
  'double': "Don''t\nYou know?",
  'special': True,
  'morespecial': 'True that'}
* ```

# Scalars

`*` indicates YAML 1.2 only.

| Data Type | YAML                               | YAML <= 1.1              |
| --------- | ---------------------------------- | ------------------------ |
| Null      | `null`, `~`, *blank*               |                          |
| Boolean   | `true` `false`                     | `yes`, `no`, `on`, `off` |
| Integer   | `10` `0b10` `0x10` `0o10`*         | `010`                    |
| Float     | `3.14` `12.5e-9` `.inf` `.nan`     |                          |
| String    | `Lorem` `ipsum`                    |                          |
| Date      | `1945-07-16` `1945-07-16 05:29:00` |                          |
***Note:*** Reserved works can be lower, upper, or mixed case: `null`, `Null`, `ture`, `TRUE`, `True` etc.

To elaborate this if we run the [below yaml file](https://github.com/xcomfan/real_python_examples/blob/6264c55aeda34572af2ba72f340ce22a644c8cbd/working_with_yaml/datatypes.yml) though [our print script](https://github.com/xcomfan/real_python_examples/blob/230acf4693b301649d7a091c13bd296ec1e46efb/working_with_yaml/sweetpotato.py)

```yml
empty1: Null
empty2: null
empty3: ~
empty4:
boolean1: True
boolean2: false
boolean3: yes
ten: 10
bin: 0b10
hel: 0x10
oct: 010
not-oct: 0o10
pi: 3.14
exp: 12.5e-9
inf: .inf
nan: .nan
trinity: 1945-07-16 05:29:00
not-trinity: 1945-07-16 05:29
```

we get the below result. Notice the following.

* When we have the hours and minutes in `non-trinity` we wind up with a string.

```python
>>> from sweetpotato import show_spud
>>> show_spud("datatypes.yml")
{ 'empty1': None,
  'empty2': None,
  'empty3': None,
  'empty4': None,
  'boolean1': True,
  'boolean2': False,
  'boolean3': True,
  'ten': 10,
  'bin': 2,
  'hel': 16,
  'oct': 8,
  'not-oct': '0o10',
  'pi': 3.14,
  'exp': 1.25e-08,
  'inf': inf,
  'nan': nan,
  'trinity': datetime.datetime(1945, 7, 16, 5, 29),
  'not-trinity': '1945-07-16 05:29'}
>>>
```

# Arrays

Sequences are denoted by square brackets or leading dashes as in the example below. ***Note:*** when using the leading dashes space before the dashes is optional.

```yml
fruits: [apple, banana, orange]
veggies:
  - tomato
  - cucumber
  - onion
mushrooms:
- champignon
- truffle
```

# Hashes

Hashes can be implicit based on key/value pairs or explicit through the inline feature.

```yml
person:
  firstName: John
  lastName: Doe
  dateOfBirth: 1969-12-31
  married: true
  spouse:
    firstName: Jane
    lastName: Smith
  children:
    - fistName: Bobby
      dateOfBirth: 1995-01-17
    - firstName: Molly
      dateOfBirth: 2001-05-14
```

Notes on the example above:

* `children` is an Anonymous hash. Its a list of dicts but both dicts are anonymous with name and data of birth inside them.

# Ambiguity

Interpretation of data types isn't always obvious to a human.

YAML 1.1 supports base 60 using colon delimited notation so you would get something like:

| Content    | Result                                  |
| ---------- | --------------------------------------- |
| `22:22`    | 1342 = 22 x 60 + 22                     |
| `05:23`    | String                                  |
| `5:36`     | 323 = 5 x 60 + 23                       |
| `5:23:00`  | 19380, number of seconds since midnight |
| `05:23:00` | String                                  |

You can force a data type using YAML tags.  A tag is denoted by `!! tag`. Here are some examples:

| YAML                       | Python                        |
| -------------------------- | ----------------------------- |
| `!!float 3`                | 3.0                           |
| `!!str 22:22`              | "22:22"                       |
| `!!binary R01GODhCAAIAPA=` | b"GIF87a\x08\x00\x08\x00\xf0" |

# Advanced Syntax

## Anchors

Using these features will make your YAML code less readable. This is however used in dev ops as that is where you end up with a lot of repetitive YAML.

YAML supports **anchors** which is the declaration and re-use of pieces of YAML.  Anchors are denoted with an ampersand (`&`). An **alias** de-references an anchor and is denoted with an asterisk (`*`). 

For example the [following yaml file](https://github.com/xcomfan/real_python_examples/blob/b0613b1690e99858a35e597b0659445991642a7b/working_with_yaml/anchors.yml) yields the below output when you run it though our [print code](https://github.com/xcomfan/real_python_examples/blob/b0613b1690e99858a35e597b0659445991642a7b/working_with_yaml/sweetpotato.py).

```yml
exercises:
  - muscles: &push-up
      - pectoral
      - triceps
      - biceps
  - muscles: &squat
      - glutes
      - quadriceps
      - hamstrings
  - muscles: &plank
      - abs
      - core
      - shoulders

schedule:
  monday:
    - *push-up
    - *squat
  tuesday:
    - *plank
  wednesday:
    - *push-up
    - *plank
```

```python
>>> from sweetpotato import show_spud
>>> show_spud("anchors.yml")
{ 'exercises': [ {'muscles': ['pectoral', 'triceps', 'biceps']},
                 {'muscles': ['glutes', 'quadriceps', 'hamstrings']},
                 {'muscles': ['abs', 'core', 'shoulders']}],
  'schedule': { 'monday': [ ['pectoral', 'triceps', 'biceps'],
                            ['glutes', 'quadriceps', 'hamstrings']],
                'tuesday': [['abs', 'core', 'shoulders']],
                'wednesday': [ ['pectoral', 'triceps', 'biceps'],
                               ['abs', 'core', 'shoulders']]}}```

You can use anchors recursively.  A node can declare an anchor and contain an alias to the same anchor. In theory this allows for arbitrary directed graphs; in practice this can blow up your parser.

Anchor values can be **merged** into other anchor values using the `<<` syntax. Aliases provide re-use as a value, while merges provide re-use of attributes. Here is [an example](https://github.com/xcomfan/real_python_examples/blob/54335a3e22cb9e030d53e0ff3276231504b97083/working_with_yaml/merges.yml).

```yml
location: &location
  address: 742 Evergreen Terrace
  city: Springfield

person: &person
  first-name: Bart
  last-name: Simpson

student:
  name: *person
  <<: *location
```

```python
>>> 
>>> from sweetpotato import show_spud
>>> show_spud("merges.yml")
{ 'location': {'address': '742 Evergreen Terrace', 'city': 'Springfield'},
  'person': {'first-name': 'Bart', 'last-name': 'Simpson'},
  'student': { 'address': '742 Evergreen Terrace',
               'city': 'Springfield',
               'name': {'first-name': 'Bart', 'last-name': 'Simpson'}}}
>>>
```

Notice that there is no location dict in `student`; instead the two attributes from location get merged into student. 

## Blocks

* YAML Supports multi line text.
* Indentation indicates continuation
* Blank line indicates new-line
* Use the pipe (`|`) operator to conserve new-lines in a multi-line block.
	* Common for embedding scripts in YAML (for Ansible, Docker, etc.)
* Use the greater than (`>`) operator to have first line indentation indicate where newlines belong.

An [example](https://github.com/xcomfan/real_python_examples/blob/1cdabc79d4456b5b8df964b20fd5116c57c8703f/working_with_yaml/multiline.yml) to elaborate

```yml
multi-line: This block of text
  will all be on one line

  The blank line is what causes a new-line in this case

script: |
  #!/usr/bin/env python

  def main():
    print("Hello world")

  if __name__ == "__main__":
    main()

folded: >
  The line of of text
    the second line of text

  A third
    line of text

  A forth
    and fifth line
```

And running it through our [printer code](https://github.com/xcomfan/real_python_examples/blob/1cdabc79d4456b5b8df964b20fd5116c57c8703f/working_with_yaml/sweetpotato.py) we get:

```python
>>> from sweetpotato import show_spud
>>> show_spud("multiline.yml")
{ 'multi-line': 'This block of text will all be on one line\n'
                'The blank line is what causes a new-line in this case',
  'script': '#!/usr/bin/env python\n'
            '\n'
            'def main():\n'
            '  print("Hello world")\n'
            '\n'
            'if __name__ == "__main__":\n'
            '  main()\n',
  'folded': 'The line of of text\n'
            '  the second line of text\n'
            '\n'
            'A third\n'
            '  line of text\n'
            '\n'
            'A forth\n'
            '  and fifth line'}
>>>```

## Multi-Doc Docs

* YAML supports nesting of documents in a single file
* Docs are separated by triple-dash (`---`)
* Can optionally mark the end of a file using triple dots (`...`)
* Use PyYAML's `safe_load_all()` which returns a document iterator to handle this sort of scenario.

The below [example](https://github.com/xcomfan/real_python_examples/blob/011337e7cd5423f2da70f327ad850575d5a5c6dc/working_with_yaml/multi_doc.yml) show how that works. Note that we cannot use the same print code for this example so we use the iterator via REPL.

```yml
song:
  title: The Ocean
  year: 1973
---
song:
  title: Ramble On
  year: 1969
...
---
song:
  title: Traveling Riverside Blues
  year: ```

```python
>>> import yaml
>>> with open("multi_doc.yml", mode="rb") as file:
...   for doc in yaml.safe_load_all(file):
...     print(doc)
... 
{'song': {'title': 'The Ocean', 'year': 1973}}
{'song': {'title': 'Ramble On', 'year': 1969}}
{'song': {'title': 'Traveling Riverside Blues', 'year': None}}
>>>
```

# Loading and writing YAML with PyYAML

PyYAML has 4 loading classes one of which is a base class that should not be used directly.

|              | Anchors, Aliases | YAML Tags | PyYAML Tags | Auxilary Types | Custom Types | Code Execution |
| ------------ | ---------------- | --------- | ----------- | -------------- | ------------ | -------------- |
| Base Loader  | yes              | ignore    | ignore      | ignore         | ingnore      | ignore         |
| SafeLoader   | yes              | yes       | error       | yes            | error        | error          |
| FullLoader   | yes              | yes       | yes         | yes            | error        | error          |
| UnsafeLoader | yes              | yes       | yes         | yes            | yes          | yes            |

Auxiliary types are data types beyond string, array, and hash.

***WARNING:*** Be careful when you use the UnsafeLoader. It will allow for arbitrary code to be executed.

## PyYaml Tags

In addition to the base YAML tags, PyYAML has Python specific tags.

`coord: !!python/tuple [5, 7]` allows you to create a Python tuple.

You can also create objects. For example give the below [person class](https://github.com/xcomfan/real_python_examples/blob/eabe70c8e549668feffeb3098a5b3705947d82f9/working_with_yaml/person.py).

```python
# person.py
from dataclass import dataclass

@dataclass
class Person:
  first_name: str
  last_name: str
```

We can reference this Python class with [the YAML](https://github.com/xcomfan/real_python_examples/blob/eecda6ad7dbc0539b0098f81ae70c6e01283ab2c/working_with_yaml/persons.yml)

```yml
ned: !!python/object:person.Person {first_name: Ned, last_name: Flanders}
seymore: !!python/object:person.Person
  first_name: Principle
  last_name: Skinner
bart: !!python/object:person.Person
  first_name: Bart
  last_name: Simpson
  age: 10
```

Again because we need to use unsafe loader here we use the REPL instead of our print code.

```python
>>> from person import Person
>>> import yaml
>>> with open("persons.yml", mode="rb") as file:
...   result = yaml.unsafe_load(file)
... 
>>> result
{'ned': Person(first_name='Ned', last_name='Flanders'), 'seymore': Person(first_name='Principle', last_name='Skinner'), 'bart': Person(first_name='Bart', last_name='Simpson')}
>>>
```

Notice that the extra `age` attribute on `bart` did not get set. The reason is `!!python/object` tags is not actually calling the `Person` `__init__()` its just setting the attribute of the object you give it. This means you can set whatever you want, but you are not going to get an error if you miss type an attribute.

PyYaml does offer object tags that actually invoke `__init__()`. 

Below is the [car class](https://github.com/xcomfan/real_python_examples/blob/4e5532712b3e47663146e0abe6ef472524fe09f4/working_with_yaml/car.py) and the [yaml](https://github.com/xcomfan/real_python_examples/blob/4e5532712b3e47663146e0abe6ef472524fe09f4/working_with_yaml/car.yml) that we use to create instances. In `!!python/object/apply` the `apply` tag actually instantiates the object passing in arguments to `.__init__()`. The arguments can be an inline sequence, a sequence in YAML hash or using `args` and `kwds` (note not `kwargs` but `kwds`).

```python
class Car:
    def __init__(self, make, model):
        self.make = make
        self.model = model
```

```yml
mustang: !!python/object/apply:car.Car [Ford, Mustang]
civic: !!python/object/apply:car.Car
  - Honda
  - Civic
m3: !!python/object/apply:car.Car
  args: [BMW]
  kwds: {model: m3}
```

The reason we don't get the pretty output below that we saw above with the `Person` class is because here we are not using `dataclass` which adds the pretty `.__str__()` method.

```python
>>> import yaml
>>> from car import Car
>>> with open("car.yml", mode="rb") as file:
...   result = yaml.unsafe_load(file)
... 
>>> result
{'mustang': <car.Car object at 0x1004abee0>, 'civic': <car.Car object at 0x1004bc520>, 'm3': <car.Car object at 0x1004bc370>}
>>> result['mustang']
<car.Car object at 0x1004abee0>
>>> result['mustang'].make
'Ford'
>>> result['mustang'].model
'Mustang'
>>>
```

Below is [an example](https://github.com/xcomfan/real_python_examples/blob/c4e70b9685fcdc237c3094d26b03c9469ae3c48c/working_with_yaml/exec.yml) of why you want to avoid using unsafe load. ***Notice*** how we are able to run the print statement as a side effect. This opens you up to using subprocess to run just about anything. ***Avoid using unsafe_load***

```yaml
deadly: !!python/object/apply:print [Some arbitrary text execution]
```

```python
>>> import yaml
>>> with open("exec.yml", mode="rb") as file:
...   result = yaml.unsafe_load(file)
... 
Some arbitrary text execution
```

## More Loading

PyYAML's load methods support loading from a string, a file handle, or a stream.

### Encoding

YAML 1.1 which is what PyYAML implements supports UTF-8 and UTF-16 encoding. YAML 1.2 additionally supports UTF-32.

For Strings you can do something like `str.encode()` and `str.decode()` 

For Files `open(filename, mode="rt", encoding="utf-8")` if you know the file is encoded with UTF-8 or use the easy method and use binary all the time like we have been in this tutorial. `open(filename, mode="rb")`

### Serialization

PyYAML has three serializer classes: `BaseDumber`, `SafeDumper`, and `Dumper` with the `safe_dump()` and `dump()` corresponding shortcuts. You can dump to a string, file or stream. `safe_dump_all()` and `dump_all()` is supported for multi doc files.

```python
>>> import yaml
>>> data = {"name": "Bart"}
>>> yaml.safe_dump(data) # stream is the default
'name: Bart\n'
>>> import io
>>> stream = io.StringIO()
>>> yaml.dump(data, stream)
>>> stream.getvalue()
'name: Bart\n'
>>> with open("dump.yml", mode="wt") as file:
...   yaml.dump(data, file)
... 
>>> with open("dump16.yml", mode="wb") as file:
...   yaml.dump(data, file, encoding="utf-16")
... 
>>>
```

Below are the serialization option you can leverage.

| Option               | Type    | Description                                              |
| -------------------- | ------- | -------------------------------------------------------- |
| `allow_unicode`      | `bool`  | Don't escape unicode and don't double quote              |
| `canonical`          | `bool`  | Output YAML in canonical form                            |
| `default_flow_style` | `bool`  | Prefer flow style over block style                       |
| `explicit_end`       | `bool`  | End document with triple dots                            |
| `explicit_start`     | `bool`  | Start document with triple dash                          |
| `indent`             | `int`   | Block level indent; 1 < 10                               |
| `width`              | `int`   | Line width, must be 2x indent                            |
| `default_style`      | `str`   | Scalar quotation style, one of: `None`, `"'"`, or `'"'`  |
| `encoding`           | `str`   | Character encoding                                       |
| `line_break`         | `str`   | Newline character: `\r`, `\n`, `\r\n`                    |
| `version`            | `tuple` | Major and minor YAML version likely for future proofing. |
| `tags`               | `dict`  | Mechanism for creating tag shortcuts.                    |
PyYAML supports low level processing via tokens and events. You can also compose documents based on nodes. (deep dive on this is in [the article](https://realpython.com/python-yaml/#getting-started-with-yaml-in-python) so we may want to visit that)

# Tools and challenges you may encounter

PyYAML is a pure python implementation. There are `LibYAML` bindings for performance but that is a separate installation process (see the docs).

`ruamel.yaml` is a YAML 1.2 compatible library.

`StrictYAML` is a type safe YAML parser

`yamllint` is a YAML linter

`yq` is a command line YAML processor for filtering data (a similar tool with the same name is available as a Go based utility)

## Online Tools

[YAML Parser](https://jsonformatter.org/yaml-parser)

[YAML Formatter](https://jsonformatter.org/yaml-formatter)

[YAML Validator](https://jsonformatter.org/yaml-validator)

## When/Should you use it?

If you have the option, maybe don't use YAML because of all the edge cases in the spec. Also avoid using PyYAML tags as those mean Python only and likely your code only. For new projects TOML is a better choice. JSON is also the de-facto standard.