

```python
import Seperated_functions_v3 as sf
import numpy as np
import math
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from dateutil import parser, relativedelta
from IPython.display import display

pd.options.mode.chained_assignment = None  # default='warn'
```

# Hoeken berekenen


```python
def calculate_arc(comparing_vector, arm_vector):
    sum_vectors = np.dot(np.transpose(comparing_vector), arm_vector)
    multiplication_lengths = np.linalg.norm(comparing_vector) * np.linalg.norm(arm_vector)
    
    cos_alpha = sum_vectors / multiplication_lengths
    arc = np.arccos(cos_alpha) * 180 / math.pi
    return arc

def get_all_arcs(df):
    df_arcs = pd.DataFrame(index=np.arange(0), columns=('pNum', 'eNum', 'nTime')).fillna(0)
    
    for index, row in df.iterrows():
        e = row.eNum
        p = row.pNum
        time = row.nTime
        
        x_elb_l = row.xElbowLeft
        y_elb_l = row.yElbowLeft
        z_elb_l = row.zElbowLeft
        
        x_elb_r = row.xElbowRight
        y_elb_r = row.yElbowRight
        z_elb_r = row.zElbowRight
        
        x_shl_l = row.xShoulderLeft
        y_shl_l = row.yShoulderLeft
        z_shl_l = row.zShoulderLeft
        
        x_shl_r = row.xShoulderRight
        y_shl_r = row.yShoulderRight
        z_shl_r = row.zShoulderRight
        
        x_spn_mid = row.xSpineMid
        y_spn_mid = row.ySpineMid
        z_spn_mid = row.zSpineMid
        
        x_spn_shl = row.xSpineShoulder
        y_spn_shl = row.ySpineShoulder
        z_spn_shl = row.zSpineShoulder

        # arc right height
        comparing_vector = np.matrix([[x_spn_mid - x_spn_shl],[y_spn_mid - y_spn_shl]])
        arm_vector = np.matrix([[x_elb_r - x_shl_r],[y_elb_r - y_shl_r]])
        arc_r_h = calculate_arc(comparing_vector, arm_vector)
        
        # arc right DEPTH
        comparing_vector = np.matrix([[x_elb_r - x_shl_r], [y_elb_r - y_shl_r] ,[0]])
        arm_vector = np.matrix([[x_elb_r - x_shl_r], [y_elb_r - y_shl_r], [z_elb_r - z_shl_r]])
        arc_r_d = calculate_arc(comparing_vector, arm_vector)

        # arc left height
        comparing_vector = np.matrix([[x_spn_mid - x_spn_shl],[y_spn_mid - y_spn_shl]])
        arm_vector = np.matrix([[x_elb_l - x_shl_l],[y_elb_l - y_shl_l]])
        arc_l_h = calculate_arc(comparing_vector, arm_vector)

        # arc left DEPTH
        comparing_vector = np.matrix([[-(x_elb_l - x_shl_l)], [y_elb_l - y_shl_l], [0]])
        arm_vector = np.matrix([[-(x_elb_l - x_shl_l)], [y_elb_l - y_shl_l], [z_elb_l - z_shl_l]])
        arc_l_d = calculate_arc(comparing_vector, arm_vector)
        
        new_row = {'pNum':p, 'eNum':e, 'nTime':time, 'arc_r_h':arc_r_h.item(0), 'arc_r_d':arc_r_d.item(0), 'arc_l_h':arc_l_h.item(0), 'arc_l_d':arc_l_d.item(0)}
        df_arcs = df_arcs.append(new_row, ignore_index=True)
    
    return df_arcs
```

# Import data


```python
df = sf.getCleanedCombinedData()
```

# Create nieuwe dataframe

## Create raw input
Nieuwe dataframe met gewenste coördinaten per frame.

## Interpolate
Nieuwe dataframe met geinterpoleerde data.


