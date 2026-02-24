# #373 - CSR: Indexed properties maps are unstable [Open]

> Username: sehe  
> Created at: 2024-05-03 02:11:46 UTC  
> Updated at: 2024-05-03 02:11:46 UTC  
> Url: https://github.com/boostorg/graph/issues/373  

The unit test `test_basic_csr_directed_graph` has been there, but disabled ever since it was added in 2012 because it didn't work.  
  
The corresponding version with external properties worked.  
  
I analyzed it and it turns out that the property maps from `indexed_properties` that underlie the bundled property maps returns an `iterator_property_map` into the actual model storage collections. However, since CSR stores nodes and edges in vectors, they can become invalidated.  
  
Due to the two-phase nature in which the parser builds the output CSR graph this happens by definition in `read_graphviz`.  
  
There is a workaround to replace the idiomatic:  
  
```c++  
TEST_GRAPH(graph_t, sample, g, "node_id", "", //  
    get(&Models::VertexBundle::name, g), // FIXME  
    get(&Models::VertexBundle::mass, g), // FIXME  
    get(&Models::EdgeBundle::weight, g) // FIXME  
);  
```  
  
With a custom property-map that **does** offer stability by indirecting via the graph model each time:  
  
```c++  
TEST_GRAPH(graph_t, sample, g, "node_id", "", //  
    boost::make_function_property_map< V >(  
        [&g](V v) -> std::string& { return g[v].name; }),  
    boost::make_function_property_map< V >(  
        [&g](V v) -> Mass& { return g[v].mass; }),  
    boost::make_function_property_map< E >(  
        [&g](E e) -> Weight& { return g[e].weight; })  
);  
```  
  
This is what is allows us to restore the unit test with the workaround. This issue is created in order to investigate  
  
 1. whether a safer property-map derivation should be made the default (removing a tricky UB trap)  
 1. whether other graph models can be reviewed for similar property-map invalidation  
 1. whether the documentation of such graph models should state property-map validity guarantees in some way; this may seem like a lot of work, e.g. arguably `adjacency_list` might be worst due to its high container configurability. However, `adjacency_list` already has extensive (terse) documentation on iterator/descriptor invalidation. Arguably, that entire group of model instances might defer to those guarantees for applicable property maps.
