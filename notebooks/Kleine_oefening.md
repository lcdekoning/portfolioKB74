

```python
%matplotlib inline
import matplotlib.pyplot as plt
import seaborn as sns; sns.set()  # for plot styling
import numpy as np
import pandas as pd
```


```python
from sklearn.datasets.samples_generator import make_blobs
X, y_true = make_blobs(n_samples=200, centers=3,
                       cluster_std=0.60, random_state=0)
plt.scatter(X[:, 0], X[:, 1], s=50);
```

    /opt/jupyterhub/anaconda/lib/python3.6/site-packages/matplotlib/font_manager.py:1316: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))



![png](output_1_1.png)



```python
X
```




    array([[ -1.65104622e+00,   3.44598961e+00],
           [  7.67522789e-01,   4.39759671e+00],
           [  1.06923853e+00,   4.53068484e+00],
           [ -1.46826903e+00,   3.26765447e+00],
           [  1.15521298e+00,   5.09961887e+00],
           [ -1.06295223e+00,   2.20755388e+00],
           [  2.36790645e+00,   5.52190878e-01],
           [  1.75644805e+00,   2.05538289e+00],
           [  1.05241733e+00,   4.54498095e+00],
           [ -1.64129611e+00,   2.68097255e+00],
           [  7.34363910e-01,   5.03725437e+00],
           [ -1.11064012e+00,   2.82213820e+00],
           [  1.43289271e+00,   4.37679234e+00],
           [  1.21387411e+00,   3.64795042e+00],
           [  4.31891060e-01,   4.33495456e+00],
           [ -1.18652985e+00,   2.78427720e+00],
           [  2.52706430e+00,   6.17812202e-01],
           [  2.14043942e+00,   7.06066610e-01],
           [  4.88382309e-01,   3.26801777e+00],
           [ -2.74531469e+00,   4.15657798e+00],
           [ -1.17979111e+00,   3.12767494e+00],
           [  1.60841463e+00,   4.01800537e-01],
           [  1.00372519e+00,   4.19147702e+00],
           [  1.54462126e+00,   4.21078127e+00],
           [ -2.88024255e+00,   2.30437816e+00],
           [  7.93137001e-03,   4.17614316e+00],
           [  1.66909648e+00,  -4.36378231e-01],
           [ -1.98539037e+00,   2.05520738e+00],
           [ -1.01214966e+00,   3.60254338e+00],
           [ -1.56387985e+00,   2.85349910e+00],
           [  1.25566754e+00,   3.38204112e+00],
           [  9.82570091e-01,   5.37530962e+00],
           [ -1.55220688e+00,   2.74574995e+00],
           [  7.28098690e-01,   3.85531444e+00],
           [  1.56724897e+00,   1.78090633e-02],
           [  2.36960214e+00,   9.50716912e-01],
           [  1.19008992e+00,   4.72773123e+00],
           [  2.22322228e+00,   8.38773426e-01],
           [  2.43934644e+00,  -7.25099666e-02],
           [ -2.63274574e+00,   2.63109786e+00],
           [  1.48859977e+00,   6.51633844e-01],
           [ -1.91834916e+00,   2.68331024e+00],
           [ -2.58802708e+00,   3.13117134e+00],
           [ -1.11301311e+00,   3.69899000e+00],
           [  2.09680545e+00,   4.84741412e+00],
           [  1.71444449e+00,   5.02521524e+00],
           [  5.71670482e-01,   4.32288566e+00],
           [ -8.85798374e-01,   2.64585078e+00],
           [  2.72396035e-01,   5.46996004e+00],
           [  1.01618041e+00,   4.48527047e+00],
           [  5.95676822e-01,   4.08614263e+00],
           [  1.97553917e+00,   7.18989132e-01],
           [ -1.91828017e+00,   2.60516867e+00],
           [ -1.62150422e+00,   4.27191636e+00],
           [ -1.59322841e+00,   3.52998589e+00],
           [ -1.73896306e+00,   1.94799775e+00],
           [  2.14917144e+00,   1.03697228e+00],
           [  2.60778282e+00,   1.08890025e+00],
           [  1.32222457e+00,   4.17880807e+00],
           [  1.86922139e+00,   5.44132083e+00],
           [ -2.32745910e+00,   2.10985176e+00],
           [ -5.38023054e-01,   3.01641891e+00],
           [  9.59360742e-01,   4.56078645e+00],
           [  1.69687788e+00,   7.54910622e-01],
           [  1.83375842e+00,   7.54036153e-01],
           [ -1.35509780e+00,   3.28318856e+00],
           [  1.13078931e+00,   9.35620856e-01],
           [  1.50757419e+00,   1.56787343e+00],
           [ -1.50372568e+00,   1.92385320e+00],
           [  2.01432256e+00,   1.92566929e+00],
           [  1.86985974e+00,  -1.07938624e-01],
           [  3.33818506e-01,   4.93645836e+00],
           [  7.15177948e-01,   5.41334556e+00],
           [ -1.76657343e+00,   3.13991579e+00],
           [  2.24592863e-01,   4.77028154e+00],
           [ -1.49720702e+00,   3.21418433e+00],
           [  1.78194802e+00,   9.08151155e-01],
           [  3.41085289e+00,   8.72309369e-01],
           [  1.48170052e+00,   6.90074595e-01],
           [ -3.12240736e+00,   3.28167398e+00],
           [ -1.90375655e+00,   2.62926599e+00],
           [  1.08272576e+00,   4.06271877e+00],
           [  9.14338767e-01,   4.55014643e+00],
           [  3.48515439e+00,   1.46435135e+00],
           [  2.36923352e+00,   7.94735861e-01],
           [  1.49493180e+00,   3.85848832e+00],
           [  6.70478769e-01,   4.04094275e+00],
           [ -1.74572014e+00,   3.01190457e+00],
           [  4.59534668e-01,   5.44982630e+00],
           [  2.15527162e+00,   1.27868252e+00],
           [ -2.22131717e+00,   2.73050691e+00],
           [ -1.07859101e+00,   2.20451529e+00],
           [  2.29469533e+00,  -7.65891994e-01],
           [  4.53791789e-01,   3.95647753e+00],
           [  2.76808540e+00,   1.08782923e+00],
           [  8.15468056e-01,   4.78526116e+00],
           [  1.21767506e+00,   3.89290127e+00],
           [ -1.59780244e+00,   2.50977534e+00],
           [ -1.93960658e+00,   2.18943582e+00],
           [  2.47019077e+00,   1.31451315e+00],
           [  1.84287117e+00,   7.26928839e-02],
           [  1.89593761e+00,   5.18540259e+00],
           [ -1.81469750e+00,   3.29009724e+00],
           [  5.72793810e-01,   4.08805543e+00],
           [ -1.60712495e+00,   3.56452854e+00],
           [ -1.84892963e-03,   4.58145668e+00],
           [  2.71506328e+00,   1.29082190e+00],
           [  4.38990142e-01,   4.53592883e+00],
           [ -5.55523811e-01,   4.69595848e+00],
           [  1.61152972e+00,   1.82347242e+00],
           [  6.69786996e-01,   3.59540802e+00],
           [ -7.08184904e-01,   2.50421275e+00],
           [  1.26572308e+00,   6.20712897e-01],
           [ -6.46956784e-01,   3.42941343e+00],
           [  7.43873988e-01,   4.12240568e+00],
           [  1.24258802e+00,   4.50399192e+00],
           [  1.65991049e+00,   3.56289184e+00],
           [  2.62492001e+00,   9.50194405e-01],
           [ -1.88609638e+00,   2.24834407e+00],
           [ -1.53631328e+00,   3.01443916e+00],
           [  1.69747910e+00,   8.66123282e-01],
           [  1.77710994e+00,   1.18655254e+00],
           [ -1.14091533e+00,   1.97550822e+00],
           [  1.20212540e+00,   3.64414685e+00],
           [  1.67280531e+00,   6.59300571e-01],
           [  3.47138300e-01,   3.45177657e+00],
           [ -2.58043836e+00,   3.18844294e+00],
           [  8.93499638e-01,   1.01093082e+00],
           [ -2.20299950e+00,   2.47947561e+00],
           [  1.34471770e+00,   4.85711133e+00],
           [ -1.35863899e+00,   2.32200809e+00],
           [  1.43472182e+00,   1.30662037e+00],
           [  1.20083098e+00,   6.01671730e-01],
           [  1.16051297e+00,   1.16129868e+00],
           [  1.37964693e+00,   4.54826443e+00],
           [  1.86873582e+00,   9.56103760e-01],
           [  1.99619601e+00,   4.99576688e-01],
           [  1.54632313e+00,   4.21297300e+00],
           [  1.45513831e+00,  -2.91989981e-02],
           [ -1.23065895e+00,   2.84821990e+00],
           [ -1.60847383e+00,   3.60001708e+00],
           [ -1.79145759e+00,   2.74966896e+00],
           [  1.87271752e+00,   4.18069237e+00],
           [  1.34195197e+00,   5.93573847e-01],
           [  2.56936589e+00,   5.07048304e-01],
           [  7.89338559e-01,   4.33748653e+00],
           [ -1.94972418e+00,   3.48383870e+00],
           [ -2.31082012e+00,   3.91276067e+00],
           [  1.39263752e+00,   9.28962707e-01],
           [  5.94762432e-01,   4.70964730e+00],
           [  2.03169783e+00,   1.96807561e-01],
           [ -2.15405603e+00,   3.64456944e+00],
           [ -1.94213392e+00,   3.83970849e+00],
           [ -2.11821046e+00,   2.03478126e+00],
           [  1.41372442e+00,   4.38117707e+00],
           [  3.35320909e+00,   1.69958043e+00],
           [  2.51834185e+00,   1.39176615e+00],
           [  1.72955064e+00,   1.14729369e+00],
           [  5.59529363e-01,   4.21400660e+00],
           [  2.77180174e-01,   4.84428322e+00],
           [ -1.44553995e-01,   2.28187277e+00],
           [ -1.02192525e+00,   2.76820711e+00],
           [ -1.36219420e+00,   2.38333321e+00],
           [ -1.75783190e+00,   2.97449321e+00],
           [  1.68353782e+00,   4.19583243e+00],
           [  2.13979079e-01,   4.88542535e+00],
           [  2.04067185e+00,   4.54845114e-01],
           [ -1.06690610e+00,   3.13165795e+00],
           [  2.04505527e+00,   1.12515470e+00],
           [  1.27955338e+00,   1.05789418e+00],
           [  4.43598630e-01,   3.11530945e+00],
           [  2.43040639e+00,  -6.35709334e-02],
           [ -4.74920358e-02,   5.47425256e+00],
           [  2.74666646e+00,   1.54543482e+00],
           [ -1.12707416e+00,   2.64145039e+00],
           [ -1.10782972e+00,   2.92014479e+00],
           [  2.10616050e+00,   3.49513189e+00],
           [ -2.54576750e+00,   3.15025055e+00],
           [  5.14320434e-01,   4.62733684e+00],
           [  1.32000621e+00,   1.40428145e+00],
           [  1.10123507e+00,   4.88977075e+00],
           [ -1.68754414e+00,   2.24107546e+00],
           [  2.31102276e+00,   1.30380848e+00],
           [  3.22881491e+00,   1.13171965e+00],
           [  2.95195825e+00,  -3.44327355e-01],
           [  2.33812285e+00,   3.43116792e+00],
           [ -1.95866665e+00,   2.43008647e+00],
           [  1.36678633e+00,   6.34971633e-01],
           [  1.06269622e+00,   5.17635143e+00],
           [  2.13003529e+00,   5.19209620e+00],
           [ -9.67794989e-01,   3.12186125e+00],
           [ -1.70200643e+00,   2.46098693e+00],
           [  1.10550448e+00,   1.26389129e+00],
           [  1.57322172e+00,   4.83933793e-01],
           [  1.36155806e+00,   1.36638252e+00],
           [  2.73124907e+00,   2.49704755e-01],
           [ -1.93731055e+00,   3.91361274e+00],
           [  2.60137487e+00,   1.08799459e+00],
           [  1.16411070e+00,   3.79132988e+00],
           [  1.61990909e+00,   6.76452867e-02]])




