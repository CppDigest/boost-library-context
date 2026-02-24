# #454 - [BUG] edge_connectivity returns wrong result on directed graphs [Open]

> Username: Becheler  
> Created at: 2026-02-04 21:34:27 UTC  
> Updated at: 2026-02-10 14:28:07 UTC  
> Url: https://github.com/boostorg/graph/issues/454  

## User bug description   
  
It has been brought to my attention by David Coudert (maintainer of Sage Math graph that uses Boost Graph) that one ticket got lost long ago when migrating from SVN: https://github.com/sagemath/sage/issues/18753  
  
Basically the edge_connectivity experience is broken for their users:  
  
```  
sage: g = digraphs.Path(3)  
sage: g.edge_connectivity(implementation="sage")  
0.0  
sage: g.edge_connectivity(implementation="boost")  # wrong answer  
1  
sage: g.add_edge(1, 0)  
sage: g.edge_connectivity(implementation="sage")  
0.0  
sage: g.edge_connectivity(implementation="boost")  
0  
```  
  
## Implementation   
  
Looking quickly into the implementation, the implementation does not filter out directed graph, treating all as undirected, and unconditionally add reverse edges when consturcting the flow graph. There is reasonable ground to believe it was not clear for the author if the reverse edge should have capacity 0 or 1:  
  
```cpp  
    for (boost::tie(ei, ei_end) = edges(g); ei != ei_end; ++ei)  
    {  
        u = source(*ei, g), v = target(*ei, g);  
        boost::tie(e1, inserted) = add_edge(u, v, flow_g);  
        cap[e1] = 1;  
        boost::tie(e2, inserted) = add_edge(v, u, flow_g);  
        cap[e2] = 1; // not sure about this  
        rev_edge[e1] = e2;  
        rev_edge[e2] = e1;  
    }  
```  
  
The bug seems to be present since early day ~2001 by Jeremy Siek. The file comes with a warning `// WARNING: not-yet fully tested!  
`,  and unless I'm mistaken there is indeed no test for this algorithm.   
  
## Proposed changes  
  
1. Add a concept restriction to forbid directed graphs  
2. Add extensive tests to lock-in functional behavior  
3. Think about supporting directed graphs: I am unsure how to do this, and this could require an algorithmic modification.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2026-02-08 21:32:24 UTC  
> Url: https://github.com/boostorg/graph/issues/454#issuecomment-3868340009  

Interesting. Could you please add some hyperlinks to the relevant sections of the library? Thanks.

---

## Comment 2

> Username: Becheler  
> Created at: 2026-02-09 12:55:21 UTC  
> Url: https://github.com/boostorg/graph/issues/454#issuecomment-3871547034  

It is here: https://github.com/boostorg/graph/blob/3131c24630e42c79b43c1f32558041c219ab84b8/include/boost/graph/edge_connectivity.hpp#L105-L125

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2026-02-10 09:40:51 UTC  
> Url: https://github.com/boostorg/graph/issues/454#issuecomment-3876485162  

Am I right that this algorithm is undocumented?

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2026-02-10 09:46:32 UTC  
> Url: https://github.com/boostorg/graph/issues/454#issuecomment-3876519191  

If Sage have their own implementation of edge connectivity for directed graphs, can we not use it to figure out what ours should do?   
Or at least get a reference to the literature that defines this algorithm?

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2026-02-10 09:49:05 UTC  
> Url: https://github.com/boostorg/graph/issues/454#issuecomment-3876534025  

Harold N. Gabow. A Matroid Approach to Finding Edge Connectivity and Packing Arborescences. Journal of Computer and System Sciences, 50(2):259-273, 1995. doi:10.1006/jcss.1995.1022

---

## Comment 6

> Username: Becheler  
> Created at: 2026-02-10 14:18:34 UTC  
> Updated at: 2026-02-10 14:28:07 UTC  
> Url: https://github.com/boostorg/graph/issues/454#issuecomment-3877926079  

In SageMath implemenetations:  
- the Gabow algorithm from the paper you mentioned is implemented [here](https://github.com/sagemath/sage/blob/5473e598ac601e989b33945622529a827ff9ed0d/src/sage/graphs/edge_connectivity.pyx )(apparently added by David Coudert as a fix to the Boost current bug)   
- The Gabow actual paper is [here](https://dl.acm.org/doi/pdf/10.1145/103418.103436)  
- And SageMath comes with an Integer Linear Programming version that solves the same problem [here]( https://github.com/sagemath/sage/blob/5473e598ac601e989b33945622529a827ff9ed0d/src/sage/graphs/connectivity.pyx#L1721-L2053)
