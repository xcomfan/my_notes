
# Getting Started With Testing in Python

## Testing Your Code

### Unit Tests Vs. Integration Tests

**Integration test** checks that components in your application operate with each other.

**Unit test** checks a small component in your application

A **test runner** is a special application designed for running tests, checking the output, and giving you tools for debugging and diagnosing tests and applications.

### Choosing a Test Runner

There are many test runners available for Pythonn. The one built into Python's standard library is called `unittest` and we will use it in this tutorial. The principles of `unittest` are easily portable to other frameworks.

The three most popular test runners are:

* `unittest`
* `nose` or `nose2`
* `pytest`

#### unittest

`unittest` has been build into Python since version 2.1. It contains both a test runner and a testing framework. `unittest` has some requirements for writing and executing tests.

`unittest` requires that:

* You put your tests into classes and methods
* you use a series of special assertion methods in the `unittest.TestCase` class instead of the build in `assert` statement.

```python
import unittest

class TestSum(unittest.TestCase):

    def test_sum(self):
        self.assertEqual(sum([1,2,3]), 6, "Should be 6")

    def test_sum_tuple(self):
        self.assertEqual(sum((1, 2, 2)), 6, "Should be 6")

if __name__ == "__main__":
    unittest.main()
```

```shell
python3 unittest_demo.py
EF
======================================================================
ERROR: test_sum (__main__.TestSum)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/boris/Study/real_python_examples/testing_learning_path/unittest_demo.py", line 6, in test_sum
    self.assertEqual(sum[1,2,3], 6, "Should be 6")
TypeError: 'builtin_function_or_method' object is not subscriptable

======================================================================
FAIL: test_sum_tuple (__main__.TestSum)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/boris/Study/real_python_examples/testing_learning_path/unittest_demo.py", line 9, in test_sum_tuple
    self.assertEqual(sum((1, 2, 2)), 6, "Should be 6")
AssertionError: 5 != 6 : Should be 6

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=1, errors=1)```

#### nose

You may find that the output of `unittest` is a bit hard to understand. `nose` can be used as a drop in replacement for the `unittest` test runner. `nose2` is a fork of `nose` created after `nose` development fell behind. if you are starting from scratch and want to use `nose` use `nose2`.

To get started with `nose2` install if from PyPi with the command `pip install nose2`. `nose2` will try to discover all tests named `test*.py` and test cases inheriting from `unittest.TestCase` in your current directory. In my testing the inheriting thing did not get picked up, so I had to start file name with `test_`

Below we run `nose2` against the sum test example above.

```shell
boris@Mac testing_learning_path % python3 -m venv venv
boris@Mac testing_learning_path % source venv/bin/activate
(venv) boris@Mac testing_learning_path % pip install nose2
Collecting nose2
  Downloading nose2-0.15.1-py3-none-any.whl (211 kB)
     |████████████████████████████████| 211 kB 7.6 MB/s
Installing collected packages: nose2
Successfully installed nose2-0.15.1
(venv) boris@Mac testing_learning_path % python -m nose2
.F
======================================================================
FAIL: test_sum_tuple (test_unittest_demo.TestSum)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/boris/Study/real_python_examples/testing_learning_path/test_unittest_demo.py", line 9, in test_sum_tuple
    self.assertEqual(sum((1, 2, 2)), 6, "Should be 6")
AssertionError: 5 != 6 : Should be 6

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=1)
```

`nose2` offers many command line flags for filtering tests you execute. For more details check the [Nose 2 documentation](https://nose2.readthedocs.io/)

#### pytest

`pytest` supports execution of `unittest` test cases however the real advantage of `pytest` is from its native test cases. `pytest` test cases are a series of functions in a Python file starting with the name `test_`.

`pytest` has some great features such as:

* Support for built in `assert` statement instead of using special `self.assert(*)`
* Support for filtering test cases
* Ability to re-run from the last failing test
* An ecosystem of hundreds of plugins to extend the functionality.

A basic example of a test written using `pytest` is below. More information can be found on the [Pytest Documentation Website](https://docs.pytest.org/en/latest/)

```python
def test_sum():
    assert sum([1, 2, 3]) == 6, "Should be 6"

def test_sum_tuple():
    assert sum((1, 2, 2)) == 6, "Should be 6"
```

## Writing Your First Test

As an example we will be implementing and testing our own sum function. Below with the project structure and our sum function resides in the [`__init__.py`](https://github.com/xcomfan/real_python_examples/blob/2e4c18f74453423002b8ebd99b6ecc86cfe1db3c/testing_learning_path/project/my_sum/__init__.py) file.

```text
project/
│
└── my_sum/
    └── __init__.py
```

### Where to Write the Test

Because your test file will need to import your application to be able to test it you want to place it above the package folder. Below is an example of such a directory tree. It would make more sense to create a folder called `tests` and break up your tests into multiple files. It will be convenient to start each test file name with `test_` so test runners can more easily pick it up. Larger projects split their test into subdirectories based on their purpose and usage.

```text
project/
│
├── my_sum/
│   └── __init__.py
|
└── test.py
```

If the application you are testing is a single script you can import any attributes of the script such as classes, functions and variables by using the built in `__iport__()` function. For example instead of `from my_sum import sum` you can write the following:

```python
target = __import__("my_sum.py")
sum = target.sum
```

The benefit of using `__import__()` is that you don't have to turn your project folder into a package, and you can specify the file name. This is also useful if your file name collides with any standard library packages. For example `math.py` would collide with `math` module.

### How to Structure a Simple Test

Before writing a test you want to make a few decisions.

1. What do you want to test?
2. Are you writing a unit test or an integration test?

Then the structure of the test should loosely follow this workflow:

1. Create your inputs
2. Execute the code being tested, capturing the output
3. Compare the output with an expected result

### How to Write Assertions

The last step of writing a test is to validate the output agains a known response. This is known as an **assertion**. `unittest` comes with lots of methods to assert values, types, and existence of variables. Below are some of the most commonly used methods.

| Method               | Equivalent to      |
| -------------------- | ------------------ |
| `.assertEqual(a, b)` | `a == b`           |
| `.assertTrue(x)`     | `bool(x) is True`  |
| `.assertFalse(x)`    | `bool(x) is False` |
| `.assertIs(a, b)`    | `a is b`           |
| `.assertIsNone(x)`   | `x is None`        |
| `.assertIn(a, b)`    | `a in b`           |
| `.assertIsInstance`  | `isinstance(a, b)` |

`.assertIs()`, `.assertIsNone()`, `.assertIn()`, and `.assertIsInstance()` all have opposite methods, named `.assertIsNot()`, and so forth.

### Side Effects

## Executing Your First Test

### Executing Test Runners

### Understanding Test Output

### Running Your Tests From Visual Studio Code

## Testing for Web Frameworks Like Django and Flask

### Why They are Different From Other Applications

### How to use the Django Test Runner

### How to Use `unittest` and Flask

## More Advanced Testing Scenarios

### Handling Expected Failures

### Isolating Behaviors in Your Application

### Writing Integration Tests

### Testing Data Driven Applications
## Testing in Multiple Environments

### Installing Tox

### Configuring Tox for Your Dependencies

### Executing Tox

## Automating the Execution of Your Tests

## What's Next

### Introducing Linters Into Your Application

### Aggressive Linting With a Code Formatter

### Keeping Your Test Code Clean

### Testing for Performance Degradation Between Changes

### Testing for Security Flaws in Your Application