```python
from sklearn.cluster import KMeans
kmeans = KMeans(n_clusters=4)
kmeans.fit(X)
y_kmeans = kmeans.predict(X)
```


```python
plt.scatter(X[:, 0], X[:, 1], c=y_kmeans, s=50, cmap='viridis')

centers = kmeans.cluster_centers_
plt.scatter(centers[:, 0], centers[:, 1], c='black', s=200, alpha=0.5);
```

    /opt/jupyterhub/anaconda/lib/python3.6/site-packages/matplotlib/font_manager.py:1316: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))



![png](output_4_1.png)



```python
from sklearn.metrics import pairwise_distances_argmin

def find_clusters(X, n_clusters, rseed=2):
    # 1. Randomly choose clusters
    rng = np.random.RandomState(rseed)
    i = rng.permutation(X.shape[0])[:n_clusters]
    centers = X[i]
    
    while True:
        # 2a. Assign labels based on closest center
        labels = pairwise_distances_argmin(X, centers)
        
        # 2b. Find new centers from means of points
        new_centers = np.array([X[labels == i].mean(0)for i in range(n_clusters)])
        
        # 2c. Check for convergence
        if np.all(centers == new_centers):
            break
        centers = new_centers
    
    return centers, labels

centers, labels = find_clusters(X, 3)
print(len(labels))
plt.scatter(X[:, 0], X[:, 1], c=labels,
            s=50, cmap='viridis');
```

    200


    /opt/jupyterhub/anaconda/lib/python3.6/site-packages/matplotlib/font_manager.py:1316: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))