```python
def create_raw_input(df_one_person):
    columns = ['pNum', 'eNum', 'frameNum', 'nTime',
               'xElbowLeft', 'yElbowLeft', 'zElbowLeft', 
               'xElbowRight', 'yElbowRight', 'zElbowRight']
    df_new = pd.DataFrame(index=np.arange(0), columns=columns).fillna(0)
    
    pNum = df_one_person.pNum.unique()[0]
    for eNum in df_one_person.eNum.unique():
        df_one_ex = df_one_person.loc[df_one_person['eNum'] == eNum]
        norm_times_both = sf.normalize_time(df_one_ex, eNum, 'lr')
        df_one_ex_rotated = sf.rotate_body(df_one_ex)
        for f in range(df_one_ex_rotated.frameNum.min(), df_one_ex_rotated.frameNum.max() + 1):
            frame = df_one_ex_rotated.loc[df_one_ex_rotated['frameNum'] == f]
            for index, raw_row in frame.iterrows():
                if raw_row.jointName == "ElbowLeft":
                    xElbowLeft = raw_row.xRotated
                    yElbowLeft = raw_row.y
                    zElbowLeft = raw_row.zRotated
                elif raw_row.jointName == "ElbowRight":
                    xElbowRight = raw_row.xRotated
                    yElbowRight = raw_row.y
                    zElbowRight = raw_row.zRotated
                elif raw_row.jointName == "ShoulderLeft":
                    xShoulderLeft = raw_row.xRotated
                    yShoulderLeft = raw_row.y
                    zShoulderLeft = raw_row.zRotated
                elif raw_row.jointName == "ShoulderRight":
                    xShoulderRight = raw_row.xRotated
                    yShoulderRight = raw_row.y
                    zShoulderRight = raw_row.zRotated
                elif raw_row.jointName == "SpineShoulder":
                    xSpineShoulder = raw_row.xRotated
                    ySpineShoulder = raw_row.y
                    zSpineShoulder = raw_row.zRotated
                elif raw_row.jointName == "SpineMid":
                    xSpineMid = raw_row.xRotated
                    ySpineMid = raw_row.y
                    zSpineMid = raw_row.zRotated
                    
            nTime = norm_times_both.loc[norm_times_both['frameNum'] == f, 'normalized']
            nTime = nTime.values[0]
            row = {'pNum': pNum, 'eNum': eNum, 'frameNum': f, 'nTime': nTime,
                   'xShoulderLeft': xShoulderLeft, 'yShoulderLeft': yShoulderLeft, 'zShoulderLeft': zShoulderLeft,  
                   'xShoulderRight': xShoulderRight, 'yShoulderRight': yShoulderRight, 'zShoulderRight': zShoulderRight, 
                   'xSpineShoulder': xSpineShoulder, 'ySpineShoulder': ySpineShoulder, 'zSpineShoulder': zSpineShoulder,  
                   'xSpineMid': xSpineMid, 'ySpineMid': ySpineMid, 'zSpineMid': zSpineMid, 
                   'xElbowLeft': xElbowLeft, 'yElbowLeft': yElbowLeft, 'zElbowLeft': zElbowLeft, 
                   'xElbowRight': xElbowRight, 'yElbowRight': yElbowRight, 'zElbowRight': zElbowRight}
            df_new = df_new.append(row, ignore_index=True)
    return df_new
    
def interpolate(raw_input_df, columns):
    intervalList = [0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9]
    boundaries_per_ex = dict()
    
    pNum = raw_input_df.pNum.unique()[0]
    for eNum in raw_input_df.eNum.unique():
        raw_input_df_eNum = raw_input_df.loc[raw_input_df['eNum'] == eNum]
        boundaries = dict()
        for i in intervalList:    
            for index, waarde in raw_input_df_eNum.iterrows():
                if waarde.nTime < i:
                    boundary_low = waarde
                if waarde.nTime >= i:
                    boundary_high = waarde
                    break;
            boundaries[i] = [boundary_low, boundary_high]
        boundaries_per_ex[eNum] = boundaries
        
    df_new = pd.DataFrame(index=np.arange(0), columns=columns).fillna(0)
    
    for boundary in boundaries_per_ex.values():
        for k, i in boundary.items():
            row = dict((cl,0) for cl in columns)
            row['pNum'] = i[0].pNum
            row['eNum'] = i[0].eNum
            row['nTime'] = k
            
            for column_name in raw_input_df.columns[3:]:
                row[column_name] = calculate_interpolation(i[0], i[1], k, column_name)
            
            df_new = df_new.append(row, ignore_index=True)
    
    return df_new


def calculate_interpolation(low_value, high_value, time, needed_info):
    low_value_info = low_value[needed_info]
    low_value_time = low_value['nTime']
    high_value_info = high_value[needed_info]
    high_value_time = high_value['nTime']

    return (low_value_info + (time - low_value_time) * ((high_value_info - low_value_info)/(high_value_time - low_value_time)))
```

