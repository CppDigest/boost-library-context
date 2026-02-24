# #116 - reverse_graph.hpp breaks ADL get [Open]

> Username: sehe  
> Created at: 2018-09-09 12:30:29 UTC  
> Updated at: 2018-09-25 14:39:56 UTC  
> Url: https://github.com/boostorg/graph/issues/116  

Including `reverse_graph.hpp` defines a `get` overload inside the `boost::detail` namespace:  
  
```  
namespace detail {                                                                                                                                                              
  template <class E>                                                                                                                                                            
  struct underlying_edge_desc_map_type {                                                                                                                                        
    E operator[](const reverse_graph_edge_descriptor<E>& k) const {                                                                                                             
      return k.underlying_descx;                                                                                                                                                
    }                                                                                                                                                                           
  };                                                                                                                                                                            
                                                                                                                                                                                
  template <class E>                                                                                                                                                            
  E                                                                                                                                                                             
  get(underlying_edge_desc_map_type<E> m,                                                                                                                                       
      const reverse_graph_edge_descriptor<E>& k)                                                                                                                                
  {                                                                                                                                                                             
    return m[k];                                                                                                                                                                
  }                                                                                                                                                                             
}                                                                                                                                                                               
                                                   
```  
  
If included before other algorithms it can mess up the ADL on `get` calls from within that detail namespace. E.g. in `strong_components.hpp` the Tarjan visitor does   
  
          if (get(comp, w) == (std::numeric_limits<comp_type>::max)())  
  
This call fails to compile if `reverse_graph.hpp` had been included before. In fact, the developers probably found this out when they decided to only include the `transpose_graph.hpp` header /after/ the Tarjan implementation (and before the  Kosaraju version that requires it).  
  
The exact mechanics of the bug are not completely clear to me. But I guess it sits on the intersection of ADL and partial ordering. In fact using   
  
     using ::boost::get;                                                                                                                                               
     if (get(comp, w) == (std::numeric_limits<comp_type>::max)())  // ...  
  
does enable ADL at instantiation time. But I suspect the **structural** fix would be to avoid declaring a `get` overload inside the detail namespace.  
  
----  
  
found from https://stackoverflow.com/questions/52239778/bgl-call-to-strong-components-fails-to-compile-when-random-spanning-tree-hpp-is

---

## Comment 1

> Username: anadon  
> Created at: 2018-09-25 14:39:56 UTC  
> Url: https://github.com/boostorg/graph/issues/116#issuecomment-424370713  

I'll start looking into this.  I'm kind of loaded for the immediate future, and I'm new so this can't be a quick thing from me.