![png](output_5_2.png)



```python
labels = KMeans(3, random_state=0).fit_predict(X)
plt.scatter(X[:, 0], X[:, 1], c=labels,
            s=50, cmap='viridis');
```

    /opt/jupyterhub/anaconda/lib/python3.6/site-packages/matplotlib/font_manager.py:1316: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))



![png](output_6_1.png)



```python
ages = pd.read_csv('/home/13040367/notebooks/ExploratoryDataAnalysis/ages.csv')
max_arcs = pd.read_csv('/home/13040367/notebooks/ExploratoryDataAnalysis/max_arcs.csv')
```


```python
list_of_ages = ages['age']
list_of_arcs_right = max_arcs['max_arc_right']
list_of_arcs_left = max_arcs['max_arc_left']

matrix_of_ages = list_of_ages.as_matrix(columns=None)
matrix_of_arcs_right = list_of_arcs.as_matrix(columns=None)
matrix_of_arcs_left = list_of_arcs.as_matrix(columns=None)

bekendeSchouderKlachten = {6:"l", 9: "l", 17:'r', 20: 'r', 22: 'r', 23:"lr", 24:"r", 34:"l", 37:"l", 45:"l", 46:"lr", 50:"lr", 57:"l"}
# bekendeSchouderKlachten.keys
```


```python
combined = []

for i in range(0,len(list_of_ages)):
    to_add = [[list_of_arcs_left[i], list_of_arcs_right[i]]]
    combined = combined + to_add
```


```python
array_combined = np.array(combined)
```


```python
centers, labels = find_clusters(array_combined, 3)
plt.scatter(array_combined[:, 0], array_combined[:, 1], c=labels,
            s=50, cmap='viridis');
```

    /opt/jupyterhub/anaconda/lib/python3.6/site-packages/matplotlib/font_manager.py:1316: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))



![png](output_11_1.png)



```python
centers, labels = find_clusters(array_combined, 3)
plt.scatter(array_combined[:, 0], array_combined[:, 1], c=labels,
            s=50, cmap='viridis');
```

    /opt/jupyterhub/anaconda/lib/python3.6/site-packages/matplotlib/font_manager.py:1316: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))



![png](output_12_1.png)



```python

```
