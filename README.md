# Overview

This is a collection of functions I'm developing to do scientific plotting in the lovely web-based visualization package called [Bokeh](http://bokeh.pydata.org/en/latest/).

Bokeh has many great features interactive 2D plots, particularly for lines and glyphs. However, it doesn't do shading or filled contouring, though they promise these features will come eventually. One option is to create rastered images of your data, but this takes a lot more bandwidth and slows down plots on the user end. I've developed a workaround that creates filled contour plots and renders them as a set of polygons, based on matplotlib's contourf function.

## Requirements

*  numpy
  
*  scipy
  
*  matplotlib
  
*  webcolors
  
*  bokeh
  
*  networkx


## Methodology
