

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import scipy as sc

max_arcs = pd.read_csv('max_arcs.csv')
ages = pd.read_csv('ages.csv')
```


```python
import statsmodels.formula.api as sm
data = pd.concat([ages.age, max_arcs.max_arc_left], axis=1)
result = sm.ols(formula="max_arc_left ~ age", data=data).fit()
```

        age  max_arc_left
    0    30     76.966447
    1    20     76.968126
    2    60     89.368176
    3    22     66.016467
    4    17     64.185861
    5    18     83.108991
    6    49     81.431338
    7    23     53.377687
    8    27     58.932988
    9    47     52.543959
    10   17     71.514988
    11   19     68.848591
    12   18     72.140307
    13   22     76.333038
    14   18     89.266111
    15   25     74.917215
    16   58     48.244884
    17   18     75.030343
    18   68     73.603396
    19   24     56.707486
    20   18     68.270694
    21   49     69.169204
    22   23     86.241291
    23   61     69.332266
    24   19     65.956025
    25   23     86.172102
    26   59     66.140498
    27   19     74.231866
    28   19     83.649960
    29   22     51.944083
    ..  ...           ...
    31   59     41.077607
    32   23     66.567916
    33   47     74.880184
    34   24     84.290957
    35   17     75.781439
    36   66     72.086856
    37   41     92.142070
    38   17     82.255993
    39   44     81.093811
    40   22     63.929138
    41    0     78.043289
    42   19     79.288181
    43   50     61.554761
    44   21     81.013489
    45   26    110.159313
    46   33     63.177180
    47   45     84.771266
    48   32     91.259645
    49   21     73.040304
    50   20     75.478405
    51   24     57.536379
    52   71     67.794726
    53   24     72.609508
    54   22     83.359767
    55   24     83.393609
    56   24     59.500691
    57   20     78.392828
    58   28     84.176531
    59   19     81.959695
    60   50     74.812947
    
    [61 rows x 2 columns]



```python
# copy_max_arcs = max_arcs.copy()
# copy_max_arcs['max_arc_left'] = result.predict(copy_max_arcs)
```