# #188 - Typo in name adjacenct_vertices() [Closed]

> Username: AlekseyMuraviev  
> Created at: 2019-10-03 09:24:11 UTC  
> Updated at: 2024-04-17 00:41:02 UTC  
> Closed at: 2024-04-17 00:41:02 UTC  
> Url: https://github.com/boostorg/graph/issues/188  

I think it should be adjacent_vertices(), not adjacen**c**t_vertices().  
  
In file https://github.com/boostorg/graph/blob/develop/include/boost/graph/labeled_graph.hpp.  
  
```  
/** @name Adjacency Graph */  
//@{  
template < LABELED_GRAPH_PARAMS >  
inline std::pair< typename LABELED_GRAPH::adjacency_iterator,  
    typename LABELED_GRAPH::adjacency_iterator >  
adjacenct_vertices(  
    typename LABELED_GRAPH::vertex_descriptor v, LABELED_GRAPH const& g)  
{  
    return adjacent_vertices(v, g.graph());  
}  
//@}  
```

---

## Comment 1

> Username: przemb  
> Created at: 2019-10-04 17:06:22 UTC  
> Url: https://github.com/boostorg/graph/issues/188#issuecomment-538481689  

I will change it :)
