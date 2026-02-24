# #178 - Prim's minimum spanning tree algorithm can't use Dijkstra's shortest paths algorithm [Closed]

> Username: benny1693  
> Created at: 2019-07-29 15:51:45 UTC  
> Updated at: 2019-08-07 09:44:55 UTC  
> Closed at: 2019-08-07 09:44:54 UTC  
> Url: https://github.com/boostorg/graph/issues/178  

Prim's algorithm can NOT use Dijkstra' shortest paths algorithm to return the map of the predecessor, because Dijkstra's shortest path allows to create a shortest paths tree, that is a spanning tree (if all the nodes are connected) but not always a minimum spanning tree.   
I suggest to do a new implementation of the algorithm.  
In general, the two algorithms are different: Dijkstra minimizes the weights of the paths from the root to the other nodes, Prim minimizes the sum of the weights of the tree.  
Here follow a simple example:  
Consider a complete graph with 3 vertices: _a_, _b_, _c_  
Now,   
{_a_, _b_} = 2   
{_a_, _c_} = 1   
{_b_, _c_} = 1   
A correct spanning tree, using Dijkstra, is:  
- _a_  
  - _b_  
  - _c_  
  
The sum of the weights is {_a_, _b_} + {_a_, _c_} =  2 + 1 = 3.  
However, Prim's algorthim (starting with _a_) would find this spanning tree, instead:  
- _a_  
  - _c_  
    - _b_  
  
The sum of the weights this time is {_a_, _c_} + {_b_, _c_} =  1 + 1 = 2  
The problem can be found in include/boost/graph/prim_minimum_spanning_tree.hpp.  
  
Edit: there was a typo in the sum of the weights for Prim (wrong edge)

---

## Comment 1

> Username: anadon  
> Created at: 2019-08-07 03:54:24 UTC  
> Url: https://github.com/boostorg/graph/issues/178#issuecomment-518929242  

It looks like you are intending to use some variant in A-star, which is available.  I'm not convinced that this is a bug, but a user friendliness thing.

---

## Comment 2

> Username: benny1693  
> Created at: 2019-08-07 09:44:54 UTC  
> Url: https://github.com/boostorg/graph/issues/178#issuecomment-519024774  

Ok, I found out that I was wrong. I didn't realize that Prim's and Dijjkstra's algorithm have the same structure. I doubted about the implementation after a comparison between metric_tsp_approx (which calls prim_minimum_spanning_tree) and a implementation of the Nearest Neighbour algorithm (that I'm sure is O(E)). In fact, the first one is faster than the second one, even if its complexity is O(E log V).
