
# Rotate body


```python
import numpy as np
import math
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from dateutil import parser, relativedelta
from IPython.display import display

pd.options.mode.chained_assignment = None  # default='warn'

# vector to origin, recalculate all other points with that translation
def x_translation (x_ver, row):
    x_ch = row['x']
    x_n = x_ch - x_ver
    return  x_n

def z_translation (z_ver, row):
    z_ch = row['z']
    z_n = z_ch - z_ver
    return z_n

# calculate arc that the body needs to rotate to get both shoulders on the same z-coordinate
def define_arc(frame):
    x_left = float(frame.loc[frame['jointName'] == "ShoulderLeft", 'xToOrigin'])
    z_left = float(frame.loc[frame['jointName'] == "ShoulderLeft", 'zToOrigin'])
    arc = math.atan(abs(z_left)/abs(x_left))
    return arc

# rotate a given point and calculate new x and new z
def rotate_body_x(row, arc):
    new_x = (math.cos(arc) * float(row['xToOrigin'])) - (math.sin(arc) * float(row['zToOrigin']))
    return new_x

def rotate_body_z(row, arc):
    new_z = (math.cos(arc) * float(row['zToOrigin'])) + (math.sin(arc) * float(row['xToOrigin'])) 
    return new_z

def rotate_body(df):
    # create empty DataFrame
    index = np.arange(0)
    frames = pd.DataFrame(index=index)
    frames = frames.fillna(0) # with 0s rather than NaNs

#     ex_max = int(df['eNum'].max())
    
    # goes through all exercises recorded for a person
    for j in range(3, 4):
        # get frames from one exercise
        one_e = df #.loc[df['eNum'] == j]
            
        frame_max = int(one_e['frameNum'].max())
        frame_min = int(one_e['frameNum'].min())
        
        # go through all frames of an exercise
        for i in range(frame_min, frame_max + 1):   
            frame = one_e.loc[one_e['frameNum'] == i]
            
            # define translation to place vectors in origin
            x_ver = float(frame.loc[frame['jointName'] == "ShoulderRight", 'x'])
            z_ver = float(frame.loc[frame['jointName'] == "ShoulderRight", 'z'])

            # translate
            frame['xToOrigin'] = frame.apply (lambda row: x_translation(x_ver, row),axis=1)
            frame['zToOrigin'] = frame.apply (lambda row: z_translation(z_ver, row),axis=1)
            
            # decide if the body clockwise or counterclockwise rotation needs
            if float(frame.loc[frame['jointName'] == "ShoulderRight", 'z']) < float(frame.loc[frame['jointName'] == "ShoulderLeft", 'z']):
                # rotate clockwise
                clock = 1
            else:
                # rotate - clockwise
                clock = -1
            
            #rotate body
            arc = define_arc(frame)
            frame['xRotated'] = frame.apply (lambda row: rotate_body_x(row, arc * clock),axis=1)
            frame['zRotated'] = frame.apply (lambda row: rotate_body_z(row, arc * clock),axis=1)

            frames = frames.append(frame)    
    return frames
```

# Arcs in exercises


