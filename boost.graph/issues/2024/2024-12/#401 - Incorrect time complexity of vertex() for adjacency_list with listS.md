# #401 - Incorrect time complexity of vertex() for adjacency_list with listS [Closed]

> Username: jorisvr  
> Created at: 2024-12-08 10:58:58 UTC  
> Updated at: 2024-12-09 21:32:59 UTC  
> Closed at: 2024-12-09 21:32:59 UTC  
> Url: https://github.com/boostorg/graph/issues/401  

The manual page on [using adjacency_list](https://www.boost.org/doc/libs/1_86_0/libs/graph/doc/using_adjacency_list.html) appears to claim that the function `vertex()` runs in constant time on instances of `adjacency_list<listS, listS>`.  
  
> - `vertex()`  
> This operation is constant time for vecS and for listS.  
  
However, it seems that the run time of `vertex(n, g)` is actually proportional to the value of *n* when `VertexList=listS`.  
  
I asked [this question on stackoverflow](https://stackoverflow.com/questions/79261293/time-complexity-of-boost-graph-function-vertex-on-adjacency-list) and we reached the conclusion that the documentation is probably wrong. The correct run time is *O(V)*.  
  
I will attach a pull request to fix the documentation.  
While editing the HTML file, I noticed a suspicious redundant space character exactly at the place of the mistake. It might be the case that the documentation was once correct, but the formula `O(V)` dropped out of the text at some point.
