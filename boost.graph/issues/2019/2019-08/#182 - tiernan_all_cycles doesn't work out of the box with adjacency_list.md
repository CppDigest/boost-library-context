# #182 - tiernan_all_cycles doesn't work out of the box with adjacency_list [Open]

> Username: wonkoRT  
> Created at: 2019-08-23 08:01:46 UTC  
> Updated at: 2019-12-30 07:59:18 UTC  
> Url: https://github.com/boostorg/graph/issues/182  

there might be a bug in   
  `boost/graph/tiernan_all_cycles.hpp`  
which comes from tiernan_all_cycles.hpp, line 161:  
`BOOST_CONCEPT_ASSERT(( VertexIndexGraphConcept<Graph> ));`  
and then in graph_concepts.hpp on line 469/70  
```  
// This is relaxed  
renumber_vertex_indices(g);  
```  
  
One way to make it compile is to just provide the renumber_vertex_indices the concept checks for, but since tiernan_all_cycles doesn't directly or indirectly call renumber_vertex_indices the check on line 161 in tiernan_all_cycles.hpp can also be commented out.  
I don't understand why this concept check is used. Maybe it's fine and the "workaround" is the right way to deal with it?  
  
[Here's a godbolt.](https://godbolt.org/z/rtdEZl)

---

## Comment 1

> Username: anadon  
> Created at: 2019-08-24 16:06:04 UTC  
> Url: https://github.com/boostorg/graph/issues/182#issuecomment-524562040  

I'll get to reading everything either today or tomorrow.

---

## Comment 2

> Username: anadon  
> Created at: 2019-08-24 16:11:18 UTC  
> Url: https://github.com/boostorg/graph/issues/182#issuecomment-524562447  

Actually, reading this, I kind of want to reimplement the whole thing.

---

## Comment 3

> Username: Naios  
> Created at: 2019-12-30 07:59:18 UTC  
> Url: https://github.com/boostorg/graph/issues/182#issuecomment-569609191  

I ran into the same issue, `renumber_vertex_indices` overall is only used internally in classes and probably should be removed from the concept as `// This is relaxed` indicates.