```python
def calculate_arc(frame, eNum, side):    
    # which exercise is done in the given dataframe
    if (eNum == 1) or (eNum == 2):
        # which coördinate is needed for the desired arc
        if eNum == 1:
            coordinate_one = 'xRotated'
        elif eNum == 2:
            coordinate_one = 'zRotated'
        coordinate_two = 'y'
        
        spineShoulder = np.matrix([[float(frame.loc[frame['jointName'] == 'SpineShoulder', coordinate_one])], 
                               [float(frame.loc[frame['jointName'] == 'SpineShoulder', coordinate_two])]])
        spineMid = np.matrix([[float(frame.loc[frame['jointName'] == 'SpineMid', coordinate_one])], 
                               [float(frame.loc[frame['jointName'] == 'SpineMid', coordinate_two])]])
        spineMid_new = spineMid - spineShoulder
        
        if side == 'l':
            # left side
            jointName_shoulder = "ShoulderLeft"
            jointName_elbow = "ElbowLeft"
        elif side == 'r':
            # right side
            jointName_shoulder = "ShoulderRight"
            jointName_elbow = "ElbowRight"
        
        shoulder = np.matrix([[float(frame.loc[frame['jointName'] == jointName_shoulder, coordinate_one])], 
                        [float(frame.loc[frame['jointName'] == jointName_shoulder, coordinate_two])]])
        elbow = np.matrix([[float(frame.loc[frame['jointName'] == jointName_elbow, coordinate_one])], 
                        [float(frame.loc[frame['jointName'] == jointName_elbow, coordinate_two])]])
        elbow_new = elbow - shoulder
        
        sum_vectors = np.dot(np.transpose(spineMid_new), elbow_new)
        multiplication_lengths = np.linalg.norm(spineMid_new) * np.linalg.norm(elbow_new)
        
    else:
        if side == 'r':
            # right side
            jointName_elbow = "ElbowRight"
            jointName_wrist = "WristRight"
        else:
            # left side
            jointName_elbow = "ElbowLeft"
            jointName_wrist = "WristLeft"
        
        try:
            elbow = np.matrix([[float(frame.loc[frame['jointName'] == jointName_elbow, 'xRotated'])], 
                                [float(frame.loc[frame['jointName'] == jointName_elbow, 'zRotated'])]])
        except: 
            print(frame)
        wrist = np.matrix([[float(frame.loc[frame['jointName'] == jointName_wrist, 'xRotated'])], 
                            [float(frame.loc[frame['jointName'] == jointName_wrist, 'zRotated'])]])

        wrist_new = wrist - elbow
        front_vector = np.matrix([[0],[-1]])

        sum_vectors = np.dot(np.transpose(front_vector), wrist_new)
        multiplication_lengths = np.linalg.norm(front_vector) * np.linalg.norm(wrist_new)
    
    cos_alpha = sum_vectors / multiplication_lengths
    alpha = np.arccos(cos_alpha) * 180 / math.pi
    
    if eNum == 1:
        if side == 'r':
            if elbow[0,0] < spineMid_new[0,0]:
                alpha = 360 - alpha
                print(alpha)
                print(elbow[0,0])
                print(spineMid_new[0,0])
                print('--------------------')
        else:
            if elbow[0,0] > spineMid_new[0,0]:
                alpha = 360 - alpha
                print(alpha)
                print(elbow[0,0])
                print(spineMid_new[0,0])
                print('--------------------')
    elif eNum == 2:
        if (elbow[1,0] > spineMid_new[1,0]) and (elbow[0,0] > spineMid_new[0,0]):
            alpha = 360 - alpha
            print(alpha)
    
    return alpha     

def get_arcs(df, eNum, side):
    frame_max = int(df['frameNum'].max())
    frame_min = int(df['frameNum'].min())

#     df = df.loc[df['eNum'] == eNum]

    # arcs = arcs.fillna(0) # with 0s rather than NaNs

    if side == 'lr':
        columns = ['frameNum', 'arc_right', 'arc_left']
        arcs = pd.DataFrame(index=np.arange(0), columns=columns).fillna(0)

        for i in range(frame_min, frame_max + 1): 
            frame = df.loc[df['frameNum'] == i]

            right_arc = calculate_arc(frame, eNum, 'r')
            left_arc = calculate_arc(frame, eNum, 'l')

            row = {'frameNum': int(i), 'arc_right': right_arc[0,0], 'arc_left': left_arc[0,0]}
            arcs = arcs.append(row, ignore_index=True)            

    else:
        columns = ['frameNum', 'arc']
        arcs = pd.DataFrame(index=np.arange(0), columns=columns)

        for i in range(frame_min, frame_max + 1): 
            frame = df.loc[df['frameNum'] == i]

            arc = calculate_arc(frame, eNum, side)
            
            row = {'frameNum': int(i), 'arc': arc[0,0]}
            arcs = arcs.append(row, ignore_index=True)            


    return arcs
```

# Time


```python
def relative_time(df, row, first_frame):
    if row == first_frame:
        return 0
    
    time_row = df.loc[df['frameNum'] == row, 'time']
    time_row = time_row.iloc[0]
    time_first_frame = df.loc[df['frameNum'] == first_frame, 'time']
    time_first_frame = time_first_frame.iloc[0]
    
    dt1 = parser.parse(time_row)
    dt2 = parser.parse(time_first_frame)
    delta = relativedelta.relativedelta(dt1, dt2)
    microseconds = delta.seconds * 1000000 + delta.microseconds
    return microseconds

def normalized_time(row, frame):
    total_time = frame['relativeTime'].max()
    
    relative_time = row['relativeTime']
    normalized = relative_time/total_time
    return normalized

def normalize_time(df, eNum, side):
    columns = ['frameNum', 'relativeTime']
    times = pd.DataFrame(index=np.arange(0), columns=columns).fillna(0)
    
    frame_max = int(df['frameNum'].max())
    frame_min = int(df['frameNum'].min())
    df = df.loc[df['eNum'] == eNum]
    
    for i in range(frame_min, frame_max + 1):
        time_relative = relative_time(df, i, frame_min)
        row = {'frameNum': int(i), 'relativeTime': time_relative}
        times = times.append(row, ignore_index=True)
    
    times['normalized'] = times.apply (lambda row: normalized_time(row, times), axis=1)
    return times
```

# Plots


```python
def create_plot(arcs, times, side):
    if side == 'lr':
        plt.plot(times['normalized'], arcs['arc_left'])
        plt.plot(times['normalized'], arcs['arc_right'])

        plt.xlabel('tijd (relatief)')
        plt.ylabel('graden')
        plt.ylim((0,200))
        plt.legend()
        plt.show()
```

# Functies aanroepen:


```python
combinedData = pd.read_csv('cleanedData_ex3.csv')      # pd.read_csv("/data/pepper/combined_single_body.csv")

for i in range(1, 6):
    person_one = combinedData.loc[combinedData['pNum'] == i]
    person_one_rotated = rotate_body(person_one)
    p1_e1_arcs = get_arcs(person_one_rotated, 3, 'lr')
    p1_e1_arcs.to_csv('arcs_' + str(i) + '.csv')
#     p1_e1_times = normalize_time(person_one, 3, 'lr')
#     create_plot(p1_e1_arcs, p1_e1_times, 'lr')
# side is nog niet overal toegevoegd, omdat ik nog geen dataframes heb met side erin.
```
