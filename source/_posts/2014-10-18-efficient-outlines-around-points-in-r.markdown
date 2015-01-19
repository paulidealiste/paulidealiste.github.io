---
layout: post
title: "Efficient outlines around points in R"
date: 2015-01-18 11:25:19 +0200
comments: true
categories: ["R", "utilities"]
keywords: "delaunay, outlines, points, alpha_shapes"
description: "This post is about the possible usage of alpha shapes for finding the outline around random points in a plane" 
---
Convex hull is one of the most widely used techniques for finding the minimum enclosing shape around a set of points in a plane, but for certain tasks it is necessery to find such shape which encloses concave parts of the outline as well. Convex hulls are also sensitive to localized point groups, far apart from the majority of other points. In these cases, hulls enclose a lot of empty space so that the possible underlining shape is obscured. In geometric morphometrics, outlines around the set of landmarks in the plane can provide the idea of shape, useful for visualization of the underlining shape (i.e. the shape described by these points). Possibly, such outlines may be analysed further, with the usual GM analytic procedures. Alpha shape[^1] is the generalization of the convex hull for a set of points in the plane which can be used for shape reconstruction, since the frontier of an alpha shape is a linear approximation of the original shape. In R, this technique is implemented in the *alphahull* library. For this post, randomly generated points-in-the-plane data will be used.

```r Generating data and loading the library
library(alphahull)
sampleData <- data.frame(x=runif(30, 10, 50), y=runif(30,10,50)) #with uniform number generators
```

For a set of points, alpha shape is based on the Delaunay triangulation and its corresponding Voronoi diagram, so they are found first, followed by alpha shape and alpha hull.

```r Calculating Delaunay triangulation, Voronoi diagram and alpha shape of the sample data
sampleDel <- delvor(sampleData) #Delaunay/Voronoi
sampleAlpha <- ashape(sampleData, alpha = 9) #Alpha shape
```
The value of the parameter alpha is determined with respect to the dimensionality of data points, since it represents the minimal distance for icnlusion or exclusion of the neighbouring points within the alpha shape procedure. 

```r Plotting of the corresponding shapes
plot(sampleDel, col=c(1, "darkturquoise", "grey80",1), lwd = c(2,2), cex = 0.1, xlab = NA, ylab = NA)
points(sampleData, col = "darkorange", pch = 16, cex = 1.5) #Plot of the Delaunay
plot(sampleAlpha, wlines = "del", col = c("goldenrod4",1, "darkturquoise"), lwd = c(2,4), cex = 0.1, xlab = NA, ylab = NA)
points(sampleData, col = "darkorange", pch = 16, cex = 1.3) #Plot of the alpha shape
```
When the value of alpha is selected taking into account the spacing between the points, the enclosing outline can be obtained so that it approximates the most probable shape of the points in the plane (Figure 1, Figure 2).

{% img center /images/Originalanddelaunay.png 640 275 'Random points Delaunay Voronoi' %}
{% img center /images/borderingalpha.png 640 275 'Random points Bordering alphashape' %}  

[^1]: Edelsbrunner, H., Kirkpatrick, D.G. and R. Seidel. 1983. On the shape of a set of points in the plane. *IEEE Transactions on information theory*, **29**: 551-559.