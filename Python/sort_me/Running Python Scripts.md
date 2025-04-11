
## Running Scripts From Python Code

### Using the `importlib` Standard-Library Module

The `importlib` module provides the `import_module()` function which allows you to programmatically import functions. This lets you emulate an import operation and execute any module or script. 

```python
>>> import importlib
>>> importlib.import_module("hello")
Hello, World!
<module 'hello' from '/home/username/hello.py'>
```

The `import_module()` function imports a module into your current namespace and runs any executable code that the target module contains.

Since you cannot import a module twice thus if you need to reload the module you use the `reload()` method. ***Note:*** The argument to `reload()` has to be the name of a module object **not** a string. To use `reload()` you need to provide a module that's already imported.

```python
>>> import hello
Hello World!

>>> import importlib
>>> importlib.reload(hello)
Hello World!
<module 'hello' from '/home/username/hello.py'>
```

### Using the Built-in `exec()` Function

