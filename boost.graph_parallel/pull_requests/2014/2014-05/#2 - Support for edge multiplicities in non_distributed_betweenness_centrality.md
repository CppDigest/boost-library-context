# #2 Support for edge multiplicities in non_distributed_betweenness_centrality [Open]

> Username: javidcf  
> Created at: 2014-05-23 10:16:43 UTC  
> Updated at: 2025-09-24 10:01:42 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/2  

Added the possibility to include edge multiplicities in `non_distributed_betweenness_centrality`. Also, this fixes the compilation issues caused by [PR #6 in boostorg/graph](https://github.com/boostorg/graph/pull/6).  
  
**Note:** The named parameters retrieval has been changed a little bit, not only to get `edge_multiplicity` but also to make it similar to the sequential `brandes_betweenness_centrality` (the `typedef` declarations). This has been done so both `weight_map` and `multiplicity_map` can be used optionally, which was not the case before (i.e. there would be a compilation error if you didn't provide `weight_map`, as explained in [this message in the Boost mailing list](http://boost.2283326.n4.nabble.com/graph-graph-parallel-Named-parameters-in-non-distributed-brandes-betweenness-centrality-td4662720.html)). Please point it out if there was any reason for it and should not be changed. On the contrary, if this change is right it should also be done in the parallel version of `brandes_betweenness_centrality`.

---

## Comment 1

> Username: javidcf  
> Created_at: 2014-05-23 10:23:42 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/2#issuecomment-43992756  

Of course, the next (and I guess final) step for this feature is to get it implemented in the fully distributed version of `brandes_betweenness_centrality`. However, it's not nearly as straightforward as it was with `non_distributed_betweenness_centrality`.

---
