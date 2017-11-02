

```python
def relative_time(row, frame):
    index = frame.index.get_loc(row.name)
    
    if index == 0:
        return 0
    
    first_row = frame.iloc[0]
    time1 = row['time']
    time2 = first_row['time']
    
    dt1 = parser.parse(time1)
    dt2 = parser.parse(time2)
    delta = relativedelta.relativedelta(dt1, dt2)
    microseconds = delta.seconds * 1000000 + delta.microseconds
    return microseconds

def normalized_time(row, frame):
    total_time = frame['relativeTime'].max()
    
    relative_time = row['relativeTime']
    normalized = relative_time/total_time
    return normalized
```
