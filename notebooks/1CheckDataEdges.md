

```python
import pandas as pd
df = pd.read_csv('/data/pepper/combined.csv')
```


```python
# check number of rows and columns
df.info()    # 14 columns and 3611650 rows

# check first few rows and last few rows
df.head()
df.tail()

# is the formatting ok?
# formatting seems to be okay

# are the values within the realm of reality?

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3611650 entries, 0 to 3611649
    Data columns (total 14 columns):
    pNum          int64
    eNum          int64
    frameNum      int64
    jointName     object
    x             float64
    y             float64
    z             float64
    orX           float64
    orY           float64
    orZ           float64
    orW           float64
    time          object
    trackingId    int64
    trackState    object
    dtypes: float64(7), int64(4), object(3)
    memory usage: 385.8+ MB





<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>pNum</th>
      <th>eNum</th>
      <th>frameNum</th>
      <th>jointName</th>
      <th>x</th>
      <th>y</th>
      <th>z</th>
      <th>orX</th>
      <th>orY</th>
      <th>orZ</th>
      <th>orW</th>
      <th>time</th>
      <th>trackingId</th>
      <th>trackState</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3611645</th>
      <td>63</td>
      <td>1</td>
      <td>4365</td>
      <td>SpineShoulder</td>
      <td>-0.006844</td>
      <td>0.214793</td>
      <td>2.283967</td>
      <td>-0.012703</td>
      <td>0.997223</td>
      <td>0.032781</td>
      <td>0.065652</td>
      <td>00:11:13.1320272</td>
      <td>72057594037928250</td>
      <td>Tracked</td>
    </tr>
    <tr>
      <th>3611646</th>
      <td>63</td>
      <td>1</td>
      <td>4365</td>
      <td>HandTipLeft</td>
      <td>-0.166747</td>
      <td>-0.365679</td>
      <td>2.121763</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>00:11:13.1320272</td>
      <td>72057594037928250</td>
      <td>Tracked</td>
    </tr>
    <tr>
      <th>3611647</th>
      <td>63</td>
      <td>1</td>
      <td>4365</td>
      <td>ThumbLeft</td>
      <td>-0.230618</td>
      <td>-0.342276</td>
      <td>2.143998</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>00:11:13.1320272</td>
      <td>72057594037928250</td>
      <td>Tracked</td>
    </tr>
    <tr>
      <th>3611648</th>
      <td>63</td>
      <td>1</td>
      <td>4365</td>
      <td>HandTipRight</td>
      <td>0.220967</td>
      <td>-0.381806</td>
      <td>2.216023</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>00:11:13.1320272</td>
      <td>72057594037928250</td>
      <td>Tracked</td>
    </tr>
    <tr>
      <th>3611649</th>
      <td>63</td>
      <td>1</td>
      <td>4365</td>
      <td>ThumbRight</td>
      <td>0.256454</td>
      <td>-0.345893</td>
      <td>2.279467</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>00:11:13.1320272</td>
      <td>72057594037928250</td>
      <td>Tracked</td>
    </tr>
  </tbody>
</table>
</div>