# Aanroepen functies


```python
e = 1

df_raw_input = pd.DataFrame(index=np.arange(0)).fillna(0)

for p in df.pNum.unique():
    if not p == 76:
        print(p)
        one_person = df.loc[df['pNum'] == p]
        one_exercise = one_person.loc[one_person['eNum'] == e]
        raw_input_df = create_raw_input(one_exercise)
        df_raw_input = df_raw_input.append(raw_input_df)


```

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22
    23
    24
    25
    26
    27
    28
    29
    30
    31
    32
    33
    34
    35
    36
    37
    38
    39
    40
    41
    42
    43
    44
    45
    46
    47
    48
    49
    50
    51
    52
    53
    54
    55
    56
    57
    58
    59
    60
    61
    67
    68
    69
    70
    71
    72
    73
    74
    75
    77
    78
    79
    80
    81
    82
    83
    84
    85



```python
df_with_arcs = get_all_arcs(df_raw_input)
# # df_raw_input.loc[:, 'xElbowRight':'zShoulderRight']
# display(df_with_arcs.loc[df_with_arcs['pNum'] == 1])
```


```python
all_interp_df = pd.DataFrame(index=np.arange(0)).fillna(0)

for p in df_with_arcs.pNum.unique():
    df_arcs_p = df_with_arcs.loc[df_with_arcs['pNum'] == p]
    interpolated_df = interpolate(df_arcs_p, list(df_arcs_p))
    all_interp_df = all_interp_df.append(interpolated_df)

display(all_interp_df)
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
      <th>nTime</th>
      <th>arc_l_d</th>
      <th>arc_l_h</th>
      <th>arc_r_d</th>
      <th>arc_r_h</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>2.127902</td>
      <td>11.479788</td>
      <td>3.074778</td>
      <td>13.440054</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>5.174369</td>
      <td>52.406990</td>
      <td>7.401363</td>
      <td>47.893388</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>9.282723</td>
      <td>97.697166</td>
      <td>7.521018</td>
      <td>91.091232</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>5.377541</td>
      <td>121.776497</td>
      <td>4.995320</td>
      <td>119.829202</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>18.754223</td>
      <td>165.118188</td>
      <td>17.503737</td>
      <td>158.253622</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>26.702497</td>
      <td>171.812814</td>
      <td>21.907158</td>
      <td>169.012407</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>17.177572</td>
      <td>142.522914</td>
      <td>14.142333</td>
      <td>140.241896</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>1.737047</td>
      <td>96.466219</td>
      <td>3.657311</td>
      <td>98.687066</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>0.596156</td>
      <td>47.428509</td>
      <td>0.705401</td>
      <td>52.510253</td>
    </tr>
    <tr>
      <th>0</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>2.777022</td>
      <td>22.515981</td>
      <td>4.244453</td>
      <td>24.361058</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>7.989956</td>
      <td>85.762754</td>
      <td>4.335646</td>
      <td>73.871609</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>2.524838</td>
      <td>125.913087</td>
      <td>2.496434</td>
      <td>121.222397</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>2.258119</td>
      <td>138.102383</td>
      <td>1.456174</td>
      <td>133.145174</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>4.195166</td>
      <td>139.267736</td>
      <td>1.837542</td>
      <td>133.255871</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>4.112706</td>
      <td>137.680207</td>
      <td>1.792147</td>
      <td>133.016831</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>4.001737</td>
      <td>135.967399</td>
      <td>1.493391</td>
      <td>132.281099</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>6.989421</td>
      <td>99.301196</td>
      <td>12.442172</td>
      <td>90.626971</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>11.548497</td>
      <td>40.858084</td>
      <td>15.989977</td>
      <td>42.393103</td>
    </tr>
    <tr>
      <th>0</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>3.467568</td>
      <td>18.288501</td>
      <td>1.305559</td>
      <td>14.192074</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>3.144603</td>
      <td>75.412569</td>
      <td>10.726964</td>
      <td>65.519127</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>3.775470</td>
      <td>114.251533</td>
      <td>3.119260</td>
      <td>108.000853</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>17.269548</td>
      <td>149.670994</td>
      <td>17.834991</td>
      <td>148.940127</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>21.606838</td>
      <td>164.660182</td>
      <td>24.204262</td>
      <td>168.839392</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>23.088551</td>
      <td>165.863495</td>
      <td>26.024090</td>
      <td>172.099965</td>
    </tr>
    <tr>
      <th>6</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>23.610953</td>
      <td>164.876518</td>
      <td>25.570463</td>
      <td>168.476335</td>
    </tr>
    <tr>
      <th>7</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>7.857908</td>
      <td>108.060972</td>
      <td>9.895011</td>
      <td>111.255955</td>
    </tr>
    <tr>
      <th>8</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>10.549423</td>
      <td>40.659109</td>
      <td>7.813531</td>
      <td>41.438435</td>
    </tr>
    <tr>
      <th>0</th>
      <td>4.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>0.257883</td>
      <td>34.991857</td>
      <td>0.588794</td>
      <td>30.702287</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>1.003420</td>
      <td>91.660841</td>
      <td>0.786611</td>
      <td>91.828196</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>3.728414</td>
      <td>130.425379</td>
      <td>0.297223</td>
      <td>135.748884</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>82.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>6.426125</td>
      <td>105.069726</td>
      <td>3.112012</td>
      <td>104.395420</td>
    </tr>
    <tr>
      <th>7</th>
      <td>82.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>0.879331</td>
      <td>59.945168</td>
      <td>3.481892</td>
      <td>55.257584</td>
    </tr>
    <tr>
      <th>8</th>
      <td>82.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>2.700877</td>
      <td>14.262358</td>
      <td>2.133247</td>
      <td>16.033835</td>
    </tr>
    <tr>
      <th>0</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>6.036356</td>
      <td>18.321563</td>
      <td>3.312283</td>
      <td>13.232360</td>
    </tr>
    <tr>
      <th>1</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>4.935752</td>
      <td>95.230243</td>
      <td>5.838766</td>
      <td>88.277757</td>
    </tr>
    <tr>
      <th>2</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>0.682108</td>
      <td>135.270847</td>
      <td>3.133272</td>
      <td>136.783512</td>
    </tr>
    <tr>
      <th>3</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>11.787652</td>
      <td>171.530006</td>
      <td>12.712800</td>
      <td>164.494899</td>
    </tr>
    <tr>
      <th>4</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>6.087008</td>
      <td>179.332257</td>
      <td>8.282754</td>
      <td>178.346203</td>
    </tr>
    <tr>
      <th>5</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>1.239857</td>
      <td>145.912428</td>
      <td>2.810237</td>
      <td>153.920218</td>
    </tr>
    <tr>
      <th>6</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>8.060932</td>
      <td>92.272537</td>
      <td>11.863038</td>
      <td>92.465973</td>
    </tr>
    <tr>
      <th>7</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>3.847029</td>
      <td>36.193981</td>
      <td>8.398196</td>
      <td>37.142022</td>
    </tr>
    <tr>
      <th>8</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>2.332910</td>
      <td>11.004991</td>
      <td>0.103173</td>
      <td>10.805140</td>
    </tr>
    <tr>
      <th>0</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>4.956334</td>
      <td>24.088927</td>
      <td>1.006322</td>
      <td>21.683097</td>
    </tr>
    <tr>
      <th>1</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>0.165426</td>
      <td>70.840791</td>
      <td>7.767270</td>
      <td>62.961039</td>
    </tr>
    <tr>
      <th>2</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>0.946452</td>
      <td>100.502259</td>
      <td>4.871328</td>
      <td>94.440262</td>
    </tr>
    <tr>
      <th>3</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>7.096500</td>
      <td>121.017232</td>
      <td>5.914744</td>
      <td>123.967784</td>
    </tr>
    <tr>
      <th>4</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>9.616915</td>
      <td>152.328884</td>
      <td>9.163396</td>
      <td>153.313500</td>
    </tr>
    <tr>
      <th>5</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>15.753404</td>
      <td>166.839711</td>
      <td>14.948359</td>
      <td>165.136005</td>
    </tr>
    <tr>
      <th>6</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>2.519169</td>
      <td>124.040415</td>
      <td>2.741011</td>
      <td>118.243425</td>
    </tr>
    <tr>
      <th>7</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>0.310292</td>
      <td>70.509018</td>
      <td>9.972443</td>
      <td>71.443298</td>
    </tr>
    <tr>
      <th>8</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>1.973912</td>
      <td>30.098018</td>
      <td>0.181525</td>
      <td>26.287096</td>
    </tr>
    <tr>
      <th>0</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>7.873838</td>
      <td>41.381824</td>
      <td>0.655291</td>
      <td>34.424991</td>
    </tr>
    <tr>
      <th>1</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>3.699333</td>
      <td>94.543071</td>
      <td>4.151661</td>
      <td>87.529322</td>
    </tr>
    <tr>
      <th>2</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>11.008431</td>
      <td>130.530706</td>
      <td>8.566347</td>
      <td>131.306484</td>
    </tr>
    <tr>
      <th>3</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>16.031904</td>
      <td>164.838605</td>
      <td>14.451203</td>
      <td>170.829927</td>
    </tr>
    <tr>
      <th>4</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>6.411819</td>
      <td>171.491084</td>
      <td>8.076614</td>
      <td>178.425464</td>
    </tr>
    <tr>
      <th>5</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>5.202091</td>
      <td>178.328489</td>
      <td>2.665784</td>
      <td>174.329942</td>
    </tr>
    <tr>
      <th>6</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>6.515227</td>
      <td>109.181858</td>
      <td>2.327676</td>
      <td>112.378722</td>
    </tr>
    <tr>
      <th>7</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>11.259756</td>
      <td>58.100261</td>
      <td>5.138704</td>
      <td>62.000953</td>
    </tr>
    <tr>
      <th>8</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>7.830663</td>
      <td>15.344181</td>
      <td>4.683741</td>
      <td>18.059409</td>
    </tr>
  </tbody>
</table>
<p>711 rows × 7 columns</p>
</div>



```python
all_interp_df.to_csv('arcs_to_cluster.csv')
```


```python
deviation_per_column = get_deviation(all_interp_df, list(all_interp_df))
deviation_df = create_deviation_df(all_interp_df, deviation_per_column, list(all_interp_df))
display(deviation_df)
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
      <th>nTime</th>
      <th>arc_l_d</th>
      <th>arc_l_h</th>
      <th>arc_r_d</th>
      <th>arc_r_h</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>1.797695</td>
      <td>17.753513</td>
      <td>0.730547</td>
      <td>13.608474</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>-0.122927</td>
      <td>24.159945</td>
      <td>-0.764296</td>
      <td>26.734671</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>-2.840348</td>
      <td>21.278320</td>
      <td>-1.102875</td>
      <td>27.107082</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>5.705410</td>
      <td>30.308902</td>
      <td>5.354707</td>
      <td>32.407703</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>-6.345970</td>
      <td>2.432425</td>
      <td>-4.737231</td>
      <td>9.919206</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>-14.474288</td>
      <td>-6.970923</td>
      <td>-9.742289</td>
      <td>-3.622142</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>-7.574175</td>
      <td>-8.225162</td>
      <td>-5.154025</td>
      <td>-4.750797</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>4.297080</td>
      <td>-11.302529</td>
      <td>3.137565</td>
      <td>-12.021151</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>3.650062</td>
      <td>-13.981559</td>
      <td>3.604790</td>
      <td>-17.949786</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>1.148575</td>
      <td>6.717320</td>
      <td>-0.439128</td>
      <td>2.687470</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>-2.938514</td>
      <td>-9.195819</td>
      <td>2.301420</td>
      <td>0.756450</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>3.917537</td>
      <td>-6.937600</td>
      <td>3.921708</td>
      <td>-3.024083</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>8.824832</td>
      <td>13.983016</td>
      <td>8.893853</td>
      <td>19.091731</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>8.213087</td>
      <td>28.282877</td>
      <td>10.928964</td>
      <td>34.916958</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>8.115503</td>
      <td>27.161684</td>
      <td>10.372722</td>
      <td>32.373434</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>5.601660</td>
      <td>-1.669647</td>
      <td>7.494917</td>
      <td>3.209999</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>-0.955294</td>
      <td>-14.137506</td>
      <td>-5.647295</td>
      <td>-3.961055</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>-7.302280</td>
      <td>-7.411133</td>
      <td>-11.679785</td>
      <td>-7.832636</td>
    </tr>
    <tr>
      <th>18</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>0.458030</td>
      <td>10.944799</td>
      <td>2.499767</td>
      <td>12.856453</td>
    </tr>
    <tr>
      <th>19</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>1.906839</td>
      <td>1.154366</td>
      <td>-4.089897</td>
      <td>9.108932</td>
    </tr>
    <tr>
      <th>20</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>2.666905</td>
      <td>4.723953</td>
      <td>3.298883</td>
      <td>10.197461</td>
    </tr>
    <tr>
      <th>21</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>-6.186597</td>
      <td>2.414405</td>
      <td>-7.484964</td>
      <td>3.296777</td>
    </tr>
    <tr>
      <th>22</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>-9.198585</td>
      <td>2.890431</td>
      <td>-11.437756</td>
      <td>-0.666563</td>
    </tr>
    <tr>
      <th>23</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>-10.860342</td>
      <td>-1.021605</td>
      <td>-13.859220</td>
      <td>-6.709700</td>
    </tr>
    <tr>
      <th>24</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>-14.007556</td>
      <td>-30.578766</td>
      <td>-16.582155</td>
      <td>-32.985236</td>
    </tr>
    <tr>
      <th>25</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>-1.823780</td>
      <td>-22.897282</td>
      <td>-3.100134</td>
      <td>-24.590039</td>
    </tr>
    <tr>
      <th>26</th>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>-6.303206</td>
      <td>-7.212158</td>
      <td>-3.503339</td>
      <td>-6.877968</td>
    </tr>
    <tr>
      <th>27</th>
      <td>4.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>3.667715</td>
      <td>-5.758556</td>
      <td>3.216531</td>
      <td>-3.653759</td>
    </tr>
    <tr>
      <th>28</th>
      <td>4.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>4.048022</td>
      <td>-15.093907</td>
      <td>5.850456</td>
      <td>-17.200137</td>
    </tr>
    <tr>
      <th>29</th>
      <td>4.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>2.713961</td>
      <td>-11.449892</td>
      <td>6.120920</td>
      <td>-17.550570</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>681</th>
      <td>82.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>3.177272</td>
      <td>29.228026</td>
      <td>5.876295</td>
      <td>31.095679</td>
    </tr>
    <tr>
      <th>682</th>
      <td>82.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>5.154796</td>
      <td>25.218522</td>
      <td>3.312984</td>
      <td>31.408332</td>
    </tr>
    <tr>
      <th>683</th>
      <td>82.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>1.545340</td>
      <td>19.184592</td>
      <td>2.176944</td>
      <td>18.526632</td>
    </tr>
    <tr>
      <th>684</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>-2.110758</td>
      <td>10.911738</td>
      <td>0.493042</td>
      <td>13.816168</td>
    </tr>
    <tr>
      <th>685</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>0.115690</td>
      <td>-18.663308</td>
      <td>0.798300</td>
      <td>-13.649698</td>
    </tr>
    <tr>
      <th>686</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>5.760268</td>
      <td>-16.295361</td>
      <td>3.284870</td>
      <td>-18.585198</td>
    </tr>
    <tr>
      <th>687</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>-0.704701</td>
      <td>-19.444607</td>
      <td>-2.362773</td>
      <td>-12.257994</td>
    </tr>
    <tr>
      <th>688</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>6.321245</td>
      <td>-11.781644</td>
      <td>4.483752</td>
      <td>-10.173374</td>
    </tr>
    <tr>
      <th>689</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>10.988352</td>
      <td>18.929462</td>
      <td>9.354633</td>
      <td>11.470047</td>
    </tr>
    <tr>
      <th>690</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>1.542465</td>
      <td>42.025214</td>
      <td>-2.874731</td>
      <td>43.025126</td>
    </tr>
    <tr>
      <th>691</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>2.187098</td>
      <td>48.969708</td>
      <td>-1.603320</td>
      <td>49.523893</td>
    </tr>
    <tr>
      <th>692</th>
      <td>83.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>1.913307</td>
      <td>22.441960</td>
      <td>4.207019</td>
      <td>23.755327</td>
    </tr>
    <tr>
      <th>693</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>-1.030737</td>
      <td>5.144373</td>
      <td>2.799003</td>
      <td>5.365430</td>
    </tr>
    <tr>
      <th>694</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>4.886016</td>
      <td>5.726144</td>
      <td>-1.130203</td>
      <td>11.667021</td>
    </tr>
    <tr>
      <th>695</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>5.495923</td>
      <td>18.473227</td>
      <td>1.546814</td>
      <td>23.758052</td>
    </tr>
    <tr>
      <th>696</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>3.986451</td>
      <td>31.068166</td>
      <td>4.435283</td>
      <td>28.269121</td>
    </tr>
    <tr>
      <th>697</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>2.791338</td>
      <td>15.221729</td>
      <td>3.603110</td>
      <td>14.859328</td>
    </tr>
    <tr>
      <th>698</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>-3.525194</td>
      <td>-1.997820</td>
      <td>-2.783489</td>
      <td>0.254260</td>
    </tr>
    <tr>
      <th>699</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>7.084228</td>
      <td>10.257337</td>
      <td>6.247296</td>
      <td>17.247674</td>
    </tr>
    <tr>
      <th>700</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>5.723836</td>
      <td>14.654672</td>
      <td>-3.177566</td>
      <td>15.222618</td>
    </tr>
    <tr>
      <th>701</th>
      <td>84.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>2.272305</td>
      <td>3.348933</td>
      <td>4.128667</td>
      <td>8.273372</td>
    </tr>
    <tr>
      <th>702</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.1</td>
      <td>-3.948241</td>
      <td>-12.148524</td>
      <td>3.150034</td>
      <td>-7.376463</td>
    </tr>
    <tr>
      <th>703</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>1.352109</td>
      <td>-17.976136</td>
      <td>2.485405</td>
      <td>-12.901263</td>
    </tr>
    <tr>
      <th>704</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>-4.566055</td>
      <td>-11.555219</td>
      <td>-2.148205</td>
      <td>-13.108170</td>
    </tr>
    <tr>
      <th>705</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>-4.948953</td>
      <td>-12.753206</td>
      <td>-4.101176</td>
      <td>-18.593023</td>
    </tr>
    <tr>
      <th>706</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>5.996434</td>
      <td>-3.940471</td>
      <td>4.689892</td>
      <td>-10.252635</td>
    </tr>
    <tr>
      <th>707</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>7.026119</td>
      <td>-13.486599</td>
      <td>9.499086</td>
      <td>-8.939677</td>
    </tr>
    <tr>
      <th>708</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>3.088170</td>
      <td>25.115894</td>
      <td>6.660631</td>
      <td>23.112377</td>
    </tr>
    <tr>
      <th>709</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>-5.225629</td>
      <td>27.063429</td>
      <td>1.656172</td>
      <td>24.664963</td>
    </tr>
    <tr>
      <th>710</th>
      <td>85.0</td>
      <td>1.0</td>
      <td>0.9</td>
      <td>-3.584446</td>
      <td>18.102769</td>
      <td>-0.373549</td>
      <td>16.501059</td>
    </tr>
  </tbody>
