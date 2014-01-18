---
layout: post
title: "Random landmark monsters in python"
date: 2014-01-17 11:36:06 +0100
comments: true
categories: ["python", "morphometrics", "random"]
keywords: "landmarks, random, multivariate normal, python, scipy, numpy, pandas"
description: "This post shows python procedure for generating random landmark datasets"
---

Generating random datasets is facilitated by powerful algorithms in both R and python that are capable of drawing random numbers from predefined distributions. For simulating geometric morphometric data the only important constraint is correlation between x and y coordinates of landmarks. Spacing of landmarks in also important, especially if the aim is to generte life-like data set, but it is not essential (hopefully) for morphometric analyses to work. Python scipy stack (*numpy*, *scipy*, *pandas* and *matplotlib* + Ipython console) is a great budnle which enables both random number generators and great visualizations. The code presented in this post uses all of these libraries, especially *pandas*, which offer the basic data structure used for storing landmark data, DataFrame. Also, it can just be pasted into Ipython console window (afer installing any of the scipy stack python distributions). The basic idea is to use uniform distribution random number generator (line 7) in order to generate ranges for the spacing of landmark groups, and then multivariate normal random distribution for creating the two-column correlated data (x, y coordinates, line 8). This code will simulate the situation with 10 landmarks recorded on 200 individuals. Spacing of landmarks can be controlled by the ranges of uniform generators (175-220 in code), and the spread of landmarks is contolled by input covariance matrix for multivariate normal generators ([[3,0],[0,3]] in code).

```python Importing libraries and generating random landmarks
import numpy as np
import pandas as pd
import brewer2mpl as bmpl
import matplotlib.pyplot as plt
import scipy.interpolate

coordstest = np.vstack([np.random.uniform(175, 220, 10), np.random.uniform(175, 220, 10)]).T #genereta coordinate ranges - spacing of landmarks
coords = np.vstack([np.random.multivariate_normal(coordstest[i,:], [[3,0],[0,3]], 200) for i in range(10)]) #correlated x and y from multivariate normal
coordinates = (np.arange(0,10).reshape(-1,1)*np.ones(200).reshape(1,-1)).flatten() #generate factor coodinates each landmark has 200 recorded points
coordinates = pd.Series(coordinates)
allCoords = pd.DataFrame(coords, columns = ['x','y']) #coordinates DataFrame
allCoords['coordinates'] = coordinates
meanCoords = allCoords.groupby('coordinates').mean() #mean landmark coordiantes
ind = np.arange(0,10)
ind = pd.Series(ind)
meanCoords['ind'] = ind
```
allCoords contain all generated landmarks, while meanCoords is the collection of mean coordinates for each landmark. In order to visualize the generated data, a convenient "outline" can be drawn, connecting all landmarks smoothly. Since generated landmarks are not ordered in a way that permits simple connect-the-dots line between them, they should be rotated, and ordered differently. This can be done by using the centroid coordinates (from all landmarks) and the polar angle between the lines connecting centroid and each landmark. If polar angle is used, then landmarks (from meanCoords) can be ordered according to its value, clockwise. Polar angle can be calculated as the arctan between x and y coordinates of mean landmarks and the centroid. 

```python Calculating polar angle and re-ordering of the meanCoords
x = np.array(meanCoords['x']) #convenience function
y = np.array(meanCoords['y'])
points = np.array((x,y)).T
cent = (np.sum(meanCoords['x'])/len(meanCoords), np.sum(meanCoords['y'])/len(meanCoords)) #the overall centroid
angle = np.arctan2(points[:,1]-cent[1],points[:,0]-cent[0]) #summary polar angle of all points
points = pd.DataFrame(points, columns = ['x','y'])
angle = pd.Series(angle)
points['angle'] = angle
points = points.sort('angle') #sort by polar angle from the centroid
```

If a path would be drawn between the mean landmarks now, it would be irregular, and not too informative. One way of constructing the smooth connection between landmarks is to use interpolation algorithms that are part of *scipy.interpolate*. One issue with this approach is the connection between the first and the last landmark, since it must be added to points DataFrame as eleventh landmark, with x,y coords the same as for the first one, in order to complete the path. This added segment behaves erratically during interpolation so the generated figures might be distorted. But this will not happen always and all program routines could be re-run as long as the desired, nice, result emerges. 

```python Interpolation of the outline data
x_hull = np.array(points['x']) 
x_hull = np.append(x_hull, x_hull[0]) #add first landmark as the last one (x)
y_hull = np.array(points['y'])
y_hull = np.append(y_hull, y_hull[0]) #add first landmark as the last one (y)
t = np.zeros(x_hull.shape)
t[1:] = np.sqrt((x_hull[1:] - x_hull[:-1])**2 + (y_hull[1:] - y_hull[:-1])**2)
t = np.cumsum(t)
t /= t[-1]
nt = np.linspace(0, 1, 100)
x1 = scipy.interpolate.spline(t, x_hull, nt) #interpolated coordines for smooth lines
y1 = scipy.interpolate.spline(t, y_hull, nt)
```

Finally, plots are produced sequentially, first all landmarks, then mean landmarks, and finally the outline. 

```python Plotting
plt.scatter(allCoords['x'], allCoords['y'], s = 25, c = "#FFD699", edgecolors = 'none') #plot all sampled points
plt.scatter(x, y, c = ind, s = 60, cmap = bmpl.get_map('Set3', 'qualitative', 7).mpl_colormap) #plot means with color brewer palette
plt.plot(x1, y1, '--', c = "#97CAFF", lw = 3) #plot outlines
labels = ['Landmark {0}'.format(i) for i in range(10)] 
for label, x, y in zip(labels, points['x'], points['y']): #annotate mean landmarks by numbers
 plt.annotate(label, xy = (x, y+0.3),ha = 'right', va = 'bottom')
plt.grid()
```
{% img center /images/FigMonster1.png 600 466 'First monster' %}
{% img center /images/FigMonster2.png 600 445 'Second monster' %}

Generated "monsters" are just there to get the idea of a possible shape, although the create the impression of the "outline", such that all landmarks are sampled from the outer perimeter of the object. The code presented would not be complete if it wasn`t for the help from people from stackoverflow ([here](http://goo.gl/DWOCLJ), [here](http://goo.gl/y9Kpv3) and [here](http://goo.gl/xwL4Dz)).

<div id="disqus_thread"></div>
<script type="text/javascript">
/* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
var disqus_shortname = 'creativemorphometrics'; // required: replace example with your forum shortname
/* * * DON'T EDIT BELOW THIS LINE * * */
(function() {
var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
(document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
<a href="http://disqus.com" class="dsq-brlink">comments powered by <span class="logo-disqus">Disqus</span></a>