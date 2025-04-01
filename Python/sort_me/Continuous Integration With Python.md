
# Example Set Up

We start with the state of things as in [this directory](https://github.com/xcomfan/real_python_examples/tree/7a6b8cf15bbc6a2a4db46dd21e97b8cc70cb11a1/ci_with_python/CalculatorLibrary) of the [example repo](https://github.com/xcomfan/real_python_examples). Its just a git repo (in my case subdirectory of git repo) with a single python project in our files.

# Adding Testing

We will be adding flake8 and pytest for our testing. We will also install `pytest-cov` so we can tell what coverage of our code we have with our tests.

To install the needed dependencies use the command `pip install flake8 pytest pytest-cov`.

Good practice is to save what you installed into a `requirements.txt` file using the command `pip freeze > requirements.txt`.

## flake8 testing

To manually run flake8 use the command `flake8 --statistics` (The `statistics` flag give you a summary at the end of the output)

## Unit testing

We are adding this [test file](https://github.com/xcomfan/real_python_examples/blob/main/ci_with_python/CalculatorLibrary/test_calculator.py)

We can run the testing with the command `pytest -v --cov .` (the dot at end keeps the testing to your current directory)

# Connecting Circle CI Pipeline to your project

Start by adding a `.circleci` directory into your project folder and inside this directory will go a [`config.yml` file](https://github.com/xcomfan/real_python_examples/blob/acc6f1dfd5d27da8b87714e9d14f2f5281ad1c08/ci_with_python/CalculatorLibrary/.circleci/config.yml) . 

With the `config.yml` file committed to your repo go to [circleci](https://circleci.com/) and log in (had to create an account)

I did not follow the full setup from Circle Ci side as my repo structure is a bit funky for it, and its pretty self explanatory on how to get things to work there.




