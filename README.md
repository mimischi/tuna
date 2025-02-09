<p align="center">
  <a href="https://github.com/nschloe/tuna"><img alt="tuna" src="https://nschloe.github.io/tuna/logo-with-text.svg" width="50%"></a>
  <p align="center">Performance analysis for Python.</p>
</p>

[![CircleCI](https://img.shields.io/circleci/project/github/nschloe/tuna/master.svg?style=flat-square)](https://circleci.com/gh/nschloe/tuna)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg?style=flat-square)](https://github.com/ambv/black)
[![PyPi Version](https://img.shields.io/pypi/v/tuna.svg?style=flat-square)](https://pypi.org/project/tuna)
[![GitHub stars](https://img.shields.io/github/stars/nschloe/tuna.svg?style=flat-square&logo=github&label=Stars&logoColor=white)](https://github.com/nschloe/tuna)
[![PyPi downloads](https://img.shields.io/pypi/dd/tuna.svg?style=flat-square)](https://pypistats.org/packages/tuna)

tuna is a modern, lightweight Python profile viewer inspired by the amazing
[SnakeViz](https://github.com/jiffyclub/snakeviz). It handles runtime and import
profiles, is rather small, uses [d3](https://d3js.org/) and
[bootstrap](https://getbootstrap.com/), and avoids
[certain](https://github.com/jiffyclub/snakeviz/issues/111)
[errors](https://github.com/jiffyclub/snakeviz/issues/112) present in SnakeViz.

Create a runtime profile with
```
python -mcProfile -o program.prof yourfile.py
```
or an [import
profile](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPROFILEIMPORTTIME)
with
```
python -X importtime yourfile.py 2> import.log
```
and show it with
```
tuna program.prof
```

![](https://nschloe.github.io/tuna/screencast.gif)


### Why tuna doesn't show the whole call tree

The whole timed call tree _cannot_ be retrieved from profile data. Python developers
made the decision to only store _parent data_ in profiles because it can be computed
with little overhead. To illustrate, consider the following program.
```python
import time


def a(t0, t1):
    c(t0)
    d(t1)
    return


def b():
    return a(1, 4)


def c(t):
    time.sleep(t)
    return


def d(t):
    time.sleep(t)
    return


if __name__ == "__main__":
    a(4, 1)
    b()
```
The root process (`__main__`) calls `a()` which spends 4 seconds in `c()` and 1 second
in `d()`. `__main__` also calls `b()` which calls `a()`, this time spending 1 second in
`c()` and 4 seconds in `d()`. The profile, however, will only store that `c()` spent a
total of 5 seconds when called from `a()`, and likewise `d()`. The information that the
program spent more time in `c()` when called in `root -> a() -> c()` than when called in
`root -> b() -> a() -> c()` is not present in the profile.

tuna only displays the part of the timed call tree that can be deduced from the profile:
![](https://nschloe.github.io/tuna/foo.png)

### Installation

tuna is [available from the Python Package Index](https://pypi.org/project/tuna/), so
simply type
```
pip3 install tuna --user --upgrade
```
to install or upgrade.


### Testing

To run the tuna unit tests, check out this repository and type
```
pytest
```

### License

tuna is published under the [MIT license](https://en.wikipedia.org/wiki/MIT_License).
