

```python
import pandas as pd
df = pd.read_csv('/data/pepper/combined.csv')
```

### Waar komt de data vandaan?
De data is opgenomen door de projectgroep zelf. Dit is gedaan door op een publieke plek te gaan staan en
verschillende mensen aan te spreken en te vragen of ze mee willen doen. 

### Technische informatie
De data is samengevoegd in één CSV bestand, voor verdere informatie zie punt 1.

### Per variabele:


Name| Pos | Label| Values| Dataypte| Num/Cat | Pred/target | Summary
---|---|---|---|---|---|---|---
pNum       | 1   | unique ID for person        | 1-63   | int64      | Num     |             |
eNum       | 2   | ID for exercise performed   | 1-3    | int64      | Cat     |             | 1: arms up next to body
           |     | | | | |                                                         | 2: arms up in front of body
           | | | | | |           | 3: shoulder rotation
frameNum   | 3   | ID for frame by pNum & eNum | 1-4365                 | int64   | Num     
jointName  | 4   | name of joint in skeleton   |                        | object  | Cat     
x          | 5   | x-coordinate of joint       | -3.35723 - 1.307299    | float64 | Num      
y          | 6   | y-coordinate of joint       | -2.22423 - 2.100633    | float64 | Num      
z          | 7   | z-coordinate of joint       | -0.079023 - 4.98454    | float64 | Num      
orX        | 8   | rotation of coordinate      | -0.7215444 - 1.0       | float64 | Num  
orY        | 9   | rotation of coordinate      | -0.7217991 - 0.9999998 | float64 | Num  
orZ        | 10  | rotation of coordinate      | -0.7241029 - 0.9999837 | float64 | Num   
orW        | 11  | ?                           | -0.7289796 - 0.9999099 | float64 | Num   
time       | 12  | time of frame               | 00:08:43.8322624 - 23:51:24.6124626 | object   | Num   
trackingId | 13  | ID of skeleton              | 72057594037928250 - 72057594037992790 | int64    | Num 
trackState | 14  | ?                           |                        | object   | Cat     |          | Inferred/Tracked


```python
df.head()
```




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
      <th>0</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>SpineBase</td>
      <td>0.382257</td>
      <td>-0.279554</td>
      <td>2.267158</td>
      <td>0.006471</td>
      <td>0.998455</td>
      <td>0.045958</td>
      <td>0.030558</td>
      <td>20:52:36.9298293</td>
      <td>72057594037935192</td>
      <td>Tracked</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>SpineMid</td>
      <td>0.385437</td>
      <td>0.033563</td>
      <td>2.296143</td>
      <td>0.005410</td>
      <td>0.998894</td>
      <td>0.046095</td>
      <td>0.007517</td>
      <td>20:52:36.9298293</td>
      <td>72057594037935192</td>
      <td>Tracked</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>Neck</td>
      <td>0.386470</td>
      <td>0.336975</td>
      <td>2.311130</td>
      <td>-0.001040</td>
      <td>0.999826</td>
      <td>0.008939</td>
      <td>-0.016308</td>
      <td>20:52:36.9298293</td>
      <td>72057594037935192</td>
      <td>Tracked</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>Head</td>
      <td>0.420000</td>
      <td>0.465417</td>
      <td>2.305439</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>20:52:36.9298293</td>
      <td>72057594037935192</td>
      <td>Tracked</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>ShoulderLeft</td>
      <td>0.224313</td>
      <td>0.219038</td>
      <td>2.281287</td>
      <td>0.791678</td>
      <td>-0.604143</td>
      <td>0.023407</td>
      <td>-0.087803</td>
      <td>20:52:36.9298293</td>
      <td>72057594037935192</td>
      <td>Tracked</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(df['pNum'].min())
print(df['eNum'].min())
print(df['frameNum'].min())
print(df['jointName'].min())
print(df['x'].min())
print(df['y'].min())
print(df['z'].min())
print(df['orX'].min())
print(df['orY'].min())
print(df['orZ'].min())
print(df['orW'].min())
print(df['time'].min())
print(df['trackingId'].min())
print(df['trackState'].min())
print('---------------------')
print(df['pNum'].max())
print(df['eNum'].max())
print(df['frameNum'].max())
print(df['jointName'].max())
print(df['x'].max())
print(df['y'].max())
print(df['z'].max())
print(df['orX'].max())
print(df['orY'].max())
print(df['orZ'].max())
print(df['orW'].max())
print(df['time'].max())
print(df['trackingId'].max())
print(df['trackState'].max())


```

    1
    1
    1
    AnkleLeft
    -3.35723
    -2.224228
    -0.07902312
    -0.7215444
    -0.7217991
    -0.7241029
    -0.7289796
    00:08:43.8322624
    72057594037928250
    Inferred
    ---------------------
    63
    4
    4365
    WristRight
    1.307299
    2.100633
    4.98454
    1.0
    0.9999998
    0.9999837
    0.9999099
    23:51:24.6124626
    72057594037992790
    Tracked



```python
df.info()
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

