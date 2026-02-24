# #415 Ensure named_graph base class is initialized before accessing it [Merged]

> Username: AnthonyVH  
> Created at: 2025-02-03 15:04:21 UTC  
> Updated at: 2025-02-04 10:11:18 UTC  
> Merged at: 2025-02-04 10:10:47 UTC  
> Closed at: 2025-02-04 10:10:47 UTC  
> Url: https://github.com/boostorg/graph/pull/415  

This PR fixes the issue that a `boost::adjacency_list` does not properly initialize it's `graph::maybe_named_graph` base before calling functions on it.  
  
More specifically, the following crashes:  
```c++  
using namespace boost;  
  
// Assume something::Vertex has proper specializations for `boost::internal_vertex_name`  
// and `boost::internal_vertex_name`.  
using graph_t = adjacency_list<hash_setS, vecS, bidirectionalS, something::Vertex>;  
  
graph_t graphA;  
// Initialize `graphA` here.  
  
graph_t graphB{graphA}; // Boom!  
```  
  
The reason this happens is because:  
- `boost::adjacency_list` inherits from `detail::adj_list_gen<...>` and `graph::maybe_named_graph<...>`  
- `boost::adjacency_list` typedefs `detail::adj_list_gen<...>` as `Base`  
- The copy constructor calls `Base(x)` as its very first "action", where `x` is `graphA`  
- This in turn ends up calling `vec_adj_list_impl<...>(graphA)`, which calls `copy_impl(graphA)`  
- This in turn calls `boost::add_vertex` on `adjacency_list &` (which `vec_adj_list_impl` can cast itself to due to CRTP)  
- This in turn calls `vec_adj_list_impl::added_vertex()` which is "injected" into `adjacency_list` via the `graph::maybe_named_graph` inheritance.  
  
When this last call (`added_vertex()`) happens on graphs that are _not_ named graphs, nothing happens (i.e. `added_vertex()` is a no-op). However, it does do a bunch of stuff if the graph is a named graph (and thus inherits from `graph::named_graph`. In that case there's some uninitialized size member which hasn't been initialized yet, which gets used at some point and then the whole thing blows up.  
  
The fix is simple: just inherit from `graph::maybe_named_graph` first, such that the call to `Base(x)` happens after the `graph::maybe_named_graph` base has already been default initialized.  
  
Note that there's no move constructor, i.e. the copy constructor will always get called even if an rvalue `graph_t` is passed to the constructor. So the above also happens for e.g. `graph_t graphB{getGraph()}`.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-02-04 10:11:17 UTC  
> Url: https://github.com/boostorg/graph/pull/415#issuecomment-2633442281  

Thanks for making an incremental improvement to the quality of this library!

---
