# qnorm
[![PyPI version](https://badge.fury.io/py/qnorm.svg)](https://badge.fury.io/py/qnorm)
[![Anaconda version](https://anaconda.org/conda-forge/qnorm/badges/version.svg)](https://anaconda.org/conda-forge/qnorm)
![tests](https://github.com/Maarten-vd-Sande/qnorm/workflows/tests/badge.svg)

Quantile normalization made easy! This tool was developed as the current implementations scattered across the web do not correctly resolve collisions/ties in the ranks. Properly resolving rank ties is important when ties happen frequently, such as when working with discrete numbers (integers) in count tables. This implementation should be relatively *fast*, and can use multiple cores to sort the columns and tie-resolvement is accelerated by numba.

## Code example

We recreate the example of [Wikipedia](https://en.wikipedia.org/wiki/Quantile_normalization):

```python
import pandas as pd
import qnorm

df = pd.DataFrame({'C1': {'A': 5, 'B': 2, 'C': 3, 'D': 4},
                   'C2': {'A': 4, 'B': 1, 'C': 4, 'D': 2},
                   'C3': {'A': 3, 'B': 4, 'C': 6, 'D': 8}})

print(qnorm.quantile_normalize(df))
```

which is what we expect:

```
         C1        C2        C3
A  5.666667  5.166667  2.000000
B  2.000000  2.000000  3.000000
C  3.000000  5.166667  4.666667
D  4.666667  3.000000  5.666667
```

**NOTE**: The function quantile_normalize also accepts numpy arrays. 

### Multicore support

To accelerate the computation you can pass a ncpus argument to the function call and qnorm will be run in parallel:

```python
qnorm.quantile_normalize(df, ncpus=8)  
```

### Normalize onto distribution

You can also use the `quantile_normalize` function to normalize "onto" a distribution, by passing a target along to the function call. 

```python
import pandas as pd
import qnorm

df = pd.DataFrame({'C1': {'A': 4, 'B': 3, 'C': 2, 'D': 1},
                   'C2': {'A': 1, 'B': 2, 'C': 3, 'D': 4}})

print(qnorm.quantile_normalize(df, target=[8, 9, 10, 11]))
```

With our values now transformed onto the target:

```
     C1    C2
A  11.0   8.0
B  10.0   9.0
C   9.0  10.0
D   8.0  11.0
```


## Command Line Interface (CLI) example

Qnorm also contains a CLI for converting csv/tsv files. The CLI depends on pandas, but this is an optional dependency of qnorm. To make use of the CLI make sure to install pandas in your current environment as well!


```console
user@comp:~$ qnorm --help

usage: qnorm [-h] [-v] table

Quantile normalize your table

positional arguments:
  table          input csv/tsv file which will be quantile normalized

optional arguments:
  -h, --help     show this help message and exit
  -v, --version  show program's version number and exit
```

And again the example of [Wikipedia](https://en.wikipedia.org/wiki/Quantile_normalization):

```console
user@comp:~$ cat table.tsv
        C1      C2      C3
A       5       4       3
B       2       1       4
C       3       4       6
D       4       2       8

user@comp:~$ qnorm table.tsv
        C1      C2      C3
A       5.666666666666666       5.166666666666666       2.0
B       2.0     2.0     3.0
C       3.0     5.166666666666666       4.666666666666666
D       4.666666666666666       3.0     5.666666666666666
```

**NOTE:** the qnorm cli assumes that the first column and the first row are used as descriptors, and are "ignored" in the quantile normalization process. Lines starting with a hashtag "#" are treated as comments and ignored.

## Installation

### pip

```console
user@comp:~$ pip install qnorm
```

### conda

Installing qnorm from the conda-forge channel can be achieved by adding conda-forge to your channels with:

```console
user@comp:~$ conda config --add channels conda-forge
```

Once the conda-forge channel has been enabled, qnorm can be installed with:

```console
user@comp:~$ conda install qnorm
```

### local

clone the repository

```console
user@comp:~$ git clone https://github.com/Maarten-vd-Sande/qnorm
```

And install it

```console
user@comp:~$ cd qnorm
user@comp:~$ pip install .
```
