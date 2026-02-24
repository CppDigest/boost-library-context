# #177 - Request: add degree_sequence_game in BGL [Open]

> Username: phcerdan  
> Created at: 2019-07-21 19:26:01 UTC  
> Updated at: 2019-07-23 16:35:03 UTC  
> Url: https://github.com/boostorg/graph/issues/177  

`igraph` has  this `degree_sequence_game` [src/games.c] method to randomly create a graph with a set of degrees.  
  
I wonder if you know if there is any implementation of that stochastic algorithm using BGL. And if not, if there is any road map on implementing it. Thanks!  
  
[src/games.c]: https://github.com/igraph/igraph/blob/master/src/games.c

---

## Comment 1

> Username: anadon  
> Created at: 2019-07-21 21:05:48 UTC  
> Url: https://github.com/boostorg/graph/issues/177#issuecomment-513587514  

Hello phcerdan,  
  
Not that I am aware of.  My focus right now is tidying BGL up for long term maintenance and support before attempting to create a BGL2.  We welcome support, but I don't have the man hours to sink into this feature request.  However, if you pique the interest of a more skilled boost dev, they might be able to handle such a request.  I'm still fairly green.

---

## Comment 2

> Username: phcerdan  
> Created at: 2019-07-23 06:39:20 UTC  
> Updated at: 2019-07-23 16:35:03 UTC  
> Url: https://github.com/boostorg/graph/issues/177#issuecomment-514075087  

As a reference, one of the generator I am interested in igraph is a verbatim copy of https://www-complexnetworks.lip6.fr/~latapy/FV/generation.html. But the license is also GPL.  
  
Attached the GPL code:  
[gengraph.tar.gz](https://github.com/boostorg/graph/files/3420540/gengraph.tar.gz)  
  
And also [graph_tools](https://git.skewed.de/count0/graph-tool) has a generator of random graph with fixed edges. graph_tools seems based on BGL, but still with GPL license :(  
https://graph-tool.skewed.de/static/doc/generation.html#graph_tool.generation.random_graph
