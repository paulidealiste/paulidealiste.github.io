---
layout: post
title: "PLS model for temperature in R"
date: 2014-09-02 13:01:59 +0200
comments: true
categories: ["R", "utilities"]
keywords: "raster, climate data, GIS, PLS"
description: "This post shows the way to analyses the common covariance patterns for morphology and climate data"
---

Continuing on the post about the climate data extraction in R, this post demonstrates a simple approach to using PLS (partial least squares) for determining the common covariance patterns between morphology and mean monthly temperature (although the model is likely to be bad). Morphology data can be found  <a href = "http://goo.gl/7BQ4tT"> here</a>. This dataset only contains PCA scores of individuals for the first two PC axes (Figure 1), extracted from the Fourier descriptors of horn shape. PC1 axis (92%) clearly separates males and females on the basis of respective horn shape. 

{% img center /images/pca1plot.png 450 393 'ordinary PCA' %}  

Temperature data are extracted using geoTiffs from  <a href="http://www.worldclim.org/" target="_blank">WorldClim</a> database, as in the mentioned post about climate data extraction. The ready-made dataset of mean monthly temperatures for the above study locality can be found <a href = "http://goo.gl/2fAlAF"> here</a>. 

```r Importing data and plotting the Figure 1 PCA
fourierPC <- read.table("~/fourierPC.txt", header = TRUE, sep = ",") #by default they are in the home directory
matTaraMean <- read.table("~/matTaraMean.txt", header = TRUE, sep = ",")

library(ggplot2)
theme_set(theme_bw())
sex <- c("f","f","m","m","f", "m","m","m","f","m","f","f","m","f","f","m","f","f","f","m","f","m","f","f","m","f") #individual sex is known in advance
ggplot(fourierPC, aes(PC1, PC2)) + geom_point(size = 5, shape = 19, aes(color = sex))
```
PLS analysis is a useful multivariate technique used for determining the common variation patterns in two blocks of data and is sometimes reffered to as PLS regression. In this post, of all PLS implementations in R, the choice is on the fabulous *plsdepot* library, developed and maintained by Gaston Sanchez, whose <a href="http://gastonsanchez.com" target="_blank"> blog/personal page</a> and work in general was a great insipiration for Creative Morphometrics. His approach is very well explained and documented over at his page, so only direct implementation on the data above will be provided here.

```r PLS plsdepot library
library(plsdepot)
taraMat <- matTaraMean[sample(221, 26),] #extract 26 samples from 221 raster grid values
climatePLS <- plsreg1(taraMat, fourierPC$PC1, comps = 3)
plot(climatePLS)#plot circle of correlations in order to determine the influence of predictor variables
```

{% img center /images/RplotCircles.png 450 393 'PLS correlation circle' %}  

It is obvious from Figure 2. that the variables in question share no common variation pattern and are totally unrelated. If the chosen data was better, some of the blue lines (predictors) would run parallel with the orange one (response). If the R^2 value for this model is examined (it is a part of climatePLS object - climatePLS$R2) the unrelatedness of predictors and response in this model gets even more obvious (R^2 = 0.035). Predictors in this model are also highly correlated within themselves, which renders them rather useles in prediction, as was stated at the onset. 