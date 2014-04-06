---
layout: post
title: "Delaunay triangulation experiment"
date: 2014-04-04 18:09:37 +0200
comments: true
categories: ["R", "morphometrics"]
keywords: "R, Delaunay, modularity"
description: "This post is about one possible way of using some featrues of Delaunay triangulation for modularity"
---

Since landmark data consists of x and y Cartesian coordinates, it would be useful if they could be represented by one summary number, that would preserve the spatial information. This is just a proposal of one possiblity using Delaunay triangulation between landmark configurations, and the average area of all triangles emanating from individual landmarks. Since landmark position within the triangulation grid would influence the shape and size as well as number of triangles around it, the area should preserve enoguh information about the spatial relationships of landmarks. Dataset for this post consists of ventral aspect half configurations from <a href="http://goo.gl/LP3xsd" target="_blank">here</a>. Delaunay triangulation will be performed using *deldir* R package, since it reports the mentioned triangle areas as a part of object summary. 

```r Importing data, basic GM, calculating the triangulation, extracting areas and plotting
library(deldir)
library(geomorph)
load("capSample.RData")

capreolusGPA <- gpagen(capSample)
capGPAcoords <- capreolusGPA$coords

delCap <- deldir(capSample[,,10][,1], capSample[,,10][,2]) #extract any individual from an array for visualization
plot(delCap, col=c("lightblue","lightgrey",1,1,1), lwd = c(2,2), xlim = c(150, 400), cex = 0.1, ann = FALSE)
points(capSample[,,10], col = "orange", pch = 16, cex = 1.4)
text(capSample[,,10], label = c(1:16), pos = 3, ceo = 0.5)

areaCap <- t(apply(capGPAcoords, 3, function(A) deldir(A[,1],A[,2])$summary[,4])) #calcuate areas per landmark
areaCap <- data.frame(areaCap)
names(areaCap) <- paste("lm", c(1:16), sep = "")
```

{% img center /images/Delaunay.png 401 473 'Delaunay' %}

Values in the areaCap matrix represent average areas of all triangles emanating from the landmark in question, so its dimensions are 60x16. Correlation matrix (16x16) can be calculated from the areaCap matrix, and it should reflect relative positioning of landmarks through spatial grouping pattern. This can be checked visually using *corrplot* package visualization of the correlation matrix, which allows direct assesment of emerging patterns in the matrix. Additionally, this package allows reordering of rows and columns according to hierarchical clustering algorhithms and representing the desired number of clusters with rectangles in the correlation plot. This can only be considered as an idea of general modularity, since landmarks from the same cranial region should be correlated more than distant landmarks. But the transformation of xy coordinates to average triangle areas may have introduced non-biological variation or obscured some of the natural variation and these procedures as well as subsequent analyses may be treated only as a fun experiment and visualization tool for now. 

```r Drawing correlation plot
library(corrplot)
capCor <- cor(areaCap)
corrplot(capCor, method = "circle", tl.cex = 0.93, order = "hclust", addrect = 3)
```

{% img center /images/Corrplot.png 570 529 'Delaunay' %}

Correlation plot with three proposed general landmark groups in the matrix reveals that the correlations are, on average, higher for locally grouped landmaks, especially the anterior ones, from 1 to 7. This can`t be used as a reliablie test for modularity hypothesis, but it can serve as a basis for further analyses based on construcing linked graphs or networks using correlation matrices from landmark data, where the xy coordinates are transformed to one number. Also this can be a useful way of representing modular structure visually, since both Delaunay and correlation plots are highly customizable, and can be colored according to real modularity hypotheses. Testing some of these will be the subject of future posts.

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