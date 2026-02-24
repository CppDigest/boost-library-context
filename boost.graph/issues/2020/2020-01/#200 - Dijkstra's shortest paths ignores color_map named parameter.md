# #200 - Dijkstra's shortest paths ignores color_map named parameter [Open]

> Username: SamueleDecarli  
> Created at: 2020-01-21 17:46:15 UTC  
> Updated at: 2022-12-10 06:20:07 UTC  
> Url: https://github.com/boostorg/graph/issues/200  

Dijsktra's shortest paths takes as parameter UTIL/OUT a color_map. Which should be passed to and used by breadth first search.  
However, the current implementation ignores the color_map, if provided, and always creates a default one.  
  
https://www.boost.org/doc/libs/1_72_0/libs/graph/doc/dijkstra_shortest_paths.html  
https://github.com/boostorg/graph/blob/develop/include/boost/graph/dijkstra_shortest_paths.hpp

---

## Comment 1

> Username: hdu-sdlzx  
> Created at: 2022-12-10 06:20:06 UTC  
> Url: https://github.com/boostorg/graph/issues/200#issuecomment-1345153307  

It seems that UTIL/OUT means "used by the algorithm and you can retrieve its result".  
An input (IN) argument means initialized values.
