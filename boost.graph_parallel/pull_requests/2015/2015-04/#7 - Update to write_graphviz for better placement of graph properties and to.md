# #7 Update to write_graphviz for better placement of graph properties and to... [Closed]

> Username: PurelyApplied  
> Created at: 2015-04-28 00:00:45 UTC  
> Updated at: 2018-05-24 23:02:04 UTC  
> Closed at: 2018-05-24 23:02:04 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/7  

... accept cluster property writers.  
  
Graph property writer moved from repeated prints within each subgraph cluster to the beginning of the graphviz file.  
write_graphviz may be called with an additional input: cluster property writer.  This will write at the beginning of each subgraph cluster.  
Since subgraphs inheret graph properties, users should see no change if the passed graph property writer matched across all processes.  
If the graph property writer did not match across all processes, preference is given to process 0.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2016-11-01 01:54:55 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/7#issuecomment-257471488  

Seems like it'd be reasonable to add the ClusterPropertiesWriter as a default argument, so we don't break the API while still providing this extension point?  Any reason not to do that?

---
