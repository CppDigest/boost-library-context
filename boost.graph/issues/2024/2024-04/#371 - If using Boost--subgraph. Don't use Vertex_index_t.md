# #371 - If using Boost::subgraph. Don't use Vertex_index_t. [Open]

> Username: edwinclement08  
> Created at: 2024-04-29 10:32:17 UTC  
> Updated at: 2024-04-29 10:32:17 UTC  
> Url: https://github.com/boostorg/graph/issues/371  

If any of y'all end up using boost::subgraph and try to add property map to it, then vertex_index_t does not work(looks like it is internally used by subgraph.
