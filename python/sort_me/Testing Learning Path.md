
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

```
#### nose

#### pytest
