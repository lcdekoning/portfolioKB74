
In this weeks assignments you will join and aggregate data from a collection of user and restaurant in Mexico. The collections we use is from: https://archive.ics.uci.edu/ml/datasets/Restaurant+%26+consumer+data

We have placed three files in the folder of this tutorial: 
- `userprofile.csv`: describes several atributes of the users
- `geoplaces2.csv`:  describes several attributes of the restaurants
- `rating_final.csv`: describes how the users rated the restaurant, the food and the service on a scale [0-2]

## part 1 ##

The first assignment is to estimate whether people with a larger budget are generally more satisfied with the restaurant they choose.

Create an RDD `rating` of the restaurant rating (the first rating) in `rating_final.csv`.


```python
filename = 'rating_final.csv'
rating = sc.textFile(filename)
ratingheader = rating.first()
```


```python
rating.take(5)
```




    ['userID,placeID,rating,food_rating,service_rating',
     'U1077,135085,2,2,2',
     'U1077,135038,2,2,1',
     'U1077,132825,2,2,2',
     'U1077,135060,1,2,2']



Also create an RDD `userbudget` in which you load the `userID` and `budget` from `userprofiles.csv`.


```python
filename = 'userprofile.csv'
userbudget = sc.textFile(filename)
userbudgetheader = userbudget.first()

def splitkey(x):
    s = x.split(',')
    return (s[0], s[17])
    
userbudget = userbudget.map(splitkey)
userbudget.take(5)
```




    [('userID', 'budget'),
     ('U1001', 'medium'),
     ('U1002', 'low'),
     ('U1003', 'low'),
     ('U1004', 'medium')]



