# #268 - Segfault for `remove_vertex` with `adjacency_list<boost::setS, ...>` [Closed]

> Username: lmondada  
> Created at: 2021-07-01 13:27:00 UTC  
> Updated at: 2022-02-21 00:45:36 UTC  
> Closed at: 2022-02-21 00:43:28 UTC  
> Url: https://github.com/boostorg/graph/issues/268  

## Bug description  
Removing a vertex in an `adjacency_list` graph produces an invalid read when the following conditions are met  
- the edge storage `OutEdgeList` is `setS` or `hash_setS`  
- the vertex storage `VertexList` is `vecS`  
- a source or target of an edge in the graph has index larger than the vertex index being deleted  
  
## Reproducing MWE  
The following code:  
```  
#include <boost/graph/adjacency_list.hpp>  
  
using Graph = boost::adjacency_list<boost::setS, boost::vecS, boost::undirectedS>;  
  
int main() {  
  while (true) {  
    Graph g(3);  
    boost::add_edge(2, 0, g);  
    boost::remove_vertex(1, g);  
  }  
}  
```  
  
On MacOS Big Sur, this results in a segfault in roughly 1/1000 iterations. This could be reproduced both with `AppleClang 12.0.5.12050022` and `LLVM Clang 12.0.0`. On Linux with gcc, no segfault was observed but the memory corruption can be observed in Valgrind (see [output](https://github.com/boostorg/graph/files/6748470/valgrind.txt)).  
  
## Proposed fix  
Reverting commit [5dd748307cc35e8](https://github.com/boostorg/graph/commit/5dd748307cc35e8ee1e7045902ba08adedbe8646#diff-406d7d1b8a0593dc27a38ce319[%E2%80%A6]85f11fb22a91f2d0326d19e8b27746d9) seems to fix the issue. The issue seems to come from incrementing an invalid iterator after a change to the original container.

---

## Comment 1

> Username: avdg81  
> Created at: 2021-11-23 12:38:14 UTC  
> Url: https://github.com/boostorg/graph/issues/268#issuecomment-976470129  

Hello all, I am facing the exact same issue as the one reported here. Just curious to know what the current status is? It seems that the underlying problem is well understood, and a possible solution has been suggested. Is it expected that it will be solved any time soon, or are there other problems (that I am not aware of) that block its resolution? Any feedback is greatly appreciated. Thanks.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2021-12-21 01:37:26 UTC  
> Url: https://github.com/boostorg/graph/issues/268#issuecomment-998398439  

This will be a priority to fix once the CI is fixed.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2022-02-21 00:45:36 UTC  
> Url: https://github.com/boostorg/graph/issues/268#issuecomment-1046365681  

The fix is now on `develop`; please test and re-open this bug report if the problem remains. Thanks everyone!
