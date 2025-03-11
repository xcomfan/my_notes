
#TODO Also go over this [more advanced tutorial](https://realpython.com/python-logging-source-code/)

# The Logging Module

The main component of the `logging` module is the **logger**. You an think of the logger as a reporter in your code that decided what to record, at what level of dtail, and where to store or send those records.

There are 5 logging levels available by default in the [logging module](https://docs.python.org/3/library/logging.html#module-logging/):

* DEBUG
* INFO
* WARNING
* ERROR
* CRITICAL

DEBUG is least severe and CRITICAL is the most severe.

There is also a `NOTSET` log level that is used for custom logging handlers and we will cover later.

Below is a very basic example. Note that the output shows the severity level, along with `root` which is the name the `logging` module gives to its default logger. This output can be configured (which we cover later). In this example we are sending a message to the `root` logger.

```python
>>> import logging
>>> logging.warning("This is only a test.")
WARNING:root:This is only a test.
```

By default the the logging modules logs messages with a severity level or `WARNING` or above. You can change this by configuring the logging module.

```python
>>> logging.debug("This is a debug message")

>>> logging.info("This is an info message")

>>> logging.warning("This is a warning message")
WARNING:root:This is a warning message

>>> logging.error("This is an error message")
ERROR:root:This is an error message

>>> logging.critical("This is a critical message")
CRITICAL:root:This is a critical message```

# Adjusting the Log Level

To set up basic logging configuration and adjust log level, the `logging` module comes with a `basicConfig()` function. (Its camel-cased which is not PEP8 naming convention because its adopted from log4j). 

```python
>>> import logging
>>> logging.basicConfig(level=logging.DEBUG)
>>> logging.debug("This will get logged.")
DEBUG:root:This will get logged.
```

Setting the log level will enable all logging calls at the defined level and higher.

# Formatting the Output

You can enhance your logs with additional data by leveraging the format parameter of `basicConfig()`

```python
>>> import logging
>>> logging.basicConfig(format="%(levelname)s:%(name)s:%(message)s")
>>> logging.warning("Hello, Warning!")
WARNING:root:Hello, Warning!
```

You can define the style of your `format` string with the `style` parameter. The options for style are `%`, `$` or `{`. When you provide a style argument your format string must match the targeted style or you will get a `ValueError`.

***Note:*** Calling `basicConfig()` to configure the `root` logger only works if the `root` logger hasn't been configured before. All `logging` functions automatically call `basicConfig()` without arguments if `basicConfig()` has never been called. So, for example, once you call `logging.debug()`, you will no longer be able to configure the `root` logger with `basicConfig()`

The `format` string can contain a number of [predefined attributes](https://docs.python.org/3/library/logging.html#logrecord-attributes)

In the example below we add a time stamp to our log format and format the timestamp. To learn more about the directives used to format the timestame (`%Y`, `%m` etc.) refer to the [`time.strftime()`](https://docs.python.org/3/library/time.html#time.strftime) documentation.

```python
>>> import logging
>>> logging.basicConfig(
...   format="{asctime} - {levelname} - {message}",
...   style="{",
...   datefmt="%Y-%m-%d %H:%M",
... )
>>> logging.error("Something went wrong!")
2025-03-05 05:11 - ERROR - Something went wrong!
```

# Logging to a File

To save your logs to a file, you can set up your logger's `baseConfig()` with the `filename` argument. Its good practice to set encoding and the [mode](https://docs.python.org/3/library/functions.html#filemodes) the fie should be opened in.

```python
>>> import logging
>>> logging.basicConfig(
...   filename="app.log",
...   encoding="utf-8",
...   filemode="a",
...   format="{asctime} - {levelname} - {message}",
...   style="{",
...   datefmt="%Y-%m-%d %H:%M",
... )
>>>
>>> logging.warning("Save me!")
>>>

>>> ^D

% cat app.log
2025-03-05 05:19 - WARNING - Save me!
```

It may be useful to structure your logs as a CSV file so they can be parsed for aggregating data.

# Displaying Variable Data

By leveraging Python's f-strings you can create debug messages with variable information.

```python
>>> import logging
>>> logging.basicConfig(
...   format="{asctime} - {levelname} - {message}",
...   style="{",
...   datefmt="%Y-%m-%d %H:%M",
...   level=logging.DEBUG
... )
>>> name = "Boris"
>>> logging.debug(f"{name=}")
2025-03-05 05:26 - DEBUG - name='Boris'
```

***Note:*** F-string are eagerly evaluated. That means they are interpolated even if the log message is never handled. If you are interpolating a lot of log messages, you should consider using the module operator (`%`) for string interpolation instead of f-strings. This is supported by `logging` natively as in the example below. This way if the debug message is not handled, then Python will never do the interpolation.

```python
>>> import logging
>>> logging.basicConfig(
...     format="%(asctime)s - %(levelname)s - %(message)s",
...     style="%",
...     datefmt="%Y-%m-%d %H:%M",
...     level=logging.DEBUG,
... )

>>> name = "Samara"
>>> logging.debug("name=%s", name)
2024-07-22 14:51 - DEBUG - name=Samara
```

# Capturing Stack Traces

Exception information can be captured if `exc_info` is set to `True`.

```python
>>> import logging
>>> logging.basicConfig(
...     filename="app.log",
...     encoding="utf-8",
...     filemode="a",
...     format="{asctime} - {levelname} - {message}",
...     style="{",
...     datefmt="%Y-%m-%d %H:%M",
... )

>>> donuts = 5
>>> guests = 0
>>> try:
...     donuts_per_guest = donuts / guests
... except ZeroDivisionError:
...     logging.error("DonutCalculationError", exc_info=True)
...

% cat app.log
2024-07-22 15:04 - ERROR - DonutCalculationError
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ZeroDivisionError: division by zero
```

There is a shortcut available. You can call `logging.exception()` which will logs a message with the lever `ERROR` and adds the exception information about the message. Calling `logging.exception()` is equivalent to calling `logging.error(exc_info=True)`. Since the `logging.exception()` function always dumps exception information, you should only call it from an exception handler. If you want to log the exception at a lever other than `ERROR` use the method in the above example.

```python
>>> try:
...     donuts_per_guest = donuts / guests
... except ZeroDivisionError:
...     logging.exception("DonutCalculationError")
...
```

# Creating a Custom Logger

So far we have worked with the default `root` logger. The downside of working with this default logger is you have to rely on a single instance of `basicConfig()` to configure the logger. For bigger projects you need more flexibility for your logging needs. Generally its a good practice to create your own custom logger by creating an object of the `Logger` class.

## Instantiating Your Logger

You instantiate a Logger class by calling the logging.getLogger() function and providing a name for your logger. You can use any string as a name but its good practice to use `__name__`. That way the your loggers name is always the same as the modules name in the Python package namespace.

```python
>>> import logging
>>> logger = logging.getLogger(__name__)
>>> logger.warning("Look at my logger!")
Look at my logger!
```

Unlike the `root` logger you can't configure the custom logger with `basicConfig()` Instead you have to configure your custom logger using handler and formatters which give you more flexibility.

## Using Handlers

A logger that you create can have one or more handlers. That means you can send your logs to multiple places when they are generated. 

```python
>>> import logging
>>> logger = logging.getLogger(__name__)
>>> console_handler = logging.StreamHandler()
>>> file_handler = logging.FileHandler("app.log", mode="a", encoding="utf-8")
>>> logger.addHandler(console_handler)
>>> logger.addHandler(file_handler)
>>> logger.handlers
[
  <StreamHandler <stderr> (NOTSET)>,
  <FileHandler /Users/RealPython/Desktop/app.log (NOTSET)>
]
>>> logger.warning("Watch out!")
Watch out!
% cat app.log
Watch out!
```

Some notes on the example above. 

The [`StreamHandler`](https://docs.python.org/3/library/logging.handlers.html#streamhandler) will send your logs to the console. The [`FileHandler`](https://docs.python.org/3/library/logging.handlers.html#filehandler) class writes log messages to a file.  There is also [`RotatingFileHandler`](https://docs.python.org/3/library/logging.handlers.html#logging.handlers.RotatingFileHandler) which creates a new log file once a file size limit is reached and [`TimedRotatingFileHander`](https://docs.python.org/3/library/logging.handlers.html#timedrotatingfilehandler) with which you can create a new log file for defined intervals. 

The handlers are how you control where the files go. Formatting is doing via formatters.

## Adding Formatters to Your Handlers

With a formatter you can control the output format by specifying a string format as we did before with the format argument of `logging.basicConfig()`.

Just like with handlers you need to instantiate a class first before working with a formatter using the `Formatter` class of the `logging` module.

```python
>>> import logging
>>> logger = logging.getLogger(__name__)
>>> console_handler = logging.StreamHandler()
>>> file_handler = logging.FileHandler("app.log", mode="a", encoding="utf-8")
>>> logger.addHandler(console_handler)
>>> logger.addHandler(file_handler)
>>> formatter = logging.Formatter(
...    "{asctime} - {levelname} - {message}",
...     style="{",
...     datefmt="%Y-%m-%d %H:%M",
... )

>>> console_handler.setFormatter(formatter)
>>> logger.warning("Stay calm!")
2024-07-22 15:58 - WARNING - Stay calm!
```

***Note:*** Unlike `.addHander()`, which is a method of `Logger` `.setFormatter()` is a method of `Handler` 

With both `console_handler` and `file_handler` aded to `logger`, the `logger.warning()` winds up in the `app.log` file, but because we only set the formatter on `console_handler` the log record in `app.log` remains un-styled.  This is an example of how you can control what each type of logging does.


## Setting the Log Levels of Custom Loggers

First a quick example of seeing the log level a logger is set to.

```python
>>> import logging
>>> logger = logging.getLogger(__name__)
>>> logger.level
0
>>> logger
<Logger __main__ (WARNING)>
>>> logger.parent
<RootLogger root (WARNING)>
>>> logger.getEffectiveLevel()
30
>>> logger.parent.level
30
```

Few notes on the above example:

* The default log level is 0 which stands for `NOTSET`
* The reason we see `WARNING` in the string representation is because its inherited from the parent.
* For any logger we can use the `getEffectiveLevel()` to get the logging level.

Numeric representation of Log Levels are:

| Numeric Value | Log Level  |
| ------------- | ---------- |
| 0             | `NOTSET`   |
| 10            | `DEBUG`    |
| 20            | `INFO`     |
| 30            | `WARNING`  |
| 40            | `ERROR`    |
| 50            | `CRITICAL` |
You can use either numeric value or log level string to se the level of your custom logger:

```python
>>> logger.setLevel(10)
>>> logger
<Logger __main__ (DEBUG)>

>>> logger.setLevel("INFO")
>>> logger
<Logger __main__ (INFO)>
>>> formatter = logging.Formatter("{levelname} - {message}", style="{")
>>> console_handler = logging.StreamHandler()
>>> console_handler.setFormatter(formatter)
>>> logger.addHandler(console_handler)
>>> logger.debug("Just checking in!")
>>> logger.info("Just checking in, again!")
INFO - Just checking in, again!
>>> console_handler
<StreamHandler <stderr> (NOTSET)>
>>> console_handler.setLevel("DEBUG")
>>> logger.debug("Just checking in!")
>>> console_handler
<StreamHandler <stderr> (DEBUG)>
```

Few notes on the above example:

 The debug message doesn't show, even though you allowed `console_log` to show log records for the level `DEBUG` and above. This behavior of logger and its handlers can be confusing. ***Note:*** You define the lowest allowed log level for the logger itself. Handlers can't show logs lower than the defined log level of the logger they are connected to.

Due to this behavior it can be helpful during developmetn to set your logger's log level to `DEBUG` and let each handler decide their lows log level. This is shown int he example below.

```python
>>> import logging
>>> logger = logging.getLogger(__name__)
>>> logger.setLevel("DEBUG")
>>> formatter = logging.Formatter("{levelname} - {message}", style="{")

>>> console_handler = logging.StreamHandler()
>>> console_handler.setLevel("DEBUG")
>>> console_handler.setFormatter(formatter)
>>> logger.addHandler(console_handler)

>>> file_handler = logging.FileHandler("app.log", mode="a", encoding="utf-8")
>>> file_handler.setLevel("WARNING")
>>> file_handler.setFormatter(formatter)
>>> logger.addHandler(file_handler)

>>> logger.debug("Just checking in!")
DEBUG - Just checking in!

>>> logger.warning("Stay curious!")
WARNING - Stay curious!

>>> logger.error("Stay put!")
ERROR - Stay put!

% cat app.log
WARNING - Stay curious!
ERROR - Stay put!
```

# Filtering Logs

For most use cases you are fine with reading logs of a certain level or above. However if you need to handle messages of a specific log level in a special way that is when using [Filter](https://docs.python.org/3/library/logging.html#logging.Filter) is useful.

The important part about the specification for the `Filter` object is; the filtering logic will check to see if the filter object has a `filter` attribute: if it does, its assumed to ba a `Filter` and its `filter()` method is called. Otherwise, it's assumed to be a callable and called witht he record as the single parameter. The returned value should conform to that returned by `filter()` ([Source](https://docs.python.org/3/library/logging.html#logging.Filter))

In other words here are 3 ways to creating filters for logging:

1. Create a **Subclass** of `logging.Filter()` and overwrite the `.filter()` method.
2. Create a **Class** that contains a `.filter()` method
3. Create a **Callable** that resembles a `.filter()` method.

```python
>>> import logging
>>> def show_only_debug(record):
...     return record.levelname == "DEBUG"
...

>>> logger = logging.getLogger(__name__)
>>> logger.setLevel("DEBUG")
>>> formatter = logging.Formatter("{levelname} - {message}", style="{")

>>> console_handler = logging.StreamHandler()
>>> console_handler.setLevel("DEBUG")
>>> console_handler.setFormatter(formatter)
>>> console_handler.addFilter(show_only_debug)
>>> logger.addHandler(console_handler)

>>> file_handler = logging.FileHandler("app.log", mode="a", encoding="utf-8")
>>> file_handler.setLevel("WARNING")
>>> file_handler.setFormatter(formatter)
>>> logger.addHandler(file_handler)

>>> logger.debug("Just checking in!")
DEBUG - Just checking in!

>>> logger.warning("Stay curious!")
>>> logger.error("Stay put!")
```

In above example we are creating a callable named `show_only_debug()` with a record parameter. The passed in record will be a [`LogRecord`](https://docs.python.org/3/library/logging.html#logrecord-objects) We than add the filter to our handler using the [`.addFilter()`](https://docs.python.org/3/library/logging.html#logging.Handler.addFilter) method of the `Handler` class. 

# Configuring Loggers Using Files and Dictionaries

## Using a File

Below is a sample configuration file for a logger.

```text
[loggers]
keys=root,sampleLogger

[handlers]
keys=consoleHandler

[formatters]
keys=sampleFormatter

[logger_root]
level=DEBUG
handlers=consoleHandler

[logger_sampleLogger]
level=DEBUG
handlers=consoleHandler
qualname=sampleLogger
propagate=0

[handler_consoleHandler]
class=StreamHandler
level=DEBUG
formatter=sampleFormatter
args=(sys.stdout,)

[formatter_sampleFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
```

You can load the above configuration file with the code below.

```python
import logging
import logging.config

logging.config.fileConfig(fname='file.conf', disable_existing_loggers=False)

logger = logging.getLogger(__name__)
logger.debug('This is a debug message`)
```

* the `disable_existing_loggers=False` is needed because by default if other loggers are being used they would be disabled.

## Using A Dictionary

In this example we read the below YAML file into a dictionary and configure the logger that way

```yaml
version: 1
formatters:
  simple:
    format: '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
handlers:
  console:
    class: logging.StreamHandler
    level: DEBUG
    formatter: simple
    stream: ext://sys.stdout
loggers:
  sampleLogger:
    level: DEBUG
    handlers: [console]
    propagate: no
root:
  level: DEBUG
  handlers: [console]
  ```

```python
import logging
import logging.config
import yaml

with open('config.yaml', 'r') as f:
    config = yaml.safe_load(f.read())
    logging.config.dictConfig(config)

logger = logging.getLogger(__name__)

logger.debug('This is a debug message')
```