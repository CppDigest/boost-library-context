# #340 Add support for GraphViz cgraph library [Open]

> Username: tobylorenz  
> Created at: 2023-06-01 22:22:17 UTC  
> Updated at: 2025-02-26 22:51:50 UTC  
> Url: https://github.com/boostorg/graph/pull/340  

Hi,  
  
GraphViz provides a native C API, called cgraph.  
This adds supports for cgraph in a similar way to existing leda_graph.hpp and stanford_graph.hpp implementations.  
  
The implementation already works well and fulfills all static `BOOST_CONCEPT_ASSERT` tests. However there are some issues remaining though. I tagged all issues in the source code with `@todo`.  
  
1. The cgraph (`Agraph_t`) can be made directed or undirected, and also strict (=disallow parallel edges) or not. This is a runtime setting, set during instantiation or determined when loading graphviz files. I found no suitable way to handle this runtime setting already in the template in order to define the typedefs for `bidirectional_tag` and `allow_parallel_edge_tag`. How can this be handled?  
2. cgraph has a longer list of properties on all levels, which I don't want to define all as property_tags. Is there a way to dynamically handle this and prevent `BOOST_DEF_PROPERTY`, `property_map<...>`, `get`, ... implementations for each of them?  
3. The `add_edge(u, v, ep, g)` and `add_vertex(vp, g)` functions are currently configured to transport the edge's name in `ep` resp. the vertex's name in `vp`. But I could also feed in a `std::map`, so that `add_vertex("V", g)` becomes `add_vertex{{"name", "V"}, g)`. Would that make sense?  
4. The boost concept checks fail, unless I pull all functions into global namespace by `using boost::out_edges;`, .... Any idea why this happens?  
5. subgraphs are currently not implemented.  
  
But overall it already works quite well for me. I can copy an existing graph into the cgraph structure to get it visualized with GraphViz.  
  
Bye  
Tobias

---
