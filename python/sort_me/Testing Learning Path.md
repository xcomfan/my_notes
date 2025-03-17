
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




