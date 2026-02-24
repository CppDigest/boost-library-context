# #396 Make filtered_graph conditionally model the underlying graph [Open]

> Username: tsnorri  
> Created at: 2024-11-08 10:54:41 UTC  
> Updated at: 2025-03-03 12:52:38 UTC  
> Url: https://github.com/boostorg/graph/pull/396  

According to `filtered_graph`'s documentation, the refinements of the model of the underlying graph are optional.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-02-26 22:23:40 UTC  
> Url: https://github.com/boostorg/graph/pull/396#issuecomment-2686343159  

Thanks for this, but I'm afraid I don't really understand what it is doing or why it is a good thing. :)  
Could you quote relevant paragraphs from the documentation and maybe even provide an example of how it changes behaviour? Thanks.

---

## Comment 2

> Username: tsnorri  
> Created_at: 2025-02-27 13:24:25 UTC  
> Updated_at: 2025-03-03 12:52:38 UTC  
> Url: https://github.com/boostorg/graph/pull/396#issuecomment-2687956471  

Right, I was quite terse, sorry about that. The issue is with adapting an existing graph type `G` for use with BGL and especially `filtered_graph`. If `G` does not model all the graph concepts `filtered_graph` supports, `filtered_graph` cannot be specialised for `G` since its `graph_traits` may lack some of the types required by the graph concepts. For instance, if `G` does not model `BidirectionalGraph`, the `graph_traits` specialisation for `G` may lack `in_edge_iterator` but `filtered_graph` requires it anyway. I attempted to fix this by defining the types conditionally in `filtered_graph`.  
  
I think that this is something that should be fixed in `filtered_graph` since [its documentation](https://www.boost.org/doc/libs/1_87_0/libs/graph/doc/filtered_graph.html) (§ Model of) states that “if the underlying `Graph` type models fewer or smaller concepts than these, then so does the filtered graph.” I think the unspecialised version of `graph_traits` [handles the issue](https://github.com/boostorg/graph/blob/8f05ca23fa90be1929ef52d10e74b17dd37bf255/include/boost/graph/graph_traits.hpp#L59-L68) by defining the missing types as `void`, but the [LEDA graph example](https://www.boost.org/doc/libs/1_87_0/libs/graph/doc/leda_conversion.html) in the documentation suggests that specialising `graph_traits` is the preferred approach.  
  
Defining the types required by each of the graph concepts in BGL in the `graph_traits` specialisation as void or otherwise would also fix the issue but I do not think it is preferable. (On the other hand, defining the required types in `G` would make it possible to use the unspecialised `graph_traits` but may not be possible.) By looking at the [example code for using the LEDA graph](https://www.boost.org/doc/libs/1_87_0/boost/graph/leda_graph.hpp), I do not think the problem occurs there, since the existing graph type seems to model at least all of the concepts `filtered_graph` supports and hence the associated `graph_traits` specialisation seems to define all the types used by `filtered_graph`.

---