</table>
<p>711 rows × 7 columns</p>
</div>



```python
deviation_df.loc[deviation_df['arc_l_h'] > 100]
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
      <th>nTime</th>
      <th>arc_l_d</th>
      <th>arc_l_h</th>
      <th>arc_r_d</th>
      <th>arc_r_h</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>614</th>
      <td>74.0</td>
      <td>1.0</td>
      <td>0.3</td>
      <td>-3.156540</td>
      <td>111.357131</td>
      <td>4.414853</td>
      <td>108.522197</td>
    </tr>
    <tr>
      <th>615</th>
      <td>74.0</td>
      <td>1.0</td>
      <td>0.4</td>
      <td>1.386181</td>
      <td>144.269416</td>
      <td>8.605177</td>
      <td>142.778819</td>
    </tr>
    <tr>
      <th>616</th>
      <td>74.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>3.440972</td>
      <td>159.535218</td>
      <td>11.309379</td>
      <td>159.174772</td>
    </tr>
    <tr>
      <th>617</th>
      <td>74.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>7.042891</td>
      <td>154.048184</td>
      <td>11.199511</td>
      <td>153.891765</td>
    </tr>
    <tr>
      <th>618</th>
      <td>74.0</td>
      <td>1.0</td>
      <td>0.7</td>
      <td>0.616998</td>
      <td>118.312941</td>
      <td>8.382330</td>
      <td>117.469501</td>
    </tr>
  </tbody>
</table>
</div>




```python
deviation_df.to_csv('arcs_to_cluster_deviation_2.csv')
```

# 'Oude' functies


```python
def get_deviation(df_one_ex, columns):
    intervalList = [0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9]
    deviation_per_column = dict((cl,0) for cl in columns[3:])
    
    for column_name in columns[3:]:
        mean_per_time = dict()
        for i in intervalList:
            df_one_time = df_one_ex.loc[df_one_ex['nTime'] == i]
            mean_one_time = df_one_time[column_name].mean()
            mean_per_time[i] = mean_one_time
        deviation_per_column[column_name] = mean_per_time
           
    return deviation_per_column 

