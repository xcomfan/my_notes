---
Status: WIP
---

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

If you find that the unit of code you want to test has lots of side effects, you might be breaking the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle). The code is doing too many things and would be better refactored. Following the Single Responsibility Principle is a good way to design code that is easy to wrtie repeatable and simple unit tests for.

## Executing Your First Test

### Executing Test Runners

There are many ways to execute test runners. The snipped below from [`test.py`](https://github.com/xcomfan/real_python_examples/blob/b0486a728963cda02cb71f90aaadee2a04eb6981/testing_learning_path/project/tests/test.py) If one way to start off your tests another is to call `unittest` from the command line wit the command `python -m unittest test`.  This lets you modify the output with command flags such as `-v` for verbose output (`python -m unittest -v test`). You can also let `uniittest` discover the tests in your directory instead of specifying a test file with `python -m unittest discover`.  You can also use the `-s` flag to specify a directory of tests you would like `unittest` to run (`python -m unittest discover -s tests -t src`. (The `-t`) flag specifies the directory to run in so `unittest` is able to import dependencies.

```python
if __name__ == '__main__':
    unittest.main()
```

### Understanding Test Output

For this example we add a [test that intentionally](https://github.com/xcomfan/real_python_examples/blob/9a04e00fbaac0350a3224bcdd4e9ddee2f553669/testing_learning_path/project/test.py) fails but testing if our [sum function](https://github.com/xcomfan/real_python_examples/blob/9a04e00fbaac0350a3224bcdd4e9ddee2f553669/testing_learning_path/project/my_sum/__init__.py) is able to sum up Fractions. When we run the test with the command `python -m unittest test` we get the output below. 

Notice that:

* The first line shows the execution results `F` indicates a failures and `.` indicates a test that passed.
* The `FAIL:` entry shows details about the failed test.
	* The test method name `test_list_fraction`
	* The test module `test` and the test case `TestSum`
	* A traceback of the failing line
	* The details of the assertion with the expected result and the actual result.

Remember that you can add extra information to the test output by adding the `-v` flag to the comman.

```shell
F.
======================================================================
FAIL: test_list_fraction (test.TestSum)
Test that it can sum a list of fractions
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/boris/Study/real_python_examples/testing_learning_path/project/test.py", line 23, in test_list_fraction
    self.assertEqual(result, 1)
AssertionError: Fraction(9, 10) != 1

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=1)```

### Running Your Tests From Visual Studio Code

Support for `unittest`, `nose`, and `pytest` execution is built into the Python plugin.

#TODO the instructions did not match the UI need to look up how to do this later.

## Testing for Web Frameworks Like Django and Flask

When working with web frameworks such as Django or Flask, there are some differences in the way you write and run the tests.

### Why They are Different From Other Applications

To test the routes, views, and models requires a lot of imports and knowledge about the framework being used. Django and Flask both provide a framework based on `unittest`. You can continue writing the tests in the same way we have been learning here, but execute them a bit differently.

### How to use the Django Test Runner

The Django `startup` template creates a `test.py` file inside your application directory. It looks like:

```python
from django.test import TestCase

class MyTestCase(TestCase):
    # Your test methods
```

To execute your tests you call the command `python manage.py test`

If you want multiple test files, replace `tests.py` with a folder called `tests`, insert an empty file inside called `__init__.py`, and create your `text_*.py` files. Django will discover and execute these.  

More information is available at the [Django Documentation Website](https://docs.djangoproject.com/en/2.1/topics/testing/overview/)

### How to Use `unittest` and Flask

Flask requires that the app be imported and then set in test mode. You can instantiate a test client and use the test client to make requests to any routes in your application. 

All of the test client instantiation is done in the `setUp` method of your test case. IN the following example, `my_app` is the name of the application. The code with your test file should look like:

```python
import my_app
import unittest


class MyTestCase(unittest.TestCase):

    def setUp(self):
        my_app.app.testing = True
        self.app = my_app.app.test_client()

    def test_home(self):
        result = self.app.get('/')
        # Make your assertions
```

## More Advanced Testing Scenarios

Most of your testing will not have static value for input such as a string or a number. Sometimes your application will require an instance of a class or a context.

The data that you create as an input is known as a **fixture**. Its common practice to create fixtures and reuse them.

If you are running the same test and passing different values each time and expecting the same result, this is known as **parameterization**.

### Handling Expected Failures

For scenarios where you code should raise an exception to handle an error case you can use the `.assertRaises()` as a context manager, and inside the `with` block execute the test steps:

```python
import unittest

from my_sum import sum


class TestSum(unittest.TestCase):
    # ... other tests before

    def test_bad_type(self):
        data = "banana"
        with self.assertRaises(TypeError):
            result = sum(data)

if __name__ == '__main__':
    unittest.main()
```

Naturally you can replace `TypeError` with any exception type.

### Isolating Behaviors in Your Application

Side effects make unit testing harder since, each time a test is run, it might give a different result, or even worse, one test could impact the state of the application and cause another test to fail.

There are some simple techniques you can use to test parts of your application that have many side effects.

* Refactoring code to follow the Single Responsibility Principle
* Mocking out any method or function calls to remove side effects
* Using integration testing instead of unit testing for this piece of the application

### Writing Integration Tests

**Integration testing** is the testing of multiple components of the application to check that they work together. Integration testing might require acting like a consumer or user of the application by:

* Calling an HTTP RET API
* Calling a Python API
* Calling a web service
* Running a command line

The most significant difference between a unit test and an integration test is that integration tests are checking more components at once and therefor will have more side effects. Integration tests will require more fixtures to be in place like a database, a network socket, or a configuration file. This is why its a good idea to separate your integration tests from unit tests. Since integration tests require longer to run due to having to set up the fixtures. Below is a recommended project layout for isolating unit test and integration tests.

There are many ways to execute a select group of tests. The specify directory `-s` flag can be added to `unittest discover` with the path. For example: `$ python -m unittest discover -s tests/integration` 

```text
project/
│
├── my_app/
│   └── __init__.py
│
└── tests/
    |
    ├── unit/
    |   ├── __init__.py
    |   └── test_sum.py
    |
    └── integration/
        ├── __init__.py
        └── test_integration.py
```

### Testing Data Driven Applications

Many integration tests will require backend data like a database to exist with certain values. These types of tests will depend on different test fixtures to make sure they are repeatable and predictable. A good technique is to store data in a folder within your integration testing folder called `fixtures` to indicate that it contains data. Below is an example of this type of project layout.

```text
project/
│
├── my_app/
│   └── __init__.py
│
└── tests/
    |
    └── unit/
    |   ├── __init__.py
    |   └── test_sum.py
    |
    └── integration/
        |
        ├── fixtures/
        |   ├── test_basic.json
        |   └── test_complex.json
        |
        ├── __init__.py
        └── test_integration.py
```

Within your test case you can use `.setUp()` method to load the test data from a fixture file in a known path. Remember you can have multiple test cases in a single Python file, and the `unittest` discovery will execute both. You an have one test case for each set of test data.

```python
import unittest


class TestBasic(unittest.TestCase):
    def setUp(self):
        # Load test data
        self.app = App(database='fixtures/test_basic.json')

    def test_customer_count(self):
        self.assertEqual(len(self.app.customers), 100)

    def test_existence_of_customer(self):
        customer = self.app.get_customer(id=10)
        self.assertEqual(customer.name, "Org XYZ")
        self.assertEqual(customer.address, "10 Red Road, Reading")


class TestComplexData(unittest.TestCase):
    def setUp(self):
        # load test data
        self.app = App(database='fixtures/test_complex.json')

    def test_customer_count(self):
        self.assertEqual(len(self.app.customers), 10000)

    def test_existence_of_customer(self):
        customer = self.app.get_customer(id=9999)
        self.assertEqual(customer.name, u"バナナ")
        self.assertEqual(customer.address, "10 Red Road, Akihabara, Tokyo")

if __name__ == '__main__':
    unittest.main()
```

If your application depends on data from a remote location such as an API, you will want to make sure your tests are repeatable. Having your tests fail because the API is unavailable can slow down development. In this situation its best to store remote fixtures locally so they can be recalled and sent to the application.

The `requests` library has a complimentary package called `responses` that gives you ways to create `response` fixtures and save them in your test folders. You can learn more about it on their [GitHub page](https://github.com/getsentry/responses)

## Testing in Multiple Environments

If you need to test if your code works on multiple version of Python or with multiple version of a package you can use Tox

### Installing Tox

Tox is available as a PyPi package `pip install tox`

### Configuring Tox for Your Dependencies

You can use the command `tox quickstart` to generate the initial `tox.ini` configuration file similar to the example below.

```ini
[tox]
env_list =
    py39
minversion = 4.24.2

[testenv]
description = run the tests with pytest
package = wheel
wheel_build_env = .pkg
deps =
    pytest>=6
commands =
    pytest {tty:--color=yes} {posargs}
```

Before running Tox you need to have a `setup.py` file in your application folder containing the steps to install your package. You can follow [This Guide](https://packaging.python.org/tutorials/packaging-projects/#setup-py) to create one. Alternatively if your project is not for distribution on PyPi, you can skip this requirement by adding `skipdist=True` to your `tox.ini` file as in the example below.

```ini
[tox]
envlist = py27, py36
skipsdist=True
```

If you don't create a `setup.py` file and your application has some dependencies from PyPi, you will need to specify those on a number of lines under the `[testenv]` section. For exampe:

```ini
[testenv]
deps = django
```

When you execute Tox, it will create virtual environments specified in your `tox.ini` file in a `.tox` directory. From there it will execute `python -m unittest discover` against each virtual environment. You can run this process by simply calling `tox` from the command line.

### Executing Tox

There are some useful commands when running Tox.

* Run only a single environment such as Python 3.6: `tox -e py36`
* Recreate the virtual environments in case your dependencies have change or site-packages got corrupted: `tox -r`
* Run Tox with less verbose output: `tox -q`
* Run Tox with more verbose output: `tox -v`

More information can be found at the [Tox Documentation Site](https://tox.readthedocs.io/en/latest/)

## Automating the Execution of Your Tests

If you want to run your tests automatically when you commit change a Continuous Integration tool such as [Travis CI](https://travis-ci.com/) is the way to go. Travis CI works nice with Python and lets you automate the testing in the cloud (in their cloud not yours)

## What's Next

### Introducing Linters Into Your Application

In addition to running your tests in your CI or automation you can and should also use a linter. One option is `flake8` which you can install with `pip install flake8`

You can run `flake8` over a single file, folder or pattern and it will show you  where your code does not meet the `PEP8` specification.

Below is an example `flake8` configuration file which you can name either `.flake8` or `setup.cfg`. You an also put the `flake8` configuration inside `tox.ini`

```text
[flake8]
ignore = E305
exclude = .git,__pycache__
max-line-length = 90
```

At the commend line the above configuration would look like `$ flake8 --ignore E305 --exclude .git,__pycache__ --max-line-length=90`

Full list of options is available at the [Flake8 Documentation Site](http://flake8.pycqa.org/en/latest/user/options.html)

### Aggressive Linting With a Code Formatter

`flake8` is a passive linter: it recommends changes, but you have to go and change the code. Code formatters will change your code automatically to meet a collection of style and layout practices. One such example is `black`. It does not have any configuration files and adheres to one specific style.

Install with 

`pip install black`

You can run it agains a file or a directory

`black test.py`

### Keeping Your Test Code Clean

Tests have a tendency to have a lot of repeated code which makes changes painful. To better follow DRY principles leverage fixtures to avoid replicated code.

### Testing for Performance Degradation Between Changes

The standard library provides `timeit` module, which can time functions a number of times and give you the distribution. The example below will execute `test()` 100 times and print the output.

```python
def test():
    # ... your code

if __name__ == '__main__':
    import timeit
    print(timeit.timeit("test()", setup="from __main__ import test", number=100))
```

If you are using `pytest` as a test runner, you can use the `pytest-benchmark` plugin. This provides a `pytest` fixture called `benchmark`. You an pass `benchmark()` any callable, and it will log the timing of the callable to the results of `pytest`.

You can install it with: `pip install pytest-benchmark`

Then, you can add a test that uses the fixture and passes the callable to be executed:

```python
def test_my_function(benchmark):
    result = benchmark(test)
```

Executing of `pytest` will now give you benchmark results.

### Testing for Security Flaws in Your Application

You can install `bandit` from PyPi using pip: `pip install bandit`

You can then pass the name of your application module with the `-r` flag, and it will give you a summary:

```shell
$ bandit -r my_sum
[main]  INFO    profile include tests: None
[main]  INFO    profile exclude tests: None
[main]  INFO    cli include tests: None
[main]  INFO    cli exclude tests: None
[main]  INFO    running on Python 3.5.2
Run started:2018-10-08 00:35:02.669550

Test results:
        No issues identified.

Code scanned:
        Total lines of code: 5
        Total lines skipped (#nosec): 0

Run metrics:
        Total issues (by severity):
                Undefined: 0.0
                Low: 0.0
                Medium: 0.0
                High: 0.0
        Total issues (by confidence):
                Undefined: 0.0
                Low: 0.0
                Medium: 0.0
                High: 0.0
Files skipped (0):
```

If there are any `bandit` rules you wish to ignore you can add the below section to your `setup.cfg` file with the options. More details can be found on the [Bandit GitHub Site](https://github.com/PyCQA/bandit)

```cfg
[bandit]
exclude: /test
tests: B101,B102,B301
```

# Test Driven Development Overview

This is just a basic example of using a fixture.  Below we have a basic Stack class we are trying to test:

```python
class Stack:
	def __init__(self):
		self._storage = []

	def __len__(self):
		return len(self._storage)

	def push(self, item):
		self._storage.append(item)
```

Below we have tests for this class. Notice how we avoid having to create a new `Stack` instance for each test by defining a fixture and injecting it into each unit test.

```python
from stack import Stack
import pytest

@pytest.fixture
def stack():
	return Stack()

def test_constructor():
	s = Stack()
	assert isinstance(s, Stack)
	assert len(s) == 0

# inject the fixture in arg
def test_push(stack)
	stack.push(3)
	assert len(stack) == 1
	stack.push(5)
	assert len(stack) == 2
```

# Python's doctest: Document and Test Your Code at Once

The `doctest` module is a testing framework that allows you to use code samples for both documentation and testing.

## Documenting Your Code With Examples and Tests

When you write docstrings a common practice is to embed usage examples for your code. Those examples typically simulate REPL sessions.

To run code examples that you embed in your documentation docstrings you can use the `doctest` modules from the [Python standard library](https://docs.python.org/3/library/index.html)

## Getting to Know Python's `doctest` Module

### What `doctest` Is and How It Works

You can use `doctest` from either your code or command line. To find and run your tests `doctest` follows these steps:

1. Searches for text that looks like Python interactive sessions in your documentation and docstrings. (Generally lines that start with `>>>` or `...`, lines immediately after are either prompts or expected output)
2. Parses those pieces of text to distinguish between executable code and expected results.
3. Runs the executable code like regular Python code.
4. Compares the execution result with expected result.

`docstring` will search for test cases in your documentation and the docstrings of packages, modules, functions, classes and methods. It does not search for test cases in any object that you import. 

### What `doctest` Is Useful For

The `doctest` framework is well suited for quick automation of acceptance test (tests that you run to determine if the specification of a given project are met) at the integration and system testing levels.  For example a package level docstring containing `doctest` tests is a great and fast way to do integration tests. At this level you can test the entire package and the integration of its modules, classes, functions and so on.

A set of high-level `doctest` tests is an excellent way to define a program's specification up front. At the same time, lower level unit-tests let you design the individual building blocks of your program.  At the class, method and function level, `doctest` tests are a powerful tool for testing your code as you write it.

`doctest` has the following purposes:

* Writing quick and effective test cases to check code as you write it.
* Running acceptance, regression, and integration test cases on your projects packages, and modules.
* Checking if your docstring are up to date and in sync with the target code.
* Verifying if your project's documentation is up to date.
* Writing hands on tutorials for your projects packages and modules and demonstrating what to expect as outputs.

## Writing Your Own `doctest` Tests in Python

### Creating `doctest` Tests for Checking Returned and Printed Values

```Python
# calculations.py
def add(a, b):
    """Compute and return the sum of two numbers."
    
    Usage examples:
    >>> add(4.0, 2.0)
    6.0
    >>> add(4, 2)
    6.0
    """
    return float(a+b)
```

To run `doctest` against the above file you would use the command `python -m doctest calculations.py` If all tests pass you will not see any output on the screen. If you want to get verbose output you can add the `-v` flag (`pyton -m doctest -v calculations.py`) This will give you output similar to that below:

```text
Trying:
    add(4.0, 2.0)
Expecting:
    6.0
ok
Trying:
    add(4, 2)
Expecting:
    6.0
ok
1 items had no tests:
    calculations
1 items passed all tests:
   2 tests in calculations.add
2 tests in 2 items.
2 passed and 0 failed.
Test passed.
```

Another common use case for `doctest` is to check the printed output of a piece of code.

```python
# printed_output.py

def greet(name="World"):
    """Print a greeting to the screen

    Usage examples:
    >>> greet("Pythonista")
    Hello, Pythonista!
    >>> greet()
    Hello, World!
    """

    print(f"Hello, {name}!")
```

```text
python3 -m doctest -v printed_output.py
Trying:
    greet("Pythonista")
Expecting:
    Hello, Pythonista!
ok
Trying:
    greet()
Expecting:
    Hello, World!
ok
1 items had no tests:
    printed_output
1 items passed all tests:
   2 tests in printed_output.greet
2 tests in 2 items.
2 passed and 0 failed.
Test passed.
```

`doctest` makes it easy to test what a function prints on the screen. Doing this with other testing frameworks this sort of test would require working with standard output stream which is more complicated.

### Understanding How `doctest` Matches Expected and Actual Test Output

In practice, `doctest` is very strict when matching expected output with actual results. Using integers instead of floats will fail a test. Spaces, tabs or wrapping returned strings in double quotes or inserting blank lines will also break tests.

Note that Python uses single instead of double quotes when displaying strings in an interactive session.

One particular case to not is below:

```python
# failing_tests.py

"""Sample failing tests.

The output must not contain leading or trailing spaces
>>> print("Hello, World!")
  Hello, World!

The output must not be a blank line
>>> print()

"""```

In the test above we have two cases to keep an eye out for.

The first test will have the below output. The reason it fails is because of a leading white space so be mindful of spaces.

```python
Trying:
    print("Hello, World!")
Expecting:
      Hello, World!
**********************************************************************
File ".../failing_tests.py", line 18, in broken_tests
Failed example:
    print("Hello, World!")
Expected:
      Hello, World!
Got:
    Hello, World!
```

The second test yields the output below. In a regular REPL session, calling `print()` without argumetns displays a blank line. In a `doctest` test, a blank line means that the code you just executed doesn't issue any output. We will cover a placeholder for `<BLANKLINE>` later.

```python
Trying:
    print()
Expecting nothing
**********************************************************************
File ".../failing_tests.py", line 22, in broken_tests
Failed example:
    print()
Expected nothing
Got:
    <BLANKLINE>
**********************************************************************
1 items had failures:
   5 of   5 in broken_tests
5 tests in 1 items.
0 passed and 5 failed.
***Test Failed*** 5 failures.
```

You need to guarantee a perfect match between the actual test output and the expected output.

### Writing `doctest` Tests for Catching Exception

The `doctest` module searches for test that looks like a Python exception report or traceback and checks it agains the exception your code raises. In the divide by zero test below we see two expected lines. The first is a header common to all exceptions, and the second contains the actual exception and its specific message. These two lines are the only requirement for `doctest` to successfully check for expected exceptions.

When dealing with exception tracebacks, `doctest` completely ignores the traceback body because it can change unexpectedly. `doctest` is only concerned with the fist line which reads `Traceback (most recent call last):` and the last line. When you are writing your test you have the following options for writing the traceback body.

* Completely remove/omit the traceback body (as in below example)
* Replace parts or all of traceback with ellipsis `(...)`
* Replace traceback body with text explanation.
* Include a sample of traceback body.

```python
# calculations.py
# ...

def divide(a, b):
    """Compute and return the quotient of two numbers.

    Usage examples:
    >>> divide(84, 2)
    42.0
    >>> divide(15, 3)
    5.0
    >>> divide(42, -2)
    -21.0

    >>> divide(42, 0)
    Traceback (most recent call last):
    ZeroDivisionError: division by zero
    """
    return float(a / b)
```

### Building More Elaborate `doctest` Tests

The `doctest` module is able to run code that creates and imports objects, calls functions, assigns variables, evaluates expressions and more. You can use this to perform all kinds of preparation steps before running your actual test cases.

Below is an example of testing a demo queue class. An important note on this example is that `doctest` runs individual docstrings in a dedicated context or scope. Therefore, names declared in one docstring can't be used in another docstring. We will cover the `doctest` scoping mechanism in more detail later.

```python
# queue.py

from collections import deque

class Queue:
    def __init__(self):
        self._elements = deque()

    def enqueue(self, element):
        """Add items to the right end of the queue.

        >>> numbers = Queue()
        >>> numbers
        Queue([])

        >>> for number in range(1, 4):
        ...     numbers.enqueue(number)

        >>> numbers
        Queue([1, 2, 3])
        """
        self._elements.append(element)

    def dequeue(self):
        """Remove and return an item from the left end of the queue.

        >>> numbers = Queue()
        >>> for number in range(1, 4):
        ...     numbers.enqueue(number)
        >>> numbers
        Queue([1, 2, 3])

        >>> numbers.dequeue()
        1
        >>> numbers.dequeue()
        2
        >>> numbers.dequeue()
        3
        >>> numbers
        Queue([])
        """
        return self._elements.popleft()

    def __repr__(self):
        return f"{type(self).__name__}({list(self._elements)})"
```

### Dealing with Whitespaces and Other Characters

If your expected output includes blank lines, then you must use the `<BLANKLINE>` placeholder tag to replace them:

```python
# greet.py

def greet(name="World"):
    """Print a greeting.

    Usage examples:
    >>> greet("Pythonista")
    Hello, Pythonista!
    <BLANKLINE>
    How have you been?
    """
    print(f"Hello, {name}!")
    print()
    print("How have you been?")
```

Tabs are another complex case. Tabs in expected are automatically converted to spaces. Tabs in the actual output are not modified. This will make your test fail because the expected and actual output won't match. For this scenario you need to use the `NORMALIZE_WHITESPACE` option or [directive](https://docs.python.org/3/library/doctest.html#doctest-directives). We will cover how to embed directives into your `doctest` tests later.

To deal with backlashes used for [explicit line joining](https://docs.python.org/3/reference/lexical_analysis.html#explicit-line-joining) you must use a raw string (an r-string), which will preserve your backslashes exactly as you type them.

***Note:*** the leading `r` in the docsting.

```python
# greet.py

def greet(name="World"):
    r"""Print a greeting.

    Usage examples:
    >>> greet("Pythonista")
    /== Hello, Pythonista! ==\
    \== How have you been? ==/
    """
    print(f"/== Hello, {name}! ==\\")
    print("\\== How have you been? ==/")
```

You can also use a regular string and escape the backslashes

```python
# greet.py

def greet(name="World"):
    """Print a greeting.

    Usage examples:
    >>> greet("Pythonista")
    /== Hello, Pythonista! ==\\
    \\== How have you been? ==/
    """
    print(f"/== Hello, {name}! ==\\")
    print("\\== How have you been? ==/")
```

### Summarizing the `doctest` Test Syntax

- Tests start after the **`>>>` prompt** and continue with the **`...` prompt**, just like in a Python interactive session.
- **Expected outputs** must occupy the line or lines immediately after the test.
- Outputs sent to the **standard output stream** are captured.
- Outputs sent to the **standard error stream** aren’t captured.
- The **column** at which a test starts doesn’t matter as long as the expected output is at the same level of indentation.

The `doctest` module ignores anything that doesn't follow the `doctest` test syntax. This allows you to add explanatory text, diagrams or whatever you need. the line `Test below checks if the function catches zero divisions:` is an example of this.

```python
# calculations.py
# ...

def divide(a, b):
    """Compute and return the quotient of two numbers.

    Usage examples:
    >>> divide(84, 2)
    42.0
    >>> divide(15, 3)
    5.0
    >>> divide(42, -2)
    -21.0

    The test below checks if the function catches zero divisions:
    >>> divide(42, 0)
    Traceback (most recent call last):
    ZeroDivisionError: division by zero
    """
    return float(a / b)
```

## Providing `doctest` Tests in Your Projects

In this section we will use a module called `calculations.py` as a sample project for running `doctest` tests from:

* The `README.md` file
* A dedicated test file
* Docstrings

The code for `calculations.py` can be found [here](https://github.com/xcomfan/real_python_examples/blob/44f0c926aa89f17bce8aff18a1f631bccca053e7/testing_learning_path/doctest_examples/calculations.py)

### Including `doctest` Tests in Your Project's Documentation

For the `calculations.py` file above we create the following [`README.md` file](https://github.com/xcomfan/real_python_examples/blob/44f0c926aa89f17bce8aff18a1f631bccca053e7/testing_learning_path/doctest_examples/README.md). 

***Note:*** that the `doctest` tests in the README.md file import the module itself.

***Note:*** Another important details is we include a [blank line](https://github.com/xcomfan/real_python_examples/blob/44f0c926aa89f17bce8aff18a1f631bccca053e7/testing_learning_path/doctest_examples/README.md?plain=1#L24) before the closing triple backticks. This is needed to signal that your `doctests` have finished, or else the triple backticks are interpreted as expected output.

To run the test use the command `python -m doctest -v README.md` This produces output similar to that below.

```text
Trying:
    import calculations
Expecting nothing
ok
Trying:
    calculations.add(2, 2)
Expecting:
    4.0
ok
Trying:
    calculations.subtract(2, 2)
Expecting:
    0.0
ok
Trying:
    calculations.multiply(2, 2)
Expecting:
    4.0
ok
Trying:
    calculations.divide(2, 2)
Expecting:
    1.0
ok
1 items passed all tests:
   5 tests in README.md
5 tests in 1 items.
5 passed and 0 failed.
Test passed.
```

### Adding Dedicated Test Files to Your Project

Another way to provide `doctest` test in your project is by suing a dedicated test file. You can do this with a plain text file. In our example we create the file [test_calculations.txt](https://github.com/xcomfan/real_python_examples/blob/a267c918328a282645938df921b920ee14374f17/testing_learning_path/doctest_examples/test_calculations.txt)

You can run this test file file with the command `python -m doctest -v test_calculations.txt`

### Embedding `doctest` Tests in Your Code's Docstrings

This is the most common way to use `doctest`.  You can add `doctest` tests at different levels in your code.

* Package
* Module
* Class and methods
* Functions

You can write package-level `doctest` tests inside the docstring of your package's `__init__.py` file. The other tests will live in the docstrings of their respective container objects.

## Understanding the `doctest` Scoping Mechanism

When you run `doctest` it creates a shallow copy of the modules global scope then `doctest` creates a local scope with the variables defined in whichever docstring is to be executed. Once the tests run, `doctest` cleans up its local scope, throwing away any local names. Thus local names declared in one docstring can't be used in the next docstring. Every `dotest` test runs in its own local scope but the global scope is common for all the tests.

Below is an example of working with these scope limitations. All of these test pass when executed. Notice that the shallow copy of global scope allows the first `increment_by` test to run while the second `increment_by` test demonstrates that the local scope is deleted between docstrings.

```python
# context.py

total = 100

def decrement_by(number):
    """Decrement the global total variable by a given number.

    >>> local_total = decrement_by(50)
    >>> local_total
    50

    Changes to total don't affect the code's global scope
    >>> total
    100
    """
    global total
    total -= number
    return total

def increment_by(number):
    """Increment the global total variable by a given number.

    The initial value of total's shallow copy is 50
    >>> increment_by(10)
    60

    The local_total variable is not defined in this test
    >>> local_total
    Traceback (most recent call last):
    NameError: name 'local_total' is not defined
    """
    global total
    total += number
    return total
```

When you use a dedicated file to provide `doctest` tests, all the tests from the file will run in the same execution scope. This means that execution of a given test can affect the result of a later test. This is not ideal as a failing test may not be the root cause of the issue. You can give each test its own scope by either placing it in a separate file (kind of annoying to do) or wrapping it in a function as in the example below.

```pyton
>>> def test_add():
...     import calculations
...     return calculations.add(2, 4)
>>> test_add()
6.0
```

## Exploring Some Limitations of `doctest`

`doctest` does not have support for [fixtures](https://docs.pytest.org/en/6.2.x/fixture.html#fixture) or the [setup](https://docs.python.org/3/library/unittest.html#unittest.TestCase.setUp) and [teardown](https://docs.python.org/3/library/unittest.html#unittest.TestCase.setUp) mechanisms of `unittest`.  You can take advantage of the [unittest API](https://docs.python.org/3/library/doctest.html?highlight=doctest#unittest-api) to reproduce some of the set up and tear down functionality.

`doctest` also needs exact matches in output. This makes some modules hard to test. For example if function returns a set which in python does not have a guaranteed order your test will be flaky. You would have to find work arounds such as using the `sorted()` function to adjust the output. 

`doctest` also does not have support for parameterization but you can simulate it with the technique below.

```python
# even_numbers.py

def get_even_numbers(numbers):
    """Return the even numbers in a list.

    >>> args = [[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]]
    >>> expected = [[2, 4], [6, 8], [10, 12]]

    >>> for arg, expected in zip(args, expected):
    ...     get_even_numbers(arg) == expected
    True
    True
    True
    """
    return [number for number in numbers if number % 2 == 0]
```

One more limitation is is testing object creating using `object.__repr__()` because that output contains the object memory address which will change run to  run. To work around this you can use the [`ELLIPSIS`](https://docs.python.org/3/library/doctest.html#doctest.ELLIPSIS) directive as in the example below.

```python
# user.py

class User:
    def __init__(self, name, favorite_colors):
        """Initialize instances of User.

        Usage examples:
        >>> User("John", {"#797EF6", "#4ADEDE", "#1AA7EC"}) # doctest: +ELLIPSIS
        <user.User object at 0x...>
        """
        self.name = name
        self._favorite_colors = set(favorite_colors)

# ...
```

## Considering Security While Using `doctest`

The `doctest` module uses `exec()` internally to executed tests embedded in docstrings and documentation files. This function is well known to be a risky for running arbitrary code, and that applies to `doctest`. Avoid running `docters` if you are not certain what the code is doing.

## Using `doctest` for Test-Driven Development

The flow should be:

1. Write the tests in your docstrings or documentation using the `doctest` syntax.
2. Write the code to pass the tests
3. Run tests using `doctest`

## Running Your Python `doctest` Tests

You can run your `doctest` tests from inside your Python code.

### Running `doctest` From Your Code

`doctest` exports two modules which are useful for running `doctest` from inside your code.

| Function     | Description                                     |
| ------------ | ----------------------------------------------- |
| `testfile()` | Runs `doctest` tests from a dedicated test file |
| `testmod()`  | Runs `doctest` tests from a Python module       |

```python
# run_file_tests.py

import doctest

doctest.testfile("test_calculations.txt", verbose=True)
```

`testfile()` function takes some [optional arguments](https://docs.python.org/3/library/doctest.html#doctest.testfile) that let you customize details in the process of running your tests. 

To run the `doctest` tests of a particular module you use `testmod()` as in the example below.  The name main idiom is to make sure this does not run on an import.

```python
if __name__ == "__main__":
    import doctest
    doctest.testmod(verbose=True)
```

Another way is to create a dedicated runner file:

```python
# run_module_tests.py

import doctest

import calculations

doctest.testmod(calculations, verbose=True)
```

Without the verbose option you won't get any output unless your tests fail. Check [documentation](https://docs.python.org/3/library/doctest.html#doctest.testmod) for more options.

You can also use the modules [API](https://docs.python.org/3/library/doctest.html#advanced-api) if you need more fine grained control over your tests.

## Controlling the Behavior of `doctest`: Flags and Directives

The `doctest` module provides a series of named constants that you can use as flags when you run `doctest`. These can be used to add directives to your `doctest` tests such as:

* Accepting True for 1
* Rejecting blank lines
* Normalizing whitespaces
* Abbreviating outputs with ellipsis (`...`)
* Ignoring exception details like the exception message
* Skipping a given test
* Finishing after the fist failing test

Check [documentation](https://docs.python.org/3/library/doctest.html#option-flags) for the full list of directives.

### Using Flags at the Command Line

In example below we use `1` instead of `True` . This will pass but if want to be string an only accept true you can use `python -m doctest -o DONT_ACCEPT_TRUE_FOR_1 options.txt`

```text
>>> 5 < 7
1
```

Another example if if you want to save yourself overly verbose validation for a string; you can enable ELLIPSES with:

```shell
$ python -m doctest \
    -o DONT_ACCEPT_TRUE_FOR_1 \
    -o ELLIPSIS options.txt
```

```text
>>> print("Hello, Pythonista! Welcome to Real Python!")
Hello, ... Python!
```

And to normalize text:

```text
>>> print("\tHello, World!")
    Hello, World!
```

```Shell
$ python -m doctest \
    -o DONT_ACCEPT_TRUE_FOR_1 \
    -o ELLIPSIS \
    -o NORMALIZE_WHITESPACE options.txt
```

### Embedding Directives in Your `doctest` tests

A `doctest` [directive](https://docs.python.org/3/library/doctest.html#doctest-directives) consists of an inline comment that begins with `# doctest:`

```text
>>> 5 < 7  # doctest: +DONT_ACCEPT_TRUE_FOR_1
True

>>> print(
...    "Hello, Pythonista! Welcome to Real Python!"
... )  # doctest: +ELLIPSIS
Hello, ... Python!

>>> print("\tHello, World!")  # doctest: +NORMALIZE_WHITESPACE
    Hello, World!
```

## Running `doctest` Tests With `unittest` and `pytest`

This is useful as you may want to use `doctest` but it may not be sufficient for everything you need in your project.

### Using `unittest` to Run `doctest` Tests

The `doctest` API allows you to convert `doctest` tests into [`unittest` test suites](https://docs.python.org/3/library/unittest.html#unittest.TestSuite). There are 2 main functions that drive this:

| Function       | Description                                                                       |
| -------------- | --------------------------------------------------------------------------------- |
| `DocFileSuite` | Converts `doctest` tests from one or more text files into a `unittest` test suite |
| `DocTestSuite` | Converts `doctest` tests from a module into a `unittest` suite                    |

To integrate your `doctest` tests with the `unittest` discovery mechanism, you must add a `load_tests()` function to your `unittest` boilerplate code.

```text
>>> import calculations

>>> calculations.add(2, 2)
4.0

>>> calculations.subtract(2, 2)
0.0

>>> calculations.multiply(2, 2)
4.0

>>> calculations.divide(2, 2)
1.0
```

```python
# test_calculations.py

import doctest
import unittest

def load_tests(loader, tests, ignore):
    tests.addTests(doctest.DocFileSuite("test_calculations.txt"))
    return tests

# Your unittest tests goes here...

if __name__ == "__main__":
    unittest.main()
```

```Shell
$ python test_calculations.py
.
---------------------------------------------------------------
Ran 1 test in 0.004s

OK
```

If your `doctest` tests live in your codes docstrings, then you can integrate them into your `unittest` suite with the below variation of `load_tests()`

```python
# test_calculations.py

import doctest
import unittest

import calculations

def load_tests(loader, tests, ignore):
    tests.addTests(doctest.DocTestSuite(calculations))
    return tests

# Your unittest goes here...

if __name__ == "__main__":
    unittest.main()
```

You can combine both options as well.

```python
import doctest
import unittest

import calculations

def load_tests(loader, tests, ignore):
    tests.addTests(doctest.DocFileSuite("test_calculations.txt"))
    tests.addTests(doctest.DocTestSuite(calculations))
    return tests

if __name__ == "__main__":
    unittest.main()
```

### Using `pytest` to Run `doctest` Tests

You can [integrate `doctest` tests with `pytest`](https://doc.pytest.org/en/latest/how-to/doctest.html) via the `pytest` `--doctest-glob` command line option. For example: `pytest --doctest-glob="test_calculations.txt`

Just like `unittest`, `pytest` interprets your dedicated test file as a single test. The `--doctest-glob` option accepts and matches patterns that'll allow you turn multiple files. A helpful pattern would be `test*.txt`.

You can also execute `doctest` tests directly from your code's deocstrings. To do this, you can use the `--doctest-modules` command line option which will scan all the modules under your working directory, loading and running any `doctest` tests it finds. To make this configuration permanent you can add the following parameter to pytests's configuration file in your project root directory.

```txt
; pytest.ini

[pytest]
addopts = --doctest-modules
```


# Python's `unittest`: Writing Unit Tests for Your Code

## Getting to Know Python's `unittest`

`unittest` supports some essential concepts that facilitate test creation, organization, and automation.

* **Test case:** An individual unit of testing. It examines the output for a given input set.
* **Test suite:** A collection of test cases, test suites, or both. They are grouped and executed as a whole.
* **Test fixture:** A group of actions required to set up an environment for testing. It also included a teardown process after the tests run.
* **Test runner:** A component that handles the execution of tests and communicates the results to the user.

## Organizing Your Tests With the `TestCase` Class

The `unittest` package defines the `TestCase` class, which is primarily designed for writing unit tests. To start writing your test cases, you just need to import the class and subclass it. Then, you’ll add methods whose names should begin with `test`. These methods will test a given unit of code using different inputs and check for the expected results.

Below is an example which tests the built in `abs()` function

```python
import unittest

class TestAbsFunction(unittest.TestCase):
    def test_positive_number(self):
        self.assertEqual(abs(10), 10)

    def test_negative_number(self):
        self.assertEqual(abs(-10), 10)

    def test_zero(self):
        self.assertEqual(abs(0), 0)
```

### Creating Test Cases

In the following examples we will be testing this example function:

```python
def categorize_by_age(age):
    if 0 <= age <= 9:
        return "Child"
    elif 9 < age <= 18:
        return "Adolescent"
    elif 18 < age <= 65:
        return "Adult"
    elif 65 < age <= 150:
        return "Golden age"
    else:
        return f"Invalid age: {age}"
```

Below is an example of testing the example function. ***Note:*** that the test methods have multiple assertions.  Using multiple assertions has the following benefits.

* Efficiency: Multiple assertions in a single test can reduce repetitive code.  It can also make tests run faster in those scenarios where you have to set up and tear down requirements for each test.
* Contextual testing: Multiple assertions may be needed to check that a function behaves correctly in a specific context.
* Convenience: Multiple assertions in a test can be more straightforward and less tedious to write compared to writing multiple single-assertion tests.

Some down sides of multiple assertions are:

* Clarity and isolation: When a test with multiple assertions fails, it can be harder to immediately identify which assertion caused the failure. 
* Breakage risk: When an earlier assertion fails the subsequent ones are not executed. This can hide additional issues.
* Test purpose blurring: When a test has multiple assertions it can become less focused and harder to understand.

```python
# ...

class TestCategorizeByAge(unittest.TestCase):
    # ...

    def test_boundary_child_adolescent(self):
        self.assertEqual(categorize_by_age(9), "Child")
        self.assertEqual(categorize_by_age(10), "Adolescent")

    def test_boundary_adolescent_adult(self):
        self.assertEqual(categorize_by_age(18), "Adolescent")
        self.assertEqual(categorize_by_age(19), "Adult")

    def test_boundary_adult_golden_age(self):
        self.assertEqual(categorize_by_age(65), "Adult")
        self.assertEqual(categorize_by_age(66), "Golden age")
```

### Running `unittest` tests

If you are calling `unittest.main()` to run `unittest` from your code, you can provide a verbosity option as in the example below.

* `0` for quite
* `1` for  normal
* `2` for detailed

```python
# ...

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

If you want to make the output of your `unittest` runs more descriptive, you can add docstrings to your test cases as in the example below. The docstrings will be in the output as shown below when you use detailed output.

```python
# ...

class TestCategorizeByAge(unittest.TestCase):
    def test_child(self):
        """Test for 'Child'"""
        self.assertEqual(categorize_by_age(5), "Child")

    def test_adolescent(self):
        """Test for 'Adolescent'"""
        self.assertEqual(categorize_by_age(15), "Adolescent")
# ...
```

```shell
$ python test_age.py
test_adolescent (__main__.TestCategorizeByAge.test_adolescent)
Test for 'Adolescent' ... ok
test_adult (__main__.TestCategorizeByAge.test_adult)
Test for 'Adult' ... ok
```

### Skipping Tests

Some common scenarios where you may need to skip test cases are:

* Incomplete feature
* External services which may not be available yet
* Conditional execution: May only want to run some tests is for example version of library is at some version.
* Known failures: a bug being worked on 
* Performance considerations: some tests may be time consuming and you don't always want to run them.
* Deprecated features: if deprecated feature has not been removed from code yet you may want to skip the tests for it.

The following decorators can be used to skip tests.

| Decorator                                 | Description                                            |
| ----------------------------------------- | ------------------------------------------------------ |
| `@unittest.skip(reason)`                  | Skips the decorated test                               |
| `@unittest.skipIf(condition, reason)`     | Skips the decorated test if `condition` is `True`      |
| `@unittest.skipUnless(condition, reason)` | Skips the decorated test unless `condidtion` is `True` |
```python
import sys
import unittest

class SkipTestExample(unittest.TestCase):
    @unittest.skip("Unconditionally skipped test")
    def test_unimportant(self):
        self.fail("The test should be skipped")

    @unittest.skipIf(sys.version_info < (3, 12), "Requires Python >= 3.12")
    def test_using_calendar_constants(self):
        import calendar

        self.assertEqual(calendar.Month(10), calendar.OCTOBER)

    @unittest.skipUnless(sys.platform.startswith("win"), "Requires Windows")
    def test_windows_support(self):
        from ctypes import WinDLL, windll

        self.assertIsInstance(windll.kernel32, WinDLL)

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

### Creating Subtests

`unittest` allows you to distinguish between similar tests using the `subTest()` context manager.  This method returns a context manager that executes the enclosed code block as a subtest. You can use this context manger to provide multiple input values for your tests.

The below code is an example of what makes sense to re-factor using `subTest()`

We are testing the basic function

```python
def is_even(number):
    return number % 2 == 0
```

Without using `subTest()`

```python
import unittest

from even import is_even

class TestIsEven(unittest.TestCase):
    def test_even_number(self):
        self.assertEqual(is_even(2), True)

    def test_odd_number(self):
        self.assertEqual(is_even(3), False)

    def test_negative_even_number(self):
        self.assertEqual(is_even(-2), True)

    def test_negative_odd_number(self):
        self.assertEqual(is_even(-3), False)

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

Refactored to use `subTest()` and expanded with additional test inputs.

```python
import unittest

from even import is_even

class TestIsEven(unittest.TestCase):
    def test_even_number(self):
        for number in [2, 4, 6, -8, -10, -12]:
            with self.subTest(number=number):
                self.assertEqual(is_even(number), True)

    def test_odd_number(self):
        for number in [1, 3, 5, -7, -9, -11]:
            with self.subTest(number=number):
                self.assertEqual(is_even(number), False)

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

## Exploring the Available Assert Methods

### Comparing Values

| Method                 | Comparison         |
| ---------------------- | ------------------ |
| `.assertEqual(a, b)`   | `a == b`           |
| `.assertNotEual(a, b)` | `a != b`           |
| `.assertTrue(x)`       | `bool(x) is True`  |
| `.assertFalse`         | `bool(x) is False` |

***Note:*** A test comparison such as `.assertEuqals(is_prime(17), True)` is valid, but `.assertTrue()` can better communicate the tests intention.

### Comparing Objects by Their Identity

| Method                | Comparison      |
| --------------------- | --------------- |
| `.assertIs(a, b)`     | `a is b`        |
| `.assertIsNot(a, b)`  | `a is not b`    |
| `.assertIsNone(x)`    | `x is None`     |
| `.assertIsNotNone(x)` | `x is not None` |
Example below is an illustration of testing object identity.

```python
import unittest

class TestListIdentity(unittest.TestCase):
    def test_list_aliases(self):
        a = ["Python", "unittest"]
        b = a
        self.assertIs(a, b)

    def test_list_objects(self):
        a = ["Python", "unittest"]
        b = ["Python", "unittest"]
        self.assertIsNot(a, b)

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

### Comparing Collections

#TODO this is a good place to link to where you cover these topics

As a reminder collections are lists, tuples, strings, dictionaries and sets

| Method                        | Comparison                   |
| ----------------------------- | ---------------------------- |
| `.assertSequenceEqual(a, b)`  | Equality of two sequences    |
| `.assertMultiLineEqual(a, b)` | Equality of two strings      |
| `.assertListEqual(a, b)`      | Equality of two lists        |
| `.assertTupleEqual(a, b)`     | Equality of two tuples       |
| `.assertDictEqual(a, b)`      | Equality of two dictionaries |
| `.assertSetEqual(a, b)`       | Equality of two sets         |

***Note:*** The `.assertEqual()` method implicitly calls the specialized methods in the table above; so in most cases you won't need to call these methods directly. You can use these to add clarity to your tests.

Example below:

```python
import unittest

class TestCollections(unittest.TestCase):
    def test_sequence_objects(self):
        a = ("H", "e", "l", "l", "o")
        b = "Hello"
        self.assertSequenceEqual(a, b)

    def test_string_objects(self):
        a = "Hello"
        b = "Hello"
        self. assertMultiLineEqual(a, b)

    def test_list_objects(self):
        a = [1, 2, 3, 4, 5]
        b = [1, 2, 3, 4, 5]
        self.assertListEqual(a, b)

    def test_tuple_objects(self):
        a = ("Jane", 25, "New York")
        b = ("Jane", 25, "New York")
        self.assertTupleEqual(a, b)

    def test_dictionary_objects(self):
        a = {"framework": "unittest", "language": "Python"}
        b = {"language": "Python", "framework": "unittest"}
        self.assertDictEqual(a, b)

    def test_set_objects(self):
        a = {1, 2, 4, 3, 5}
        b = {1, 5, 3, 4, 2}
        self.assertSetEqual(a, b)

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

### Running Membership Tests

| Method               | Check        |
| -------------------- | ------------ |
| `.assertIn(a, b)`    | `a in b`     |
| `.assertNotIn(a, b)` | `a not in b` |

```python
import unittest

class TestMembership(unittest.TestCase):
    def test_value_in_collection(self):
        a = 1
        b = [1, 2, 3, 4, 5]
        self.assertIn(a, b)

    def test_value_not_in_collection(self):
        a = 10
        b = [1, 2, 3, 4, 5]
        self.assertNotIn(a, b)

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

### Checking for an Objects Type

| Method                       | Comparison             |
| ---------------------------- | ---------------------- |
| `.assertIsInstance(a, b)`    | `isinstance(a, b)`     |
| `.assertNotIsInstance(a, b)` | `not isinstance(a, b)` |

### Testing for Exceptions

| Method                                           | Check                            |
| ------------------------------------------------ | -------------------------------- |
| `.assertRaises(exc, fun, *args, **kwds)`         | `fun (*args, **kwds) raises exc` |
| `.assertRaisesRegex(exc, r, fun, *args, **kwds)` | `fun (*args, **kwds) raises exc` |
As an example we test this `is_prime()` function.

```python
from math import sqrt

def is_prime(number):
    if not isinstance(number, int):
        raise TypeError(
            f"integer number expected, got {type(number).__name__}"
        )
    if number < 2:
        raise ValueError(f"integer above 1 expected, got {number}")
    for candidate in range(2, int(sqrt(number)) + 1):
        if number % candidate == 0:
            return False
    return True
```

```python
import unittest

from prime_v2 import is_prime

class TestIsPrime(unittest.TestCase):
    def test_prime_number(self):
        self.assertTrue(is_prime(17))

    def test_non_prime_number(self):
        self.assertFalse(is_prime(10))

    def test_invalid_type_float(self):
        with self.assertRaises(TypeError):
            is_prime(4.5)

    def test_invalid_type_str(self):
        with self.assertRaises(TypeError):
            is_prime("5")

    def test_zero_and_one(self):
        with self.assertRaises(ValueError):
            is_prime(0)
        with self.assertRaises(ValueError):
            is_prime(1)

    def test_negative_number(self):
        with self.assertRaises(ValueError):
            is_prime(-1)

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

The `TestCase` class also provides some additional assert methods that help you with `warnings` and logs. 

| Method                                      | Check                                                          |
| ------------------------------------------- | -------------------------------------------------------------- |
| `.assertWarns(warn, fun, *args, **kwds)`    | fun(*args, **kwds) raises warn                                 |
| `.assertWarns(warn, r, fun, *args, **kwds)` | fun(*args, **kwds) raises warn and the message matches regex r |
| `.assertLogs(logger, level)`                | The `with` block logs on logger with minimum level             |
| `.assertNoLogs(logger, level)`              | The `with` block does not log on logger with minimum level     |

### Using Custom Assert Methods

You can create your own assert methods as well. To do this, you can subclass the TestCase and extend the class with new assertion methods.

In the example below we add an assertion that all values in a list are integers.

```python
import unittest

class CustomTestCase(unittest.TestCase):
    def assertAllIntegers(self, values):
        for value in values:
            self.assertIsInstance(
                value,
                int,
            )
```

Below we use our custom assertion

```python
import unittest

class CustomTestCase(unittest.TestCase):
    # ...

class TestIntegerList(CustomTestCase):
    def test_values_are_integers(self):
        integers_list = [1, 2, 3, 4, 5]
        self.assertAllIntegers(integers_list)

if __name__ == "__main__":
    unittest.main()
```

## Using `unittest` From the Command Line

Using command line you can run tests directly from modules, classes, and from individual test methods.

```shell
$ python -m unittest test_module1 test_module2
$ python -m unittest test_module.TestCase
$ python -m unittest test_module.TestCase.test_method
```

Note that for these commands to work, the target module must be in the import path of your current Python environment. To avoid this issue you can specify the path to the module. `python -m unittest test_age.py` (you can also use the `python -m unittest` shortcut for this behaviour)

### Discovering Tests Automatically

The test loader can inspect each module in a given directory looking for classes derived from `TestCase`. You can trigger this behavior with `python -m unittest discover`.

Some command line options to the `discover` sub command are:

| Option                          | Description                                               |
| ------------------------------- | --------------------------------------------------------- |
| `-s` or `--start-directory`     | Specify the directory where your tests reside             |
| `-v` or `--verbose`             | Produce verbose output                                    |
| `-p` or `--pattern`             | Allows for using glob patterns and defaults to `test*.py` |
| `-t` or `--top-level-directory` | Defines the top level directory of a project              |
### The `unittest` cli itself has a number of options:

| Option               | Description                                                                                                                 |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `-v`                 | Shows more verbose output                                                                                                   |
| `-b` or `--buffer`   | Buffers the standard output and error streams during the test execution                                                     |
| `-c` or `--catch`    | Waits for the current test to run and reports all the results up to the point `^Ctrl + C` is pressed during test execution. |
| `-f` or `--failfast` | Stops the test run on the first error of failure                                                                            |
| `-k`                 | Only runs test methods and classes that match the patter or substing                                                        |
| `--locals`           | Show local variables in tracebacks                                                                                          |
| `--duration N`       | Shows the N slowest test cases (N=0 for all)                                                                                |

## Grouping Your Tests With the `TestSuite` Class

The `TestSuite` class allows you to create groups of tests and run them selectively. This can be useful for:

* Organizing tests into logical groups.
* Break up tests into levels such as unit tests, integration tests, and system tests.
* Group tests by platform such as Linux, macOS

For example we have the below program.

```python
import math
from collections import Counter

def add(x, y):
    return x + y

def subtract(x, y):
    return x - y

def multiply(x, y):
    return x * y

def divide(x, y):
    if y == 0:
        raise ZeroDivisionError("Cannot divide by zero.")
    return x / y

def mean(data):
    return sum(data) / len(data)

def median(data):
    n = len(data)
    index = n // 2
    if n % 2:
        return sorted(data)[index]
    return sum(sorted(data)[index - 1 : index + 1]) / 2

def mode(data):
    c = Counter(data)
    return [k for k, v in c.items() if v == c.most_common(1)[0][1]]
```

In this code we would need to test basic arithmetic operations, and other statistical operations. Below is a test file not using `TestSuite()`

```python
# test_calculations.py
import unittest

from calculations import (
    add,
    divide,
    mean,
    median,
    mode,
    multiply,
    subtract,
)

class TestArithmeticOperations(unittest.TestCase):
    def test_add(self):
        self.assertEqual(add(10, 5), 15)
        self.assertEqual(add(-1, 1), 0)

    def test_subtract(self):
        self.assertEqual(subtract(10, 5), 5)
        self.assertEqual(subtract(-1, 1), -2)

    def test_multiply(self):
        self.assertEqual(multiply(10, 5), 50)
        self.assertEqual(multiply(-1, 1), -1)

    def test_divide(self):
        self.assertEqual(divide(10, 5), 2)
        self.assertEqual(divide(-1, 1), -1)
        with self.assertRaises(ZeroDivisionError):
            divide(10, 0)

class TestStatisticalOperations(unittest.TestCase):
    def test_mean(self):
        self.assertEqual(mean([1, 2, 3, 4, 5, 6]), 3.5)

    def test_median_odd(self):
        self.assertEqual(median([1, 3, 3, 6, 7, 8, 9]), 6)

    def test_median_even(self):
        self.assertEqual(median([1, 2, 3, 4, 5, 6, 8, 9]), 4.5)

    def test_median_unsorted(self):
        self.assertEqual(median([7, 1, 3, 3, 2, 6]), 3)

    def test_mode_single(self):
        self.assertEqual(mode([1, 2, 2, 3, 4, 4, 4, 5]), [4])

    def test_mode_multiple(self):
        self.assertEqual(set(mode([1, 1, 2, 3, 4, 4, 5, 5])), {1, 4, 5})

if __name__ == "__main__":
    unittest.main()
```

### Creating Test Suites With the `TestSuite()` Constructor

The `TestSuite` class constructor takes the `tests` argument that must be an iterable of tests or other test suites. Thus our test suite code would look like:

```python
# ...

def make_suite():
    arithmetic_tests = [
        TestArithmeticOperations("test_add"),
        TestArithmeticOperations("test_subtract"),
        TestArithmeticOperations("test_multiply"),
        TestArithmeticOperations("test_divide"),
    ]
    return unittest.TestSuite(tests=arithmetic_tests)

if __name__ == "__main__":
    suite = make_suite()
    runner = unittest.TextTestRunner(verbosity=2)
    runner.run(suite)
```

To run the suite, you create a [`TextTestRunner`](https://docs.python.org/3/library/unittest.html#unittest.TextTestRunner) and pass the suite to its `.run()` method. Running this file will give you output similar to that below. You wind up running just the test in the test suite, skipping the other tests in `test_calculations.py` example above.

```shell
$ python test_calculations.py
test_add (__main__.TestArithmeticOperations.test_add) ... ok
test_subtract (__main__.TestArithmeticOperations.test_subtract) ... ok
test_multiply (__main__.TestArithmeticOperations.test_multiply) ... ok
test_divide (__main__.TestArithmeticOperations.test_divide) ... ok

----------------------------------------------------------------------
Ran 4 tests in 0.000s

OK
```

### Adding Tests to a Suite `.addTest()` and `.addTests()`

```python
# ...

def make_suite():
    arithmetic_suite = unittest.TestSuite()
    arithmetic_suite.addTest(TestArithmeticOperations("test_add"))
    arithmetic_suite.addTest(TestArithmeticOperations("test_subtract"))
    arithmetic_suite.addTest(TestArithmeticOperations("test_multiply"))
    arithmetic_suite.addTest(TestArithmeticOperations("test_divide"))
    return arithmetic_suite

if __name__ == "__main__":
    suite = make_suite()
    runner = unittest.TextTestRunner(verbosity=2)
    runner.run(suite)
```

```python
# ...

def make_suite():
    statistical_tests = [
        TestStatisticalOperations("test_mean"),
        TestStatisticalOperations("test_median_odd"),
        TestStatisticalOperations("test_median_even"),
        TestStatisticalOperations("test_median_unsorted"),
        TestStatisticalOperations("test_mode_single"),
        TestStatisticalOperations("test_mode_multiple"),
    ]
    statistical_suite = unittest.TestSuite()
    statistical_suite.addTests(statistical_tests)

    return statistical_suite

if __name__ == "__main__":
    suite = make_suite()
    runner = unittest.TextTestRunner(verbosity=2)
    runner.run(suite)
```

### Creating Suites With the `load_tests() Function`

The `load_tests()` function is a hook that `unittest` provides for customizing test loading and suite creations. The function takes three mandatory arguments with the below signature:

```Python
def load_tests(loader, standard_tests, pattern):
```

The `loader` argument will hold a test loader, which normally is an instance of [`TestLoader`](https://docs.python.org/3/library/unittest.html#unittest.TestLoader) When you define `load_tets()` in a module, the `standard_tests` argument will receive the tests that are loaded from the module by default. When you define the function in a package the `standard_tests` will get the tests loaded from the package's `__init__.py` file. Finally the `pattern` argument is a blog pattern that you can use when discovering the tests.

When you call `unittest.main()` in a test module, the `load_tests()` function defined in the module gets called automatically, and `unittest` takes care of passing in the required arguments. This behavior allows you to build a test suite with minimal boilerplate code.

Here’s an example of how to create two test suites. One for the arithmetic tests and another for the statistical tests:

```python
# ...

def load_tests(loader, standard_tests, pattern):
    suite = unittest.TestSuite()
    suite.addTests(loader.loadTestsFromTestCase(TestArithmeticOperations))
    suite.addTests(loader.loadTestsFromTestCase(TestStatisticalOperations))
    return suite

if __name__ == "__main__":
    unittest.main()
```

We are not using `_standard_tests` in this example. This argument can be useful for when test packages where you’d like to add tests to those found in `__init__.py`and build a test suite from there. The `pattern` argument is also for loading tests from packages rather than from modules.

## Creating Test Fixtures

A fixture is a preparation that you perform before and after running one or more tests. The preparation before a test is known as **setup**, while the tasks you perform after the test run is called **teardown**. In `unittest` you create setup and teardown fixtures in your test cases by overriding the following methods in your `TestClass` subclasses.

| Method             | Description                                                                                   |
| ------------------ | --------------------------------------------------------------------------------------------- |
| `.setUP()`         | An instance method that `unittest` calls before running each test method in a test case class |
| `.tearDown()`      | An instance method that `unittest` calls after running each test method in a test case class  |
| `.setUpClass()`    | A class method that `unittest` calls before running the tests in a test case class            |
| `.tearDownClass()` | A class method that `unittest` calls after running the tests in a test case class.            |

***Note:*** The last two methods are class methods, which means you need to use the `@classmethod` decorator to create them. These methods only take the current test class as an argument. Remember that they run only once per class.

```python
import unittest

class Test(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        ...

    @classmethod
    def tearDownClass(cls):
        ...
```

To create module-level fixtures, you need to use **module-level function** rather than methods on a `TestCase subclass`. The required functions are:

| Function           | Description                                         |
| ------------------ | --------------------------------------------------- |
| `setUpModule()`    | Runs before all test cases in the containing module |
| `tearDownModule()` | Runs after all test cases have run                  |

If an exception occurs in the `setUpModue()` function, then none of the tests in the module run, and the `tearDownModule()` function won't run either.

### Test Fixtures

We will test the following implementation of a stack for our examples.

```python
class Stack:
    def __init__(self, items=None):
        self.items = list(items) if items is not None else []

    def push(self, item):
        self.items.append(item)

    def pop(self):
        return self.items.pop()

    def __len__(self):
        return len(self.items)

    def __iter__(self):
        return iter(self.items)

    def __reversed__(self):
        return reversed(self.items)
```

To test the above code you have two options.

1. You can create a new and independent instance of Stack for every test.
2. You can create a single instance of the class for all the tests.

The second option is less repetitive and what fixtures are intended for.

```python
import unittest

from stack import Stack

class TestStack(unittest.TestCase):
    def setUp(self):
        self.stack = Stack()

    def tearDown(self):
        del self.stack

    def test_push(self):
        self.stack.push(1)
        self.assertEqual(self.stack.items, [1])

    def test_pop(self):
        self.stack.push(2)
        item = self.stack.pop()
        self.assertEqual(item, 2)

    def test_len(self):
        self.stack.push(3)
        self.stack.push(4)
        self.assertEqual(len(self.stack), 2)

    def test_iter(self):
        items = [5, 6, 7]
        for item in items:
            self.stack.push(item)
        for stack_item, test_item in zip(self.stack, items):
            self.assertEqual(stack_item, test_item)

    def test_reversed(self):
        items = [5, 6, 7]
        for item in items:
            self.stack.push(item)
        reversed_stack = reversed(self.stack)
        self.assertEqual(list(reversed_stack), [7, 6, 5])

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

### Class Level Fixtures

If you use `.setUpClass()` and `.tearDownClass()` class methods, then you can create class-level fixtures. This type of fixture only runs once per test case class. The `setUPClass()` method runs before the test methods, and `.terDownClass()` runs after all the test methods have run. This behavior is known as a **shared fixture** because all the test methods depend on a single setup and teardown run. ***Note:*** shared fixtures break test isolation.

To demonstrate we will test the below Employee class. We are using `__slots__` because we plan to have a lot of instances of this class and want to save on memory.

```python
class Employee:
    __slots__ = ["name", "age", "job", "salary"]

    def __init__(self, name, age, job, salary):
        self.name = name
        self.age = age
        self.job = job
        self.salary = salary

    def profile(self):
        for attr in self.__slots__:
            print(f"{attr.capitalize()}: {getattr(self, attr)}")
        print()
```

The input data lives in a CSV with content similar to below.

```text
name,age,job,salary
Alice,25,Engineer,50000
Bob,30,Analyst,60000
Jane,35,Manager,80000
John,40,CEO,100000
...
```

We also have a function that reads this file and returns a list of employees.

```python
import csv

class Employee:
    # ...

def from_csv_file(file_path):
    with open(file_path) as file:
        reader = csv.DictReader(file)
        employees = []
        for row in reader:
            employees.append(
                Employee(
                    name=row["name"],
                    age=int(row["age"]),
                    job=row["job"],
                    salary=float(row["salary"]),
                )
            )
        return employees
```

And the test code would look like.

```python
import os
import unittest
from tempfile import NamedTemporaryFile

from employee import from_csv_file

SAMPLE_CSV = """name,age,job,salary
Alice,25,Engineer,50000
Bob,30,Analyst,60000
Jane,35,Manager,80000
John,40,CEO,100000
"""

class TestFromCsvFile(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls.temp_file = NamedTemporaryFile(
            delete=False,
            mode="w",
            newline="",
            suffix=".csv",
        )
        cls.temp_file_name = cls.temp_file.name
        cls.temp_file.write(SAMPLE_CSV)
        cls.temp_file.close()
        cls.employees = from_csv_file(cls.temp_file_name)

    @classmethod
    def tearDownClass(cls):
        os.remove(cls.temp_file_name)

    def test_from_csv_file_total_employees(self):
        self.assertEqual(len(self.employees), 4)

    def test_from_csv_file_employee_attributes(self):
        self.assertEqual(self.employees[0].name, "Alice")
        self.assertEqual(self.employees[0].age, 25)
        self.assertEqual(self.employees[0].job, "Engineer")
        self.assertEqual(self.employees[0].salary, 50000.0)

    def test_from_csv_file_employee_name(self):
        self.assertEqual(self.employees[0].name, "Alice")
        self.assertEqual(self.employees[1].name, "Bob")
        self.assertEqual(self.employees[2].name, "Jane")
        self.assertEqual(self.employees[3].name, "John")

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

To create the temporary file we are using [NamedTemporaryFile](https://docs.python.org/3/library/tempfile.html#tempfile.NamedTemporaryFile) from the [tempfile module](https://docs.python.org/3/library/tempfile.html#module-tempfile) 

### Module-Level Fixtures

To make module level fixtures you need to define the following functions at the module level.

```python
def setUpModule():
    ...

def tearDownModule():
    ...
```

These fixtures run once per module. The setup fixture runs before all the test cases in the module, and the teardown fixture runs after all the test cases in the module have run. If an exception happens in the `setUpModule()` function, then none of the tests in the module will run, and the `tearDownModule()` won’t run either. If the raised exception is a [`SkipTest`](https://docs.python.org/3/library/unittest.html#unittest.SkipTest)exception, then the module will be reported as skipped instead of an error.

Module-level fixtures are useful when you have several `TestCase` subclasses in a module, and some of them will benefit from a common setup and teardown logic.

The classic example is a test module with a few test cases that check for database-related functionalities. These tests may need an active connection to the database, which you can create in the `setUpModule()` function and close in the `tearDownModule()` function.

## Testing With Fake Objects: `unittest.mock`

The `unittest.mock` module provides two base classes that allow you to mock, or simulate external resources, such as files, connections and so on:

1. `Mock` is a generic mock object
2. `MagicMock` is the same as Mock, but includes magic methods

For an example of using a mock object we will test the following code. There is no easy way to test this code because it is dependent on the current day and that can vary based on when the test is being run.

```Python
# weekday.py
import datetime

def is_weekday():
    today = datetime.date.today()
    return 0 <= today.weekday() < 5
```

To test this code with a mock we would do something like:

We are using the `@patch` decorator to create a Mock object around the `datetime` module. This is patching the `datetime` module that you imported in your `weekday` module. This Mock object goes to the `mock_datetime` argument of the underlying test method. We then create fake return values for `.today()` in each test method. 

```python
# test_weekend.py
import datetime
import unittest
from unittest.mock import patch

import weekday

class TestWeekday(unittest.TestCase):
    @patch("weekday.datetime")
    def test_is_weekday(self, mock_datetime):
        mock_datetime.date.today.return_value = datetime.date(2024, 4, 4)
        self.assertTrue(weekday.is_weekday())

    @patch("weekday.datetime")
    def test_is_weekend(self, mock_datetime):
        mock_datetime.date.today.return_value = datetime.date(2024, 4, 6)
        self.assertFalse(weekday.is_weekday())

if __name__ == "__main__":
    unittest.main(verbosity=2)
```

# Improve Your Tests With the Python Mock Object Library

## What is Mocking?

Python mock objects provide deeper insight into your code:

* When functions were called
* How many times they were called
* What arguments were passed

The `Mock` class allows you to imitate real objects, and the `patch()` function lets you temporarily substitute mocks for real objects in your tests.

## The Python Mock Library

Provides the `Mock()` class and the `patch()` method. You can use `path()` as either a decorator or a context manager giving you control over the scope in which the object is mocked. Once the designated scope exits, `path()` will clean up your code by replacing the mocked objects wit their original counterparts.

## The Mock Object

First you instantiate a Mock Instance as in the example below:

```python
>>> from unittest.mock import Mock
>>> mock = Mock()
>>> mock
<Mock id='4561344720'>
```

Once instantiated you can substitute an object in your code with your new Mock by passing it as an argument to a function or by redefining another object:

```python
# Pass mock as an argument to do_something()
do_something(mock)

# Patch the json library
json = mock
```

When you substitute an object in your code with Mock it must look like the real object its replacing. For example if you are mocking the `json` library and your program calls `dumps()`, then your Python mock object must also contain `dumps()` We cover how this happens in the next section.

### Understanding Lazy Attributes and Methods

A `Mock` must simulate any object that it replaces. to achieve such flexibility, it creates its [attributes](https://docs.python.org/3/library/unittest.mock.html#quick-guide) when you access them.

```python
>>> mock.some_attribute
<Mock name='mock.some_attribute' id='4394778696'>
>>> mock.do_something()
<Mock name='mock.do_something()' id='4394778920'>
```

Since Mock can create arbitrary attributes on the fly, its able to replace any object:

```python
>>> json = Mock()
>>> json.dumps()
<Mock name='mock.dumps()' id='4392249776'>
```

The capability of Mock to recursively define other mocks allows for you to use mocks in complex situations:

```python
>>> json = Mock()
>>> json.loads('{"k": "v"}').get("k")
<Mock name='mock.loads().get()' id='4379599424'>
```

### Leveraging Assertions and Inspection

Mock instances store data on how you used them. They allow you to see if you called a method, and how you called a method. There are two ways to use this information.

1. Assertions - allow you to assert that you used an object as expected
2. Inspection - allows you to view special attributes to understand how your application used an object.

```python
>>> from unittest.mock import Mock
>>> json = Mock()

>>> json.loads('{"key": "value"}')
<Mock name='mock.loads()' id='4550144184'>

>>> json.loads.assert_called()
>>> json.loads.assert_called_once()
>>> json.loads.assert_called_with('{"key": "value"}')
>>> json.loads.assert_called_once_with('{"key": "value"}')
>>> json.loads('{"key": "value"}')
<Mock name='mock.loads()' id='4550144184'>
>>> # Below fails because at this point we called .loads() two times
>>> json.loads.assert_called_once()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/path/to/python/unittest/mock.py", line 918, in assert_called_once
    raise AssertionError(msg)
AssertionError: Expected 'loads' to have been called once. Called 2 times.
Calls: [call('{"key": "value"}'), call('{"key": "value"}')].
```

A mock object comes with assertion methods such as:

* `.assert_called()` - Ensues that the mocked method was called.
* `.assert_called_once()` - Checks that you called the method exactly one time.
* `.assert_not_called()` - Ensures that you never called the mocked method.

It also has methods that let you inspect arguments passed to the mocked method. To pass these assertions, you must call the mocked method with the same arguments that you pass to the actual method. This approach can break when you specify the arguments differently in both calls, even if you provide the same argument.

* `.assert_called_with(*args, **kwargs)` - Ensures that you called the mocked method at least once with the specified arguments.
* `.assert_called_once_with(*args, **kwargs)` - Checks that you called the mocked method exactly one time with the specified arguments.

```python
>>> json = Mock()
>>> json.loads(s='{"key": "value"}')
<Mock name='mock.loads()' id='4421942864'>

>>> json.loads.assert_called_with('{"key": "value"}')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/path/to/python/unittest/mock.py", line 939, in assert_called_with
    raise AssertionError(_error_message()) from cause
AssertionError: expected call not found.
Expected: loads('{"key": "value"}')
Actual: loads(s='{"key": "value"}')

>>> json.loads.assert_called_with(s='{"key": "value"}')
```

You are also able to introspect you Mock by accessing special attributes. You can use these special attributes to test that your objects behave as you intended

```python
>>> from unittest.mock import Mock

>>> json = Mock()
>>> json.loads('{"key": "value"}')
<Mock name='mock.loads()' id='4391026640'>
>>> # Number of times you called loads():
>>> json.loads.call_count
1

>>> # The last loads() call:
>>> json.loads.call_args
call('{"key": "value"}')

>>> # List of loads() calls:
>>> json.loads.call_args_list
[call('{"key": "value"}')]

>>> # List of calls to json's methods (recursively):
>>> json.method_calls
[call.loads('{"key": "value"}')]
```

### Managing a Mock's Return Value

Mocks also let you control a functions [return value](https://docs.python.org/3/library/unittest.mock.html#unittest.mock.Mock.return_value).

For example, if the below test code runs on weekend it will fail the test.

```python
# holidays.py
from datetime import datetime

def is_weekday():
    today = datetime.today()
    # Python's datetime library treats Monday as 0 and Sunday as 6
    return (0 <= today.weekday() < 5)

# Test if today is a weekday
assert is_weekday()
```

We work around this with:

```python
from datetime import datetime
from unittest.mock import Mock

# Save a couple of test days
wednesday = datetime(year=2025, month=1, day=1)
sunday = datetime(year=2025, month=1, day=5)

# Mock datetime to control today's date
datetime = Mock()

def is_weekday():
    today = datetime.today()
    # Python's datetime library treats Monday as 0 and Sunday as 6
    return (0 <= today.weekday() < 5)

# Mock .today() to return Wednesday
datetime.today.return_value = wednesday
# Test Wednesday is a weekday
assert is_weekday()

# Mock .today() to return Sunday
datetime.today.return_value = sunday
# Test Sunday is not a weekday
assert not is_weekday()
```

While this is a good example there is a library for mocking `datetime` called [`freezegun`](https://github.com/spulec/freezegun)

Below is an example of Mocking libraries in a separate test file. The example below ***DOES NOT WORK*** because when you import a module in another module, the imported names are bound to that module's namespace. Mocking `datetime` in your test file doesn't affect `dateime` in `is_weekday()` because `holidays.py` has already imported the real `datetime` module.

```python
# test_holidays.py
from datetime import datetime
from unittest.mock import Mock

from holidays import is_weekday

# Save a couple of test days
wednesday = datetime(year=2025, month=1, day=1)
sunday = datetime(year=2025, month=1, day=5)

# Mock datetime to control today's date
datetime = Mock()

# Mock .today() to return Wednesday
datetime.today.return_value = wednesday
# Test Wednesday is a weekday
assert is_weekday()

# Mock .today() to return Sunday
datetime.today.return_value = sunday
# Test Sunday is not a weekday
assert not is_weekday()
```

To correctly mock `datetime` in `holidays.py`, you should patch `datetime` in the namespace where it is used. The `unittest.mock()` library's `patch()` function is useful for this purpose. We will cover this technique in the next section but for now we will continue testing in the same file.

### Managing a Mock's Side Effects



## The `patch()` Function

## Common Mocking Problems

## Avoiding Common Problems Using Specifications



