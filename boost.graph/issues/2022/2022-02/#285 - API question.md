# #285 - API question. [Open]

> Username: swang206  
> Created at: 2022-02-08 20:26:35 UTC  
> Updated at: 2022-07-14 19:28:55 UTC  
> Url: https://github.com/boostorg/graph/issues/285  

https://github.com/GrammaTech/gtirb/blob/2bc074282ef1e33d1988ce8f069252810d1307a3/include/gtirb/CFG.hpp#L81  
  
  using type = boost::adjacency_list<  
      OutEdgeListS, VertexListS, DirectedS,  
      // Vertices are CfgNodes.  
      CfgNode*,  
      // Edges have labels.  
      EdgeLabel,  
      // The graph keeps track of vertex descriptors for  
      // each node.  
      std::unordered_map<const CfgNode*, vertex_descriptor>, EdgeListS>;  
  
How to get the EdgeLabel for each edge?

---

## Comment 1

> Username: jesseli2002  
> Created at: 2022-07-14 19:28:55 UTC  
> Url: https://github.com/boostorg/graph/issues/285#issuecomment-1184817928  

See https://www.boost.org/doc/libs/1_79_0/libs/graph/doc/bundles.html - in particular, the section on "Accessing bundled properties" - in summary, just use `graph[edge_descriptor]`
