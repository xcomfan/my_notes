
# Visual Studio Code

## Extensions to Install

### [Microsoft's Python Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

This extension supports:
* IntelliSense code completion
* Linting
* Debugging
* Code snippets
* Unit testing
* Automatic use of conda virtual environments
* Code editing in Jupyter environments and Jupyter Notebooks

## Python Linting

[This](https://code.visualstudio.com/docs/python/linting) covers how to set up your choice of Python linter.

#TODO read above doc and set it up the way you prefer.

## Virtual Environment Support

#TODO need to try this out

VS Code understand and will use any virtualenv, pipenv or conda environment it sees when you open a project.

## Testing Support

VS Code can automatically recognize existing Python tests written in the `unittes`, `pytest`, or `Nose` frameworks. 

To run your existing unit tests; right click on a file in the explorer and select "Run Tests"

#TODO I did not see exactly what the doc was showing and the sample project did not have any tests so will have to try this later.

## Debugging Support

VS Code can support the debugging features:

* Automatic variable tracking
* Watch expressions
* Breakpoints
* Call stack inspection

The debugger can control Python apps running in the build-in terminal or an external terminal instance. It can attach to any already running Python instance, and can even debug Django and Flask apps.

You can start the debugger with `F5`. `F10`and `F11`can be used to step over and into functions. `SHIFT` + `F5`will exit the debugger. Breakpoints are set with `F9` You can also bring up the debug view from the left hand pane.

For debugging more complicated project you will need to set up a configuration which is accessible from the debug view.  When you do this VS code will create a file for you called `launch.json`which you can use to tune your debugger.