To join the `userbudget` with the `userrating`, you must convert the `ratings` to a (userID, rating) structure (we don't need the placeID for this assignment).


```python
def splitkey(x):
    s = x.split(',')
    return (s[0], s[2])

rating = rating.map(splitkey)
```


```python
rating.take(5)
```




    [('userID', 'rating'),
     ('U1077', '2'),
     ('U1077', '2'),
     ('U1077', '2'),
     ('U1077', '1')]



Join `useratings` and `userbudget`, and map the result to a `(budget, rating)` structure. Don't forget to convert rating to an int (with `int()`).


```python
rating_budget = userbudget.join(rating)
rating_budget = rating_budget.values()
rating_budget = rating_budget.filter(lambda x: x[1] != 'rating')
rating_budget = rating_budget.map(lambda x: (x[0], int(x[1])))
rating_budget.take(5)
```




    [('low', 1), ('low', 1), ('low', 2), ('low', 1), ('low', 1)]



Group the result by budget (the key), and compute the average rating. To compute the average of a list `l` in python you can divide `sum(l)` by `len(l)`.


```python
aTuple = (0,0) # As of Python3, you can't pass a literal sequence to a function.
grouped = rating_budget.aggregateByKey(aTuple, lambda a,b: (a[0] + b,    a[1] + 1),
                                       lambda a,b: (a[0] + b[0], a[1] + b[1]))
finalResult = grouped.mapValues(lambda v: v[0]/v[1]).collect()
print(finalResult)
```

    [('low', 1.1360759493670887), ('high', 1.4761904761904763), ('medium', 1.2084468664850136), ('?', 1.2318840579710144)]


Indeed, it seems that users with a higher budget are more satisfied with the restaurants they visit.

## Part 2 ##

The next assignment is to estimate whether users ratings are affected by the distance between where they live and where the restaurant is. 

We want to compute the distance between the user's home and the restaurant for every rating. Both positions can be looked up from the userprofiles and the places.

Create an RDD `userpos` with the userID, latitude and longitude from `userprofiles.csv`. To use the location, put latitude and longitude inside a tuple.


```python
filename = 'userprofile.csv'
userprofiles = sc.textFile(filename)

def splitkey(x):
    s = x.split(',')
    return (s[0], (s[1], s[2]))
    
userpos = userprofiles.map(splitkey)

userpos = userpos.filter(lambda x: x[0] != 'userID')

userpos.take(5)
```




    [('U1001', ('22.139997', '-100.978803')),
     ('U1002', ('22.150087', '-100.983325')),
     ('U1003', ('22.119847', '-100.946527')),
     ('U1004', ('18.867', '-99.183')),
     ('U1005', ('22.183477', '-100.959891'))]



Create a broadcast variable that contains a dictionary from which you can lookup a users position based on their ID.


```python
bc_userpos = sc.broadcast(userpos.collectAsMap())
```


```python

```




    {'U1001': ('22.139997', '-100.978803'),
     'U1002': ('22.150087', '-100.983325'),
     'U1003': ('22.119847', '-100.946527'),
     'U1004': ('18.867', '-99.183'),
     'U1005': ('22.183477', '-100.959891'),
     'U1006': ('22.15', '-100.983'),
     'U1007': ('22.118464', '-100.938256'),
     'U1008': ('22.122989', '-100.923811'),
     'U1009': ('22.159427', '-100.990448'),
     'U1010': ('22.190889', '-100.998669'),
     'U1011': ('23.724972', '-99.152856'),
     'U1012': ('18.813348', '-99.243697'),
     'U1013': ('22.174624', '-100.993873'),
     'U1014': ('23.751607', '-99.170108'),
     'U1015': ('22.12676', '-100.905209'),
     'U1016': ('22.156247', '-100.977402'),
     'U1017': ('18.952615', '-99.201616'),
     'U1018': ('22.190949', '-100.917902'),
     'U1019': ('22.153385', '-100.975294'),
     'U1020': ('18.878189', '-99.222969'),
     'U1021': ('23.730569', '-99.171883'),
     'U1022': ('22.146708', '-100.964355'),
     'U1023': ('23.752943', '-99.166589'),
     'U1024': ('22.154021', '-100.976028'),
     'U1025': ('22.125603', '-100.907844'),
     'U1026': ('23.733', '-99.133'),
     'U1027': ('22.16515', '-100.987015'),
     'U1028': ('23.752874', '-99.169242'),
     'U1029': ('22.151796', '-100.989075'),
     'U1030': ('18.844818', '-99.182758'),
     'U1031': ('23.735698', '-99.159851'),
     'U1032': ('22.169184', '-100.986843'),
     'U1033': ('22.15', '-100.983'),
     'U1034': ('22.137178', '-101.013169'),
     'U1035': ('18.839671', '-99.223897'),
     'U1036': ('22.160572', '-100.989418'),
     'U1037': ('22.15031', '-100.900536'),
     'U1038': ('22.125786', '-100.943705'),
     'U1039': ('23.738067', '-99.139906'),
     'U1040': ('18.895187', '-99.18039'),
     'U1041': ('18.935191', '-99.23624'),
     'U1042': ('18.925773', '-99.219589'),
     'U1043': ('23.77103', '-99.167082'),
     'U1044': ('18.95298', '-99.260789'),
     'U1045': ('22.156724', '-100.984268'),
     'U1046': ('22.144415', '-100.933097'),
     'U1047': ('22.142429', '-100.949147'),
     'U1048': ('22.142208', '-101.022785'),
     'U1049': ('22.15', '-100.983'),
     'U1050': ('23.758815', '-99.171216'),
     'U1051': ('18.877719', '-99.22299'),
     'U1052': ('22.138055', '-100.936005'),
     'U1053': ('22.175833', '-100.986671'),
     'U1054': ('22.150683', '-100.975342'),
     'U1055': ('22.143289', '-100.987683'),
     'U1056': ('22.168997', '-100.974376'),
     'U1057': ('22.196624', '-100.91217'),
     'U1058': ('22.205802', '-100.986081'),
     'U1059': ('18.988278', '-99.097023'),
     'U1060': ('23.715238', '-99.158864'),
     'U1061': ('22.140388', '-100.937321'),
     'U1062': ('22.195826', '-101.006317'),
     'U1063': ('23.745096', '-99.164357'),
     'U1064': ('22.139511', '-100.957002'),
     'U1065': ('23.733665', '-99.105617'),
     'U1066': ('18.890695', '-99.157104'),
     'U1067': ('23.752269', '-99.168605'),
     'U1068': ('23.752269', '-99.168605'),
     'U1069': ('19.347641', '-99.130229'),
     'U1070': ('23.753237', '-99.166868'),
     'U1071': ('22.154339', '-100.975342'),
     'U1072': ('18.86826', '-99.212033'),
     'U1073': ('22.15', '-100.983'),
     'U1074': ('18.917', '-99.25'),
     'U1075': ('22.167575', '-100.960364'),
     'U1076': ('22.156469', '-100.98554'),
     'U1077': ('22.156469', '-100.98554'),
     'U1078': ('22.172109', '-100.963199'),
     'U1079': ('18.917', '-99.25'),
     'U1080': ('23.743793', '-99.163397'),
     'U1081': ('22.207749', '-100.942383'),
     'U1082': ('23.753061', '-99.166095'),
     'U1083': ('22.13392', '-101.028373'),
     'U1084': ('22.172524', '-101.005758'),
     'U1085': ('22.196787', '-100.936335'),
     'U1086': ('22.157281', '-100.98444'),
     'U1087': ('23.753336', '-99.167984'),
     'U1088': ('22.142748', '-100.940664'),
     'U1089': ('22.162562', '-100.99313'),
     'U1090': ('22.158473', '-100.984268'),
     'U1091': ('22.154677', '-100.949013'),
     'U1092': ('22.125365', '-100.947888'),
     'U1093': ('18.871654', '-99.183251'),
     'U1094': ('23.73944', '-99.160548'),
     'U1095': ('22.179865', '-101.018547'),
     'U1096': ('22.1455', '-100.939593'),
     'U1097': ('22.145471', '-100.939588'),
     'U1098': ('22.182571', '-100.963232'),
     'U1099': ('22.184862', '-100.970535'),
     'U1100': ('18.879729', '-99.067106'),
     'U1101': ('22.150891', '-100.974891'),
     'U1102': ('22.137131', '-100.941151'),
     'U1103': ('23.752265', '-99.16859'),
     'U1104': ('22.149005', '-100.978153'),
     'U1105': ('22.120019', '-100.950991'),
     'U1106': ('18.927072', '-99.173584'),
     'U1107': ('23.742409', '-99.171889'),
     'U1108': ('22.143524', '-100.987562'),
     'U1109': ('22.303308', '-101.05468'),
     'U1110': ('18.871678', '-99.183263'),
     'U1111': ('22.143078', '-100.908523'),
     'U1112': ('22.143078', '-100.908523'),
     'U1113': ('22.137343', '-100.913935'),
     'U1114': ('22.177726', '-101.014094'),
     'U1115': ('22.138127', '-100.920512'),
     'U1116': ('22.156469', '-100.98554'),
     'U1117': ('18.875641', '-99.220737'),
     'U1118': ('18.940062', '-99.228172'),
     'U1119': ('18.96479', '-99.260017'),
     'U1120': ('22.121857', '-100.904279'),
     'U1121': ('18.871674', '-99.183253'),
     'U1122': ('22.169601', '-100.991821'),
     'U1123': ('23.753112', '-99.168567'),
     'U1124': ('22.137072', '-100.918865'),
     'U1125': ('22.19204', '-100.956935'),
     'U1126': ('22.15421', '-100.942233'),
     'U1127': ('18.943935', '-99.206532'),
     'U1128': ('22.187236', '-100.994213'),
     'U1129': ('23.728798', '-99.134047'),
     'U1130': ('23.733', '-99.133'),
     'U1131': ('22.138245', '-100.910948'),
     'U1132': ('22.15', '-100.983'),
     'U1133': ('18.886698', '-99.114979'),
     'U1134': ('22.149654', '-100.99861'),
     'U1135': ('22.170396', '-100.949936'),
     'U1136': ('22.149607', '-100.997235'),
     'U1137': ('22.144803', '-100.944623'),
     'U1138': ('22.152884', '-100.939663')}



Also create an RDD `placepos` that contains the ID and position of places in `geoplaces2.csv`.


```python
filename = 'geoplaces2.csv'
geoplaces = sc.textFile(filename)

def splitkey(x):
    s = x.split(',')
    return (s[0], (s[1], s[2]))
    
placepos = geoplaces.map(splitkey)

placepos = placepos.filter(lambda x: x[0] != 'placeID')

placepos.take(5)
```




    [('134999', ('18.915421', '-99.184871')),
     ('132825', ('22.1473922', '-100.983092')),
     ('135106', ('22.1497088', '-100.9760928')),
     ('132667', ('23.7526973', '-99.1633594')),
     ('132613', ('23.7529035', '-99.165076'))]



And create a similar broadcast variable to lookup the position of a place based in it's ID.


```python
bc_geopos = sc.broadcast(placepos.collectAsMap())
```




    {'132560': ('23.7523041', '-99.1669133'),
     '132561': ('23.726819', '-99.1265059'),
     '132564': ('23.7309245', '-99.1451848'),
     '132572': ('22.1416471', '-100.9927118'),
     '132583': ('18.9222904', '-99.234332'),
     '132584': ('23.7523648', '-99.1652879'),
     '132594': ('23.7521677', '-99.165709'),
     '132608': ('23.7588052', '-99.1651297'),
     '132609': ('23.7602683', '-99.1658646'),
     '132613': ('23.7529035', '-99.165076'),
     '132626': ('23.7375834', '-99.1351318'),
     '132630': ('23.7529305', '-99.1644725'),
     '132654': ('23.7355234', '-99.1295877'),
     '132660': ('23.7529428', '-99.1646791'),
     '132663': ('23.7525107', '-99.1669536'),
     '132665': ('23.7367977', '-99.1342413'),
     '132667': ('23.7526973', '-99.1633594'),
     '132668': ('23.738212', '-99.1519547'),
     '132706': ('23.7292162', '-99.1323571'),
     '132715': ('23.7324226', '-99.1586602'),
     '132717': ('23.7318602', '-99.1504365'),
     '132723': ('22.1489337', '-101.019845'),
     '132732': ('23.7543569', '-99.171288'),
     '132733': ('23.7527071', '-99.1625655'),
     '132740': ('23.7521965', '-99.1666317'),
     '132754': ('22.1477379', '-100.9906163'),
     '132755': ('22.153324', '-101.0195459'),
     '132766': ('18.9101777', '-99.2315438'),
     '132767': ('18.8820871', '-99.1630268'),
     '132768': ('18.9257734', '-99.2326355'),
     '132773': ('18.8699929', '-99.2103195'),
     '132825': ('22.1473922', '-100.983092'),
     '132830': ('22.1508494', '-100.9397522'),
     '132834': ('22.156469', '-100.98554'),
     '132845': ('22.1262926', '-100.9007764'),
     '132846': ('22.1422732', '-100.9426537'),
     '132847': ('22.1443365', '-100.9373825'),
     '132851': ('22.136872', '-100.9345736'),
     '132854': ('22.1378633', '-100.9383273'),
     '132856': ('22.1513782', '-100.9746337'),
     '132858': ('22.1312917', '-100.9371941'),
     '132861': ('22.1480965', '-101.0173023'),
     '132862': ('22.1506429', '-100.9870148'),
     '132866': ('22.1412198', '-100.9313107'),
     '132869': ('22.1412384', '-100.9239252'),
     '132870': ('22.1430781', '-100.9354788'),
     '132872': ('22.1735955', '-100.9946027'),
     '132875': ('22.1499013', '-100.9937793'),
     '132877': ('22.1353639', '-100.9349477'),
     '132884': ('22.1395776', '-101.0278863'),
     '132885': ('22.1795169', '-100.9584358'),
     '132921': ('22.150305', '-100.9891337'),
     '132922': ('22.1511348', '-100.9823115'),
     '132925': ('22.1534997', '-100.976243'),
     '132937': ('22.1500193', '-100.9799203'),
     '132951': ('22.1544736', '-100.9858091'),
     '132954': ('22.1603808', '-100.9880447'),
     '132955': ('22.147622', '-101.0102749'),
     '132958': ('22.1449787', '-101.0056829'),
     '134975': ('18.940828', '-99.215426'),
     '134976': ('18.916654', '-99.22711'),
     '134983': ('18.948657', '-99.235361'),
     '134986': ('18.928798', '-99.239513'),
     '134987': ('18.932725', '-99.225211'),
     '134992': ('18.936683', '-99.247366'),
     '134996': ('18.923429', '-99.216413'),
     '134999': ('18.915421', '-99.184871'),
     '135000': ('18.870565', '-99.226938'),
     '135001': ('18.941859', '-99.241927'),
     '135011': ('18.91061', '-99.169539'),
     '135013': ('18.917441', '-99.165945'),
     '135016': ('18.869347', '-99.209944'),
     '135018': ('18.859803', '-99.222164'),
     '135019': ('18.875011', '-99.159422'),
     '135021': ('18.933537', '-99.222497'),
     '135025': ('22.14955', '-100.97797'),
     '135026': ('22.148665', '-101.001273'),
     '135027': ('22.147145', '-100.974494'),
     '135028': ('22.146658', '-100.987219'),
     '135030': ('22.14788', '-100.989472'),
     '135032': ('22.152481', '-100.973486'),
     '135033': ('22.14161', '-100.973142'),
     '135034': ('22.140517', '-101.021422'),
     '135035': ('22.145813', '-101.018032'),
     '135038': ('22.155651', '-100.977767'),
     '135039': ('22.145893', '-100.97487'),
     '135040': ('22.135617', '-100.969709'),
     '135041': ('22.15106', '-100.977659'),
     '135042': ('22.159357', '-100.973411'),
     '135043': ('22.185756', '-100.944518'),
     '135044': ('22.141848', '-100.997475'),
     '135045': ('22.151189', '-100.98179'),
     '135046': ('22.141282', '-101.002958'),
     '135047': ('22.150921', '-100.993828'),
     '135048': ('22.142017', '-100.999246'),
     '135049': ('22.135011', '-101.0286'),
     '135050': ('22.174887', '-100.970825'),
     '135051': ('22.151189', '-100.977058'),
     '135052': ('22.150981', '-100.977412'),
     '135053': ('22.178931', '-101.012861'),
     '135054': ('22.140626', '-100.915657'),
     '135055': ('22.148854', '-101.008472'),
     '135057': ('22.145992', '-100.955118'),
     '135058': ('22.165587', '-101.001273'),
     '135059': ('22.145108', '-100.989547'),
     '135060': ('22.156883', '-100.978485'),
     '135062': ('22.153703', '-100.979033'),
     '135063': ('22.156724', '-100.975556'),
     '135064': ('22.154687', '-100.996617'),
     '135065': ('22.14958', '-100.999557'),
     '135066': ('22.16835', '-100.972466'),
     '135069': ('22.140129', '-100.944872'),
     '135070': ('22.152918', '-100.915164'),
     '135071': ('22.126375', '-100.910926'),
     '135072': ('22.149192', '-101.002936'),
     '135073': ('22.147175', '-100.974269'),
     '135074': ('22.149689', '-100.999525'),
     '135075': ('22.139573', '-100.991564'),
     '135076': ('22.181017', '-100.973614'),
     '135079': ('22.156376', '-100.998355'),
     '135080': ('22.145008', '-100.997969'),
     '135081': ('22.164842', '-100.960493'),
     '135082': ('22.151448', '-100.915099'),
     '135085': ('22.150802', '-100.98268'),
     '135086': ('22.141421', '-101.013955'),
     '135088': ('18.8760113', '-99.2198896'),
     '135104': ('23.7529821', '-99.1684341'),
     '135106': ('22.1497088', '-100.9760928'),
     '135108': ('22.1362534', '-100.9335852'),
     '135109': ('18.9217848', '-99.2353499')}



To compute the distance an accurate approximation is the Vincenty distance in the geopy library (use `pip install geopy` to install). 

Here is an example to compute the distance:


```python
from geopy.distance import vincenty
vincenty((31.8300167,35.0662833), (31.83,35.0708167)).meters
```




    429.16765838976664



Now, map the ratings, so that you retrieve the position of the user and the position of the restaurant, and compute the vincenti distance between them. Output the distance and rating.


```python

```




    [('2', 693.4067254748844),
     ('2', 806.8757681276663),
     ('2', 1036.3302977034452),
     ('1', 729.1540436901495),
     ('1', 80.87798147571374)]



Now average the distance per rating.


```python

```




    [('1', 10620.704846132321),
     ('0', 32532.24377565323),
     ('2', 27352.013001884887)]



It seems that there is no linear relation between the distance to the restaurant and the given rating.