def create_deviation_df(df, deviation_dict, columns):
    deviation_df = pd.DataFrame(index=np.arange(0), columns=columns).fillna(0)
    
    for index, row in df.iterrows():
        cols = dict()
        cols['pNum'] = row.pNum
        cols['eNum'] = row.eNum
        cols['nTime'] = row.nTime
        
        for column in columns[3:]:
            joint = deviation_dict[column]
            mean = joint[row.nTime] 
            original = row[column]
            cols[column] = mean - original
        
        deviation_df = deviation_df.append(cols, ignore_index=True)
    
    return deviation_df
```


```python
y_elb_l = interpolated_df.loc[interpolated_df['eNum'] == 1, 'zElbowLeft'].tolist()
time = interpolated_df.loc[interpolated_df['eNum'] == 1, 'nTime'].tolist()

data_ex1 = interpolated_df.loc[interpolated_df['eNum'] == 1]

# data = pd.concat([decades, max_arcs.max_arc_left], axis=1)
f, ax = plt.subplots(figsize=(9,6))
fig = sns.boxplot(x='nTime', y='yElbowLeft', data=data_ex1)
# fig.axis(ymin=-0.5,ymax=1)
plt.show()

# data = pd.concat([decades, max_arcs.max_arc_left], axis=1)
f, ax = plt.subplots(figsize=(9,6))
fig = sns.boxplot(x='nTime', y='zElbowLeft', data=data_ex1)
# fig.axis(ymin=-0.5,ymax=1)
plt.show()
```
