---
layout: post
title: "Python project and explore"
date: 2014-07-07 17:21:08 +0200
comments: true
categories: ["python", "morphometrics"]
keywords: "morphometrics, numpy, pandas, procrustes, projection, PCA"
description: "This post shows the orthogonal projection on superimposed data as well as exploratory PCA"
---

Since Procrustes superimposition introduces the data into a curved shape space which corresponds to a hyperhemisphere of radius 1 with numerous dimensions (as defined by Rohlf, 1999[^1]), usual multivariate statistical methods are not readily applicable to such data. This data must be projected to a flat, Euclidean tangent space, that is in contact with the hyperhemisphere in one point, taken as a mean shape of the whole dataset. This projection can be applied only if the variation in shapes in not overtly large so that their "shadows" on a flat space are not too far apart. In this post, data will be generated as usual and Procrustes procedures will be taken from previous posts, using defined functions *centsiz*, *transScale*, *mshapr*, *pProc* and *pGP*.      

```python Importing libraries, generating data and utility functions
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import Pycluster as pyc
import brewer2mpl
from mpl_toolkits.mplot3d import Axes3D
from sklearn.decomposition import PCA

coordstest = np.vstack([np.random.uniform(175, 210, 10),    #slightly larger variation
                        np.random.uniform(175, 210, 10)]).T 
coords = np.vstack([np.random.multivariate_normal(coordstest[i,:], [[10,0],[0,1]], 200) for i in range(10)])
coordinates = pd.Series((np.arange(0,10).reshape(-1,1)*np.ones(200).reshape(1,-1)).flatten()) #coordinates column
individuals = np.tile(np.arange(0,200),10) #individuals column
allCoords = pd.DataFrame(coords, columns = ['x','y'])
allCoords['coordinates'] = coordinates
allCoords['individuals'] = individuals
allCoords = allCoords.sort_index(by = ['individuals','coordinates'])
```
After Procrustes superimposition, data can be projected orthogonally and stereographically. More common method is the orthogonal projection which minimizes lagre shape differences between landmark configurations. The procedure is based on Rohlf, 1999, suggesting that the matrix of aligned centered preshapes should be multiplied by the mean shape of unit centroid size, subtracted from the identity matrix of comparable dimensionality (Claude, 2008).   

```python Orthogonal projection of superimposed data, Procrustes superimposition and projection
def ortproj(A, numland, dim, numind): #where A is a pandas dataframe (tempCoords), numind =  landmarks, numind =  individuals
  mshape = A.iloc[:,0:2].groupby(A.iloc[:,2]).mean()
  mshStd = mshape/centsize(mshape)
  mshMel = pd.melt(mshStd).drop('variable',1).T
  #mshRep = pd.concat([mshMel]*numind, ignore_index = True) #calculate repeating size standardized meanShape
  mshRep = np.dot(np.ones([numind,1]), mshMel)
  simDia = np.identity(numland * dim) #identity matrix
  #lonWid = A[["x","y"]].values.reshape(numind,numland*dim) #must have column names "x" and "y" for coordinates
  lonWidX = A["x"].values.reshape(numind,numland)
  lonWidY = A["y"].values.reshape(numind,numland)
  lonWid = np.concatenate((lonWidX, lonWidY), axis = 1)
  temp1 = simDia - np.dot(mshMel.T, mshMel)
  Xi = np.dot(lonWid, temp1) #orthogonal according to Rholf
  Xproj = Xi + mshRep
  return Xproj

procCoords = pGP(allCoords, 200,2,10) #perform Procrustes superimposition
procoordinates = np.tile(np.arange(0,10),200)
procindividuals = allCoords['individuals']
procCoords['coordinates'] = procoordinates
procCoords['individuals'] = sort(procindividuals)
projCoords = ortproj(procCoords, 10, 2, 200) #perform orthogonal projection
```
Numpy array projCoords holds wide representation of projected data (rows are individuals, while columns are all landmarks, first all x and then y coordinates), ready to be used in PCA or any other multivariate method. In order to illustrate potential grouping in PCA morphospace, since the data is randomly generated, a kmeans clustering will be performed with 3 groups, just to effectively split the data. PCA can be done in python in numerous ways, but in this post a PCA decomposition from *scikit-learn* package is used.

```python PCA, cluster analysis and plots
pca = PCA(n_components = 3)
pca.fit(projCoords)
print(pca.explained_variance_ratio_)

projCoordsP = pca.transform(projCoords) #get the individual scores on PC axes

clusters = pd.Series(pyc.kcluster(projCoordsP, nclusters = 3, method = 'a', dist = 'e')[0])
set2 = brewer2mpl.get_map('RdYlBu', 'diverging', 3).mpl_colors #generate nice colors
projCoordsPa = np.column_stack((projCoordsP, clusters)) #concatenate all for sequential plot-building

group1PCA = projCoordsPa[projCoordsPa[:,3] == 0] #split by group for sequential plot-building
group2PCA = projCoordsPa[projCoordsPa[:,3] == 1]
group3PCA = projCoordsPa[projCoordsPa[:,3] == 2]
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.plot(group1PCA[:,0], group1PCA[:,1], group1PCA[:,2], 'o', c = set2[0], alpha = 1, label = 'Group1')
ax.plot(group2PCA[:,0], group2PCA[:,1], group2PCA[:,2], 'o', c = set2[1], alpha = 1, label = 'Group2')
ax.plot(group3PCA[:,0], group3PCA[:,1], group3PCA[:,2], 'o', c = set2[2], alpha = 1, label = 'Group3')
ax.set_title("PCA with grouping according to kmeans 3 group solution")
plt.legend(loc='upper left')
ax.set_xlabel('PC1 (14.45%)')
ax.set_ylabel('PC2 (13.29%)')
ax.set_zlabel('PC3 (12.46%)')
plt.show()
```
Since the data is randomly generated, there is not much structured variation in it, as three of the first PC axes describe 14.45%, 13.29% and 12.46% of total sample variability, respectively. On the other hand, kmeans forces the data into three groups, which is useful for demonstration of plotting in matplotlib with 3D axes, and a groupping structure in PCA scatterplots (Figure 1), similar to real-world data. 

{% img center /images/TDPCAscatter.png 650 484 'PCA of projected and superimposed data' %}

[^1]: Rohlf, J.F. 1999. Shape statistics: Procrustes superimpositions and tangent spaces. *Journal of Classification* **16**: 197-223.