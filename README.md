# Module 16: Pandas

This module introduces the _Python Data Analysis_ library [**`pandas`**](http://pandas.pydata.org/)&mdash;a set of modules, functions, and classes used to for easily and efficiently performing data analysis&mdash;`panda`'s speciality is its highly optimized performance when working with large data sets. `pandas` is the most common library used with Python for Data Science (and mirrors the `R` language in many ways, allowing programmers to easily move between the two).
In this module, we will discuss the two main data structures used by `pandas` (_Series_ and _DataFrames_) and how to use them to organize and work with data.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Contents**

- [Resources](#resources)
- [Setup](#setup)
- [Series](#series)
  - [Series Operations and Methods](#series-operations-and-methods)
  - [Accessing Series](#accessing-series)
- [Data Frames](#data-frames)
  - [DataFrame Operations and Methods](#dataframe-operations-and-methods)
  - [Accessing DataFrames](#accessing-dataframes)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Resources
- [10 minutes to pandas (pandas docs)](http://pandas.pydata.org/pandas-docs/stable/10min.html) a basic set of examples
- [Tutorials (pandas docs)](http://pandas.pydata.org/pandas-docs/stable/tutorials.html) a list and guide to various tutorials (of mixed quality)
- [Intro to Data Structure (pandas docs)](http://pandas.pydata.org/pandas-docs/stable/dsintro.html)
- [Essential Basic Functionality (pandas docs)](http://pandas.pydata.org/pandas-docs/stable/basics.html) not really basic, but a complete set of examples
- [Pandas. Data Processing (Data Analysis in Python)](http://dataanalysispython.readthedocs.io/en/latest/pandas.html)
- [pandas Foundations (DataCamp)](https://www.datacamp.com/courses/pandas-foundations/)

## Setup
`pandas` is a **third-party** library (not built into Python!), but is included by default with Anaconda and so can be imported directly. Additionally, Pandas is built on top of the [`numpy`](http://www.numpy.org/) scientific computing library which supports highly optimized mathematical operations. Thus many `pandas` operations involve working with `numpy` data structures, and the `pandas` library requires `numpy` (also included in Anaconda) to also be imported:

```python
# import libraries
import pandas as pd  # standard shortcut names
import numpy as np
```

- We usually `import` the module and reference types and methods using dot notation, rather than importing them into the global namespace.

- Note that this module will focus primarily on `pandas`, leaving `numpy`-specific data structures and functions for the reader to explore.


## Series
The first basic `pandas` data structure is a [**Series**](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.html). A Series represents a _one-dimensional ordered collection of values_, making them somewhat similar to a regular Python _list_. However, elements can also be given _labels_ (called the **index**), which can be non-numeric values, similar to a _key_ in a Python _dictionary_. This makes a Series somewhat like an ordered dictionary&mdash;one that supports additional methods and efficient data-processing behaviors.

Series can be created using the `Series()` function (a _constructor_ for instances of the class):

```python
# create a Series from a list
number_series = pd.Series([1, 2, 2, 3, 5, 8])
print(number_series)
```

produces

```
0    1
1    2
2    2
3    3
4    5
5    8
dtype: int64
```

Printing a Series will display it like a _table_: the first value in each row is the **index** (label) of that element, and the second is the value of the element in the Series.

- Printing will also display the _type_ of the elements in the Series. All elements in the Series will be treated as "same" type&mdash;if you create a Series from mixed elements (e.g., numbers and strings), the type will be the a generic `object`. In practice, we almost always create Series from a single type.

If we create a Series from a list, each element will be given an _index_ (label) that is that values's index in the list. We can also create a Series from a _dictionary_, in which case the keys will be used as the index labels:

```python
# create a Series from a dictionary
age_series = pd.Series({'sarah':42, 'amit':35, 'zhang':13})
print(age_series)
```
```
amit     35
sarah    42
zhang    13
dtype: int64
```

- Note that the Series is automatically **sorted** by the keys of the dictionary! This means that the order of the elements in the Series will always be the same for a given dictionary (which cannot be said for the dictionary items themselves).


### Series Operations and Methods
The main benefit of Series (as opposed to normal lists or dictionaries) is that they provide a number of operations and methods that make it easy to consider and modify the entire Series, rather than needing to worth with each element individually. In a way, the functions include built-in _mapping_, _reducing_, and _filtering_ style operations.

In particular, basic operators (whether math operators such as `+` and `-`, or relational operators such as `>` or `==`) function as **vectorized operations**, meaning that they are applied to the entire Series **member-wise**: the operation is applied to the first element in the Series, then the second, then the third, and so forth:

```python
sample = pd.Series(range(1,6))  # Series of numbers from 1 to 5 (6 is excluded)
result = sample + 4  # add 4 to each element (produces new Series)
print(result)
    # 0    5
    # 1    6
    # 2    7
    # 3    8
    # 4    9
    # dtype: int64

is_greater_than_3 = sample > 3  # compare each element
print(is_greater_than_3)
    # 0    False
    # 1    False
    # 2    False
    # 3     True  # note index and value are not the same
    # 4     True
    # dtype: bool
```

- Having a Series operation apply to a _scalar_ (a single value) is referred to as [**broadcasting**](https://docs.scipy.org/doc/numpy/user/basics.broadcasting.html). The idea is that the smaller "set" of elements (e.g., a single value) is _broadcast_ so that it has a comparible size, thereby allowing different "sized" data structures to interact. Technically, operating on a Series with a _scalar_ is actually a specific case of operating on it with another Series!

If the second operand is _another Series_, then mathematical and relational operations are still applied **member-wise**, with the elements of each operand being "matched" by their index label. This means that for most Series whose indices are

```python
s1 = pd.Series([2, 2, 2, 2, 2])
s2 = pd.Series([1, 2, 3, 4, 5])

# examples of operations (list only includes values)
list(s1 + s2)  # [3, 4, 5, 6, 7]
list(s1 / s2)  # [2.0, 1.0, 0.66666666666666663, 0.5, 0.40000000000000002]
list(s1 < s2)  # [False, False, True, True, True]

# add a Series to itself (why not?)
list(s2 + s2)  # [2, 4, 6, 8, 10]

# perform more advanced arithmetic!
s3 = (s1 + s2) / (s1 + s1)
list(s3)  # [0.75, 1.0, 1.25, 1.5, 1.75]
```

And note that these operations will be _fast_, even for very large Series, allowing for effective data manipulations.

`pandas` Series also include a number of [_methods_](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.html) for inspecting and manipulating the data. Some useful examples (not comprehensive):

| Function | Description |
| --- | --- |
| `index` | an _attribute_; the sequence of index labels (convert to a _list_ to use) |
| `head(n)` | returns a Series containing only the first `n` elements |
| `tail(n)` | returns a Series containing only the last `n` elements |
| `any()` | returns whether ANY of the elements are `True` (or "truthy") |
| `all()` | returns whether ALL of the elements are `True` (or "truthy") |
| `mean()` | returns the statistical mean of the elements in the Series |
| `std()` | returns the standard deviation of the elements in the Series |
| `describe()` | returns a Series of [descriptive statistics](http://pandas.pydata.org/pandas-docs/stable/basics.html#descriptive-statistics) |
| `idxmax()` | returns the index label of the element with the max value |

Series support many more methods as well: see the [full documentation](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.html) for a complete list.

One particularly useful method to mention is the [`apply()`](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.apply.html#pandas.Series.apply) method. This method is used to _apply_ a particular **callback function** to each element in the series. This is a _mapping_ operation, similar to what we've done with the `map()` function:

```python
def square(n):  # a function that squares a number
    return n**2

number_series = pd.Series([1,2,3,4,5])  # an initial series

square_series = number_series.apply(square)
list(square_series)  # [1, 4, 9, 16, 25]

# can also apply built-in functions
import math
sqrt_series = number_series.apply(math.sqrt)
list(sqrt_series)  # [1.0, 1.4142135623730951, 1.7320508075688772, 2.0, 2.2360679774997898]

# pass additional arguments as keyword args (or `args` for a single argument)
cubed_series = number_series.apply(math.pow, args=(3,)) # call math.exp(n, 3) on each
list(cubed_series)  # [1.0, 8.0, 27.0, 64.0, 125.0]
```


### Accessing Series
Just like lists and dictionaries, elements in a Series can be accessed using **bracket notation**, putting the index label inside the brackets:

```python
number_series = pd.Series([1, 2, 2, 3, 5, 8])
age_series = pd.Series({'sarah':42, 'amit':35, 'zhang':13})

# get the 1th element from the number_series
number_series[1]  # 2

# get the 'sarah' element from age_series
age_series['amit']  # 35

# get the 0th element from age_series
# (Series are ordered, so can be accessed positionally)
age_series[0]  # 42
```

Note that the returned values are not technically basic `int` or `float` or `string` types, but are rather specific `numpy` objects that work almost identically to their normal type (but with some additional optimization).

You can also use list-style _slices_ using the colon operator (e.g., elements **`1:3`**).

 it is also possible to specify ___a sequence of indicies___ (i.e., a _list_ or _range_ or even a _Series_ of indices) to access using bracket notation. This will produce a new Series object that contains only the elements that have those labels:

```python
age_series = pd.Series({'sarah':42, 'amit':35, 'zhang':13})

index_list = ['sarah', 'zhang']
print( age_series[index_list] )
    # sarah    42
    # zhang    13
    # dtype: int64

# using an anonymous variable for the index list (notice the brackets!)
print( age_series[['sarah', 'zhang']] )
    # sarah    42
    # zhang    13
    # dtype: int64
```

This also means that you can use something like a _list comprehension_ to (or even a Series operation!) to determine which elements to select from a Series!

```python
letter_series = pd.Series(['a','b','c','d','e','f'])
even_numbers = [num for num in range(0,6) if num%2 == 0]  # list of even numbers

# get letters with even numbered indices
letter_series[even_numbers]  # []
    # 0    a
    # 2    c
    # 4    e
    # dtype: object

# in one line (check the brackets!)
letter_series[[num for num in range(0,6) if num%2 == 0]]
```

Finally, using a ___sequence of booleans___ with bracket notatoin will produce a new Series containing the elements whose position _corresponds_ with `True` values. This is called **boolean indexing**.

```python
shoe_sizes = pd.Series([7, 6.5, 4, 11, 8])  # a series of shoe sizes
index_filter = [True, False, False, True, True]  # list of which elements to extract

# extract every element in an index that is True
shoe_sizes[index_filter]  # has values 7.0, 11.0, 8.0
```

- In this example, since `index_filter` is `True` at index 0, 3, and 4, then `shoe_sizes[index_filter]` returns a Series with the elements from index numbers 0, 3, and 4.

This is incredibly powerful because it allows us to easily perform **filtering** operations on a Series:

```python
shoe_sizes = pd.Series([7, 6.5, 4, 11, 8])  # a series of shoe sizes
big_sizes = shoe_sizes > 6.5  # has values True, False, False, True, True

big_shoes = shoe_sizes[big_sizes]  # has values 7, 11, 8

# as one line
big_shoes = shoe_sizes[shoe_size > 6.5]
```

- You can think of the last statement as saying _shoe sizes **where** shoe size is greater than 6.5_.

- You can include _logical operators_ ("and" and "or") by using the operators `&` for "and" and `|` for "or". Be sure to wrap each relational expression in `()` to enforce order of operations.

While it is perfectly possible to do similar filtering with a list comprehension, the boolean indexing expression can be very simple to read and runs quickly. (This is also the normal style of doing filtering in the `R` programming language).


## Data Frames
The most common data structure used in `pandas` (more common than Series) is a [**DataFrame**](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). A DataFrame represents a **table**, where data is organized into rows and columns. You can think of a DataFrame as being like a Excel spreadsheet or a SQL table.

- We have previously represented tabular data using a _list of dictionaries_. However, this required us to be careful to make sure that all of the dictionaries shared keys, and did not offer easy ways to interact with the table in terms of its rows or columns. DataFrames give us that functionality!

A DataFrame can also be understood as a _dictionary of Series_, where each Series represents a **column** of the table. The keys of this dictionary are the _index labels_ of the columns, while the the index labels of the Series serve as the labels for the row.

- This is distinct from spreadsheets or SQL tables, which are often seen as a collection of _observations_ (rows). Programmatically, DataFrames should primarily be considered as a collection of _features_ (columns), which happen to be sequenced to correspond to observations.

A DataFrame can be created using the `DataFrame()` function (a _constructor_ for instances of the class). This function usually takes as an argument _dictionary_ where the values are Series (or values that can be converted into a Series, such as a list or a dictionary):

```python
name_series = pd.Series(['Ada','Bob','Chris','Diya','Emma'])
heights = range(58,63)
weights = [115, 117, 120, 123, 126]

df = pd.DataFrame({'name':name_series, 'height':heights, 'weight':weights})
print(df)
    #    height   name  weight
    # 0      58    Ada     115
    # 1      59    Bob     117
    # 2      60  Chris     120
    # 3      61   Diya     123
    # 4      62   Emma     126
```
- Although DataFrames variables are often named `df` in `pandas` examples, this is ___not___ a good variable name. You should use much more descriptive names for your DataFrames (e.g., `person_size_table`) when used in actual programs.
- You can specify the order of columns in the table using the `columns` keyword argument, and the order of the rows using the `index` keyword argument.

It is also possible to create a DataFrame directly from a spreadsheet&mdash;such as from **`.csv`** file (containing **c**omma **s**separated **v**alues) by using the `pandas.read_csv()` function:

```python
my_dataframe = pd.read_csv('path/to/my/file.csv')
```

See [the IO Tools documentation](http://pandas.pydata.org/pandas-docs/stable/io.html) for details and other file-reading functions.

### DataFrame Operations and Methods
Much like Series, DataFrames support a **vectorized** form of mathematical and relational operators: when the other operand is a _scalar_, then the operation is applied member-wise to each value in the DataFrame:

```python
# data frame of test scores
test_scores = pd.DataFrame({
    'math':[91, 82, 93, 100, 78, 91],
    'spanish':[88, 79, 77, 99, 88, 93]
})

curved_scores = test_scores * 1.02  # curve scores up by 2%
print(curved_scores)
    #      math  spanish
    # 0   92.82    89.76
    # 1   83.64    80.58
    # 2   94.86    78.54
    # 3  102.00   100.98
    # 4   79.56    89.76
    # 5   92.82    94.86

print(curved_scores > 90)
    #     math spanish
    # 0   True   False
    # 1  False   False
    # 2   True   False
    # 3   True    True
    # 4  False   False
    # 5   True    True
```

It is possible to have both operands be DataFrames. In thiis case the operation is applied member-wise, where values are matched if they have the same row and column label. Note that any value that doesn't have a pair will instead produce the value `NaN` (Not a Number). This is not a normal way of working with DataFrames&mdash;it is much more common to access individual rows and columns and work with those (e.g., add make a new column that is the sum of two others); see below for details.

Also like Series, DataFrames objects support a large number of methods, including:

| Function | Description |
| --- | --- |
| `index` | an _attribute_; the sequence of **row** index labels (convert to a _list_ to use) |
| `columns` | an _attribute_; the sequence of **column** index labels (convert to a _list_ to use) |
| `head(n)` | returns a DataFrame containing only the first `n` _rows_ |
| `tail(n)` | returns a DataFrame containing only the last `n` _rows_ |
| `assign(...)` | returns a new DataFrame with an additional column; call as `df.assign(new_label=new_column)` |
| `drop(label, row_or_col)` | returns a new DataFrame with the given row or column removed |
| `mean()` | returns a Series of the statistical means of the values of each **column** |
| `all()` | returns a Series of whether ALL the elemnts in each **column** are `True` (or "truthy") |
| `describe()` | returns a DataFrame whose columns are Series of descriptive statistics for each **column** in the original DataFrame |

You may notice that many of these methods (e.g., `head()`, `mean()`, `describe()`, `any()`) also exist for Series. In fact, most every method that Series support are supported by DataFrames as well. These methods are all applied **per column** (not per row)&mdash;that is, calling `mean()` on a DataFrame will calculate the _mean_ of **each column** in that DataFrame:

```python
df = pd.DataFrame({
    'name':['Ada','Bob','Chris','Diya','Emma'],
    'height':range(58,63),
    'weights':[115, 117, 120, 123, 126]})
df.mean()
    # height      60.0
    # weights    120.2
    # dtype: float64
```

If the Series method would return a _scalar_ (a single value, as with `mean()` or `any()`), then the DataFrame method returns a Series whose labels are the column labels, as above. If the Series method instead would return a Series (multiple values, as with `head()` or `describe()`), then the DataFrame method returns a new DataFrame whose columns are each of the resulting Series:

```python
df = pd.DataFrame({
    'name':['Ada','Bob','Chris','Diya','Emma'],
    'height':range(58,63),
    'weights':[115, 117, 120, 123, 126]})
df.describe()
    #         height     weights
    # count   5.000000    5.000000
    # mean   60.000000  120.200000
    # std     1.581139    4.438468
    # min    58.000000  115.000000
    # 25%    59.000000  117.000000
    # 50%    60.000000  120.000000
    # 75%    61.000000  123.000000
    # max    62.000000  126.000000
```
- Notice that the `height` column is the result of calling `describe()` on the DataFrame's `height` column Series!

- As a general rule: if you're expecting one value per column, you'll get a Series of those values; if you're expecting multiple values per column, you'll get a DataFrame of those values.

- This also means that you can sometimes "double-call" methods to reduce them further. For example, `df.any()` returns a Series of whether each column contains a `True` value; `df.all().all()` would check if _that_ Series contains all `True` values (thus checking _all_ columns have all `True` value, i.e., the entire table is all `True` values).


### Accessing DataFrames
DataFrames make it possible to quickly access individual or a subset of values, though these methods use a variety of syntax structures. For this explanation, refer to the following sample DataFrame initially described above:

```python
# all examples in this section
df = pd.DataFrame({
    'name':['Ada','Bob','Chris','Diya','Emma'],
    'height':range(58,63),
    'weight':[115, 117, 120, 123, 126]
})

print(df)
    #     height   name   weight
    # 0      58    Ada      115
    # 1      59    Bob      117
    # 2      60  Chris      120
    # 3      61   Diya      123
    # 4      62   Emma      126
```


Since DataFrames are most commonly viewed as a _dictionary of columns_, it is possible to access them as such using **bracket notation** (using the index label of the column):

```python
print( df['height'] )  # get height column
    # 0    58
    # 1    59
    # 2    60
    # 3    61
    # 4    62
    # Name: height, dtype: int64
```

However, it is often more common to refer to individual columns using **dot notation**, treating each column as an _attribute_ or _property_ of the DataFrame object:

```python
# same results as above
print( df.height )  # get height column
```

It is also possible to select _multiple_ columns by using a _list_ or sequence inside the **bracket notation** (similar to selecting multiple values from a Series). This will produce a new DataFrame (a "sub-table")

```python
# count the brackets carefully!
print( df[['name', 'height']] )  # get name and height columns

# can also select multiple columns with a list of their positions
print( df[[1,2]] )  # get 1st (name) and 2nd (weight) columns
```

- _Watch out though_! Specifying a **slice** (using a colon **`:`**) will actually select by _row_ position, not column position!

  ```python
  print( df[0:2] ) # get ROWS 0 through 2 (not inclusive)
      #    height name   weight
      # 0      58  Ada      115
      # 1      59  Bob      117
  ```

  I do not know wherefore this inconsistency, other than "convenience".

Because DataFrames support multiple indexes, it is possible to use **boolean indexing** (as with Series), allowing you to _filter_ for rows based the values in their columns:

```python
print( df[ df.height > 60 ] )
    #    height  name  weight
    # 3      61  Diya     123
    # 4      62  Emma     126
```

- Note that `df.height`is a Series (a column), so `df.height > 60` produces a Series of boolean values (`True` and `False`). This Series is used to determine _which_ rows to return from the DataFrame&mdash;each row that corresponds with a `True` index.

Finally, DataFrames also provide two _attributes_ (properties) used to "quick access" values: **`loc`**, which provides an "index" (lookup table) based on index labels, and **`iloc`**, which provides an "index" (lookup table) based on row and column positions. Each of these "indexes" can be thought of as a _dictionary_ whose values are the individual elements in the DataFrame, and whose keys can therefore be used to access those values using **bracket notation**. The dictionaries support multiple types of keys (using label-based `loc` as an example):

| Key Type | Description | Example |
| --- | --- | --- |
| `df.loc[row_label]` | An individual value | `df.loc['Ada']` (the row labeled `Ada`) |
| `df.loc[row_label_list]` | A list of row labels | `df.loc[['Ada','Bob']]` (the rows labeled `Ada` and `Bob`)
| `df.loc[row_label_slice]` | A _slice_ of row labels | `df.loc['Bob':'Diya']` (the rows from `Bob` to `Diya`. Note that this is an _inclusive_ slice!) |
| `df.loc[row_label, col_label]` | A _tuple_ of `(row, column)` | `df.loc['Ada', 'height']` (the value at row `Ada`, column `height`) |
| `df.loc[row_label_seq, col_label_seq]` | A _tuple_ of label lists or slices | `df.loc['Bob':'Diya', ['height','weight']` (the rows from `Bob` to `Diya` with the columns `height` and `weight`) |

- Note that the example `df` table doesn't have row labels beyond `0` to `4`

- Using a _tuple_ makes it easy to access a particular value in the table, or a range of values (_selecting_ rows and columns ).

- Note that we can also use the boundless slice `:` to refer to "all elements". So for example:

  ```python
  df.loc[:, 'height']  # get all rows, 'height' column
  ```

This is a basic summary of how to create and access DataFrames; for more [detailed usage](http://pandas.pydata.org/pandas-docs/stable/basics.html#), [additional methods](http://pandas.pydata.org/pandas-docs/stable/dsintro.html#dataframe), and specific ["recipes"](http://pandas.pydata.org/pandas-docs/stable/cookbook.html), see the [official `pandas` documentation](http://pandas.pydata.org/pandas-docs/stable/tutorials.html).

<!-- FOR FUTURE VERSIONS -->
<!-- //piping -->
<!-- //merge/join/GROUPING -->
