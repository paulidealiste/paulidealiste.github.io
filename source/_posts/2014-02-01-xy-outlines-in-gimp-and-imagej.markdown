---
layout: post
title: "XY outlines in GIMP and imageJ"
date: 2014-02-01 11:23:55 +0100
comments: true
categories: ["morphometrics", "utilities"]
keywords: "outline, GIMP, imageJ"
description: "This post shows python procedure for generating outlines in XY format"
---

This post continues on the first post about outline deformations and presents a simple way for generating outlines as XY data. XY format is very convenient for representing outline and line art in R or python. This type of visualization can be produced using <a href="http://www.gimp.org" target="_blank">GIMP</a> and <a href="http://rsbweb.nih.gov/ij" target="_blank">imageJ</a> through a sequence of easy steps. GIMP enables easy extraction of the central object from the image background. First step in outline extraction should be the precise definition of the object boundaries, such that the contrast between the object and the background is maximal. Example picture for this post is available <a href="http://goo.gl/bkirX7" target="_blank">here</a>. After opening the picture in GIMP workspace it looks like in Figure 1.

{% img /images/Gimp1.png 375 289 'Gimp import' %} {% img right /images/Gimp2.png 375 290 'Gimp scissors' %}

The easiest way to separate this chamois cranium from its background is to use `Scissor Select Tool` from the GIMP toolbox. This tool can nicely track the path between sucessive control points that should be placed along the desired object (Figure 2). When control points are placed along the shape, selection can be completed by pressing enter, and the background can be deleted by inverting selection pressing `Ctrl+I`. and deleting it using the delete key (Figure 3). Selection should be inverted once more and converted to path; from the `Select` menu `To Path`. Finally, stroke path (Figure 4, path card and right click on selection, `Stroke path`) gives the desired outline that can be exported as .tiff for imageJ through `Export` in the `File` menu. 

{% img /images/Gimp3.png 375 290 'Gimp no background' %} {% img right /images/Gimp4.png 375 289 'Gimp stroke path' %}

In imageJ, picture should be converted to binary in `Process` menu, by selecting `Binary` and `Make Binary`. Final step is saving the binary image selection as XY data. First the outline should be selected by the magic wand selection tool (Figure 5) and then the image should be saved in XY format, in `File` menu, `Save as` and select `XY coordinates`. The XY data is also available <a href="http://goo.gl/d44PxK" target="_blank">here</a>. 

{% img /images/GimpNo.png 310 326 'imageJ' %}

Now the .txt file could be easily imported into R and python.