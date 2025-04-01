
GCD is greatest common divisor: largest number that divides two other numbers.

LCM is the least common multiple: smallest number that can be divided by two other numbers.

```python
>>> from math import gcd, lcm
>>> gcd(49, 14)
7
>>> lcm(49, 14)
98
>>> # before python 3.9 for multiples numbers you would...
>>> from functools import reduce
>>> reduce(gcd, [273, 1729, 6048])
7
>>> # after Python 3.9 you can ...
>>> gcd(273, 1729, 6048)
7
```

