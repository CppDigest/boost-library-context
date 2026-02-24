# #246 - Confusing function signature of dfs, bfs searches [Open]

> Username: TonyXiang8787  
> Created at: 2021-03-09 07:34:44 UTC  
> Updated at: 2022-10-14 05:55:41 UTC  
> Url: https://github.com/boostorg/graph/issues/246  

In the [documentation](https://www.boost.org/doc/libs/1_75_0/libs/graph/doc/depth_first_search.html) of (for example) depth first search. The signature of the named parameter version requires a lvalue mutable reference graph.  
  
```c++  
// named parameter version  
template <class Graph, class class P, class T, class R>  
void depth_first_search(Graph& G,  
  const bgl_named_params<P, T, R>& params);  
```  
  
Also in the documentation below it requires a non-const lvalue. However, if I read the actual code signature, it is acutally `const Graph&`. This is confusing. The same confusion also appears in the documentation of undirected_dfs, breath first search, and the dfs/bfs visit.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-10-14 05:55:41 UTC  
> Url: https://github.com/boostorg/graph/issues/246#issuecomment-1278519703  

Can you open a pull request to fix it?
