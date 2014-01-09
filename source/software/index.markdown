---
layout: page
title: "Adventurous software"
date: 2014-01-08 09:40
comments: false
sharing: false
footer: false
keywords: "software, R, python, numpy, scipy, matplotlib, geomorph, ggplot2, shapes"
description: "This page shows the software packages available in R or python needed for shape analysis"
---

{% img center /images/logos.png 616 211 'Logos' %} 

Computational power and availability provided by both [R](http://www.r-project.org/) and [python](http://www.python.org/) are tremenduous. Shape analysis requires acessible and versatile software for performing statistical procedures and matrix algebra, so both of the languages are a good choice, since the main data structure in both of them is the matrix or a multidimensional matrix. Both R and python have a great collection of libraries that enable all of the required analyses. Also, online (and offline) community of users is enormous so any problem can be solved easily, in the cloud. 

In R, there are several libraries that implement procedures for the biological analysis of shape, mainly landmark data, but also outline data. There are four packages for performing geometric morphometrics in R and they are maintained actively by their authors, who are also willing to help with their usage.

1. [geomorph](http://cran.r-project.org/web/packages/geomorph/)
2. [shapes](http://cran.r-project.org/web/packages/shapes/index.html)
3. [Morpho](http://cran.r-project.org/web/packages/Morpho/index.html)
4. [Momocs](http://cran.r-project.org/web/packages/Momocs/index.html) 

Apart from the packages there is a wonderful book about doing morphometric analyses in R, conveniently named [Morphometrics in R](http://www.springer.com/statistics/life+sciences,+medicine+%26+health/book/978-0-387-77789-4), from Julien Claude, written in 2008. This book is one of the best starting points for R in general, morphometric theory and practical usage of all GM analyses.

Python is a general-purpose programming language that has unbelievably large user base but there are no specific packages for geometric morphometrics, yet. On the other hand, python offers some of the most powerfull linear algebra libraries [numpy](http://www.numpy.org/) and [scipy](http://scipy.org/). Also, there are other libraries commonly used in scientific data analysis such as [pandas](http://pandas.pydata.org/) for flexible data manipulation and uniterrupted workflow and [matplotlib](http://matplotlib.org/) that offers great visualizations. The best thing about scientific python usage is the availability of the language distributions budled with all the libraries needed for scientific data analysis. This is commonly known as *scipy stack* that is easily installed on all major operating systems (Linux, Windows and OSX). The overview of scientific python distributions is given [here](http://scipy.org/install.html).

Finally, both R and python rely on command line interface for all analytic steps. R comes with the basic Tcl/Tk command line interface that offers basic functionality for controlling the workflow. Python has IDLE, command line interface that is very similar to basic R. Fortunately, both languages have superb IDEs (Integrated Development Environments) that enable flexible and versatile coding practice. For R there is [R studio](http://www.rstudio.com/) and for Python there is [PyCharm community edition](http://www.jetbrains.com/pycharm/download/). Scientific python distributions also pack with the fenomenal [IPython](http://ipython.org/) enhanced console that offers much flexibility in entering commands and esspecially for generating nicely formatted HTML reports, neatly organized. R Studio also has great report generation sytstem through the [knitr](http://yihui.name/knitr/) package and its own markdown system.