# Overview

This is a collection of functions I'm developing to do scientific plotting in the lovely web-based visualization package called [Bokeh](http://bokeh.pydata.org/en/latest/).

Bokeh has many great features interactive 2D plots, particularly for lines and glyphs. However, it doesn't do shading or filled contouring, though they promise these features will come eventually. One option is to create rastered images of your data, but this takes a lot more bandwidth and slows down plots on the user end. I've developed a workaround that creates filled contour plots and renders them as a set of polygons, based on matplotlib's contourf function.

Bokeh's image function with defaults (left) and contoured version with defaults (right):
<img src="https://github.com/asher-pembroke/bokeh-tools/blob/master/rastered.png" width="400">
<img src="https://github.com/asher-pembroke/bokeh-tools/blob/master/contoured.png" width="400">


## Requirements

*  [numpy](http://www.numpy.org/)
  
*  [scipy](http://www.scipy.org/)
  
*  [matplotlib](http://matplotlib.org/)
  
*  [webcolors](https://pypi.python.org/pypi/webcolors/)
  
*  [Bokeh](http://bokeh.pydata.org/en/latest/).
  
*  [networkx](http://networkx.github.io/) - Graph theory for dummies!


## Methodology

I used output from matplotlib's contourf function to get a set of path objects. These are converted into polygons with holes. Matplotlib uses a MoveTo command to carve out interior holes from exterior polygons, but bokeh's plotting routines don't have this functionality: all polygons have to be simple. However, we can convert a polygon with holes into a simple polygon by introducing cuts that join the exterior boundaries to the interior holes. I used the solution described here: http://cs.stackexchange.com/questions/43758/turning-a-simple-polygon-with-holes-into-exterior-bounded-only

The main goal is to find a single path which traverses the exterior and interior holes, touching all vertices (some twice, where bridges between the holes occur). First, we compute the edges of a delaunay graph of all the points, giving each point a label indicating which hole it belonged to and 0 for the exterior boundary. Then, we compute the minimum spanning tree (mst) using edge lengths for the weights, which generates a graph that touches all the points once. The mst looks a lot like the original polygons, plus a few bridges. For some holes, there may be more than one bridge connecting it to another hole, so to get a unique bridge we create a graph with the exterior + holes as the nodes and bridges as the edges. We then compute the minimum spanning tree of *that* graph to get the unique bridges that connect all the holes with each other and the exterior boundary. To get the final path, we put the original points in a directed graph, insert bridges as degenerate pairs of edges, then compute the shortest path from any starting vertex back to itself.
