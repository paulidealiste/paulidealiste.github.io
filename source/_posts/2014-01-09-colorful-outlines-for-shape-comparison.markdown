---
layout: post
title: "Colorful outlines for shape comparison"
date: 2014-01-09 19:55:01 +0100
comments: false
categories: ["R", "morphometrics"]
keywords: "morphometrics, outlines, color, shape, R, geomorph"
description: "This post shows R procedure to generate coloured outline deformations in order to compare cranial shapes between groups"
---

This procedure is based on the outlines generated from the digital photos using imageJ and converting imageJ images to x-y continuous outline data available from <a href="http://goo.gl/TYSzf0" target="_blank">here</a>. Its goal is to present a visual overview of global shape differences between individuals from natural populations, using landmark data from ventral projection of their crania. All outlines are based on deformation via Thin Plate Splines, using mean shapes for populations as deformation targets and references. Superimposition methods as well as preliminary GM analyses were done in R and marvelous *geomorph* package by Dean Adams and Erik Otarola-Castillo. Additionally, since one of the common points of contemporary scientific research is the reproducibility of solutions offered all posts will also contain randomly generated sample data that could be used similarly to real-world datasets. Sample generation can be very useful, especially in teaching, so I intend to focus on it in future posts. 

```r Importing libraries and generating the basic dataset (files should be placed in your working directory)
library(geomorph)
library(ggplot2)
library(Morpho)
d <- read.table("ventralnoOutlineCap.txt") #outline data
load("capreolusRgen.RData")
capreolusArray <- capreolusSample1 #or capreolusSample2-5
```
The workspace "capreolusRgen.RData" (which can be downloaded from <a href="http://goo.gl/4uKerX" target="_blank">here</a>) contains several randomly generated datasetes of 657 individuals and 28 landmarks, named "capreolusSample#". These data was generated on the basis of real-world values, using the linear regression model to control random number generators. The code that was used probably does not repoduce the sampling of landmarks well, especially regarding correlations between pairs of landmark coordinates or the landmarks that conform to the object symmetry, but for the purpose of illustration in this post, I hope they should be fine. 

```r Procrustes superimposition
capreolusGPA <- gpagen(capreolusArray, ShowPlot = FALSE)
pop <- sample(3, 657, replace = TRUE) #generate random 3 population partition
pop[which(pop == 1)] <- "pop1"
pop[which(pop == 2)] <- "pop2"
pop[which(pop == 3)] <- "pop3"
capreolus <- capreolusGPA$coords #extract landmark coordinates
```

Following the Procrustes superimposition is the calculation of mean shapes, both for all males and for separate populations. After mean shapes are calculated the only thing left is to use TPS in order to deform outlines (variable d), using mean shape of all males as a reference and mean shape of populations as target. This can all be done using *Morpho* R-package from Stefan Schlager.

```r Mean shapes and TPS deformations
meanCap <- mshape(capreolus)
meanPop1 <- mshape(capreolus[,,which(pop == "pop1")]) #by population
meanPop2 <- mshape(capreolus[,,which(pop == "pop2")])
meanPop3 <- mshape(capreolus[,,which(pop == "pop3")])
pop1 <- data.frame(tps3d(as.matrix(d), meanCap, meanPop1)) #for each population
pop2 <- data.frame(tps3d(as.matrix(d), meanCap, meanPop2))
pop3 <- data.frame(tps3d(as.matrix(d), meanCap, meanPop3))
capWhole <- rbind(pop1, pop2, pop3) #combine data
pops <- c(rep("pop1", 2836), rep("pop2", 2836), rep("pop3", 2836)) #outline has 2836 points
```

Finally, depicting shape changes can be achieved by wonderful Hadley Wickham`s *ggplot2* R-package. This package has a neat way of "forcing" you to keep your data organized, so all variables are inside one data frame, both quantitative and qualitative. 

```r ggplot2 plotting of shape outline deformations
wholeCap <- data.frame(capWhole, pops) #deformed outlines and population membership
theme_set(theme_bw()) #change default ggplot theme to b&w
dplot <- ggplot(wholeCap, aes(wholeCap[,1],wholeCap[,2], group = pops)) #initialize ggplot object
dplot <- dplot + geom_path(size = 1, aes(color = pops)) + facet_grid(.~pops) #add layers
dplot + theme(axis.title = element_blank(), axis.text = element_blank(), axis.ticks = element_blank())
```

{% img center /images/post1outline.png 616 546 'Outlines' %}

By inspecting outlines it can be seen that the individuals from pop1 are the smallest while the ones from pop2 are the largest. Shape differences are also determined by the relationship of length to width, so that individuals from pop2 have the widest crania, while the ones from pop1 have the narrowest. Also, it can be seen that in the individuals with the largest crania, size differences are detemined mostly by dimensions of the anterior part, maxillary and rostral regions, that are both wider and longer with respect to individuals with smaller crania. Posterior part of the cranium is more similar between individuals from different populations, and it may be more stable.
