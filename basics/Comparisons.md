 # About Comparisons

A [comparison operator][comparisons] in Python (_also called a Python relational operator_), looks at the _values_ of two [operands][operand] and returns a boolean `True` or `False` if the `comparison` condition is or is not met.

The table below shows the most common Python comparison operators:

| Operator | Operation                  | Description                                                               |
| -------- | -------------------------- | ------------------------------------------------------------------------- |
| `>`      | "greater than"             | `a > b` is `True` if `a` is **strictly** greater in value than `b`        |
| `<`      | "less than"                | `a < b` is `True` if `a` is **strictly** less in value than `b`           |
| `==`     | "equal to"                 | `a == b` is `True` if `a` is **strictly** equal to `b` in value           |
| `>=`     | "greater than or equal to" | `a >= b` is `True` if `a > b` OR `a == b` in value                        |
| `<=`     | "less than or equal to"    | `a <= b` is `True` if `a < b` or `a == b` in value                        |
| `!=`     | "not equal to"             | `a != b` is `True` if `a == b` is `False`                                 |
| `is`     | "identity"                 | `a is b` is `True` if **_and only if_** `a` and `b` are the same _object_ |
| `is not` | "negated identity"         | `a is not b` is `True` if `a` and `b` are **not** the same _object_       |
| `in`     | "containment test"         | `a in b` is `True` if `a` is member, subset, or element of `b`            |
| `not in` | "negated containment test" | `a not in b` is `True` if `a` is not a member, subset, or element of `b`  |

They all have the same priority (_which is higher than that of [Boolean operations][boolean operations], but lower than that of arithmetic or bitwise operations_).

## Comparison between different data types

Objects that are different types (_except numeric types_) never compare equal by default.
Non-identical instances of a `class` will also _**not**_ compare as equal unless the `class` defines special [rich comparison][rich comparisons] methods that customize the default `object` comparison behavior.
For (much) more detail, see [Value comparisons][value comparisons] in the Python documentation.

Numeric types are (mostly) an exception to this type matching rule.
An `integer` **can** be considered equal to a `float` (_or an [`octal`][octal] equal to a [`hexadecimal`][hex]_), as long as the types can be implicitly converted for comparison.

For the other numeric types ([complex][complex numbers], [decimal][decimal numbers], [fractions][rational numbers]), comparison operators are defined where they "make sense" (_where implicit conversion does not change the outcome_), but throw a `TypeError` if the underlying objects cannot be accurately converted for comparison.
For more information on the rules that Python uses for numeric conversion, see [arithmetic conversions][arithmetic conversions] in the Python documentation.

```python
>>> import fractions

# A string cannot be converted to an int.
>>> 17 == '17'
False

# An int can be converted to float for comparison.
>>> 17 == 17.0
True

# The fraction 6/3 can be converted to the int 2
# The int 2 can be converted to 0b10 in binary.
>>> 6/3 == 0b10
True

# An int can be converted to a complex 
# number with a 0 imaginary part.
>>> 17 == complex(17)
True

# The fraction 2/5 can be converted to the float 0.4
>>> 0.4 == 2/5
True

>>> complex(2/5, 1/2) == complex(0.4, 0.5)
True
```

Any ordered comparison of a number to a `NaN` (_not a number_) type is `False`.
A confusing side effect of Python's `NaN` definition is that `NaN` never compares equal to `NaN`.
If you are curious as to why `NaN` was defined this way in Python, this [Stack Overflow Post on NaN][so nan post] around the setting of the international standard is an interesting read.

```python
>>> x = float('NaN')

>>> 3 < x
False

>>> x < 3
False

# NaN never compares equal to NaN
>>> x == x
False
```

## Comparing Strings

Strings (`str`) are compared [_lexicographically_][lexographic order], using their individual Unicode code points (_the result of passing each code point in the `str` to the built-in function [`ord()`][ord], which returns an `int`_).
If all code points in both strings match and are _**in the same order**_, the two strings are considered equal.
This comparison is done in a 'pair-wise' fashion - first-to-first, second-to-second, etc.
In Python 3.x, `str` and `bytes` cannot be directly coerced/compared.

```python
>>> 'Python' > 'Rust'
False

>>> 'Python' > 'JavaScript'
True

# Examples with Mandarin.
# hello < goodbye
>>> '你好' < '再见'
True

# ord() of first characters
>>> ord('你'), ord('再')
(20320, 20877)

# ord() of second characters
>>> ord('好'), ord('见')
(22909, 35265)

# And with Korean words.
# Pretty < beautiful.
>>> '예쁜' < '아름다운'
False

>>> ord('예'), ord('아')
(50696, 50500)
```

## Comparing Container Data Types

Container data types (_`lists`, `tuples`, `sets`, `dicts`, etc._) also compare [_lexicographically_][lexographic order] - they are equal if both containers have the same data **and** the same data types (_in the case of `lists` and `tuples`, they must also have the same **ordering**_), unequal otherwise.

```python
>>> [1, 2] == [1, 2]
True

# But if the data is not in the same order, they are not equal.
>>> [2, 1] == [1, 2]
False

# The same holds true for tuples
>>> (3,4,5) == (5,4,3)
False

# Length is also compared
>>> [1, 2] < [1, 2, 3]
True

# Comparing dicts
>>> {'name': 'John', 'age': 19} == {'name': 'John', 'age': 18}
False

>>> {'name': 'John', 'age': 19} == {'name': 'John', 'age': 19}
True
```

## Comparison Chaining

Comparison operators can be chained _arbitrarily_.
Note that the evaluation of an expression takes place from `left` to `right`.
For example, `x < y <= z` is equivalent to `x < y` `and` `y <= z`, except that `y` is evaluated **only once**.
In both cases, `z` is _not_ evaluated **at all** when `x < y` is found to be `False`.
This is often called `short-circuit evaluation` - the evaluation stops if the truth value of the expression has already been determined.

`Short circuiting` is supported by various boolean operators, functions, and also by comparison chaining in Python.
Unlike many other programming languages, including `C`, `C++`, `C#`, and `Java`, chained expressions like `a < b < c` in Python have a conventional [mathematical interpretation][three way boolean comparison] and precedence.

```python
>>> x = 2
>>> y = 5
>>> z = 10

>>> x < y < z
True

>>> x < y > z
False

>>> x > y < z
False
```

## Identity comparisons

The operators `is` and `is not` test for object [_identity_][object identity], as opposed to object _value_.
An object's identity never changes after creation and can be found by using the [`id()`][id function] function.

`<apple> is <orange>` evaluates to `True` if _**and only if**_ `id(<apple>)` == `id(<orange>)`.
`<apple> is not <orange>` yields the inverse.

Due to their singleton status, `None` and `NotImplemented` should always be compared to items using `is` and `is not`.
See the Python reference docs on [value comparisons][value comparisons none] and [PEP8][PEP8 programming recommendations] for more details on this convention.

```python
>>> 
# A list of favorite numbers.
>>> my_fav_numbers = [1, 2, 3]

>>> your_fav_numbers = my_fav_numbers

>>> my_fav_numbers is your_fav_numbers
True

# The returned id will differ by system and Python version.
>>> id(my_fav_numbers)
4517478208

# your_fav_numbers is only an alias pointing to the original my_fav_numbers object.
# Assigning a new name does not create a new object.
>>> id(your_fav_numbers)
4517478208


>>> my_fav_numbers is not your_fav_numbers
False

>>> my_fav_numbers is not None
True

>>> my_fav_numbers is NotImplemented
False
```

## Membership comparisons

The operators `in` and `not in` test for _membership_.
`<fish> in <soup>` evaluates to `True` if `<fish>` is a member of `<soup>` (_if `<fish>` is a subset of or is contained within `<soup>`_), and evaluates `False` otherwise.
`<fish> not in <soup>` returns the negation, or _opposite of_ `<fish> in <soup>`.

For string and bytes types, `<name> in <fullname>` is `True` _**if and only if**_ `<name>` is a substring of `<fullname>`.

```python
>>> 
# A set of lucky numbers.
>>> lucky_numbers = {11, 22, 33}
>>> 22 in lucky_numbers
True

>>> 44 in lucky_numbers
False

# A dictionary of employee information.
>>> employee = {'name': 'John Doe', 'id': 67826, 'age': 33, 'title': 'ceo'}

# Checking for the membership of certain keys.
>>> 'age' in employee
True

>>> 33 in employee
False

>>> 'lastname' not in employee
True

# Checking for substring membership
>>> name = 'Super Batman'
>>> 'Bat' in name
True

>>> 'Batwoman' in name
False
```

# Customizing comparison behavior

Comparison behavior for objects can be customized through the implementation of `rich comparison methods`.
For more information, see [Python Tutorial: classes][classes], [Python Classes and Magic Methods (Dan Bader)][magic methods], and [Special method names][dunder methods].

[PEP8 programming recommendations]: https://pep8.org/#programming-recommendations
[arithmetic conversions]: https://docs.python.org/3/reference/expressions.html?highlight=number%20conversion#arithmetic-conversions
[boolean operations]: https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not
[classes]: https://docs.python.org/3/tutorial/classes.html#classes
[comparisons]: https://docs.python.org/3/library/stdtypes.html?highlight=comparisons#comparisons
[complex numbers]: https://docs.python.org/3/library/functions.html#complex
[decimal numbers]: https://docs.python.org/3/library/decimal.html
[dunder methods]: https://docs.python.org/3/reference/datamodel.html#special-method-names
[hex]: https://docs.python.org/3/library/functions.html?highlight=hex#hex
[id function]: https://docs.python.org/3/library/functions.html#id
[lexographic order]: https://en.wikipedia.org/wiki/Lexicographic_order
[magic methods]: https://dbader.org/blog/python-dunder-methods
[object identity]: https://docs.python.org/3/reference/datamodel.html
[octal]: https://docs.python.org/3/library/functions.html?#oct
[operand]: https://www.computerhope.com/jargon/o/operand.htm
[ord]: https://docs.python.org/3/library/functions.html#ord
[rational numbers]: https://docs.python.org/3/library/fractions.html
[rich comparisons]: https://docs.python.org/3/reference/datamodel.html#object.__lt__
[so nan post]: https://stackoverflow.com/questions/1565164/what-is-the-rationale-for-all-comparisons-returning-false-for-ieee754-nan-values
[three way boolean comparison]: https://en.wikipedia.org/wiki/Three-way_comparison
[value comparisons none]: https://docs.python.org/3/reference/expressions.html?highlight=none#value-comparisons
[value comparisons]: https://docs.python.org/3/reference/expressions.html?highlight=nan#value-comparisons






