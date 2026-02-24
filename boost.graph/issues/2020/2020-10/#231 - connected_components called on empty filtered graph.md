# #231 - connected_components called on empty filtered graph [Open]

> Username: pgerell  
> Created at: 2020-10-29 14:09:04 UTC  
> Updated at: 2020-11-10 17:22:06 UTC  
> Url: https://github.com/boostorg/graph/issues/231  

The connected_components function returns `(std::numeric_limits<comp_type>::max)() + 1` when called on a filtered graph with all vertices filtered out.  
  
An example of this problem can be found here:  
https://godbolt.org/z/vWsG8j  
  
If the line   
```cpp  
    if (num_vertices(g) == 0) return 0;  
```  
was replaced with  
```cpp  
   typedef typename boost::graph_traits<Graph>::vertex_iterator vi;  
    std::pair<vi, vi> verts = vertices(g);  
    if (verts.first == verts.second)  
      return 0;  
```  
the function would work as expected.

---

## Comment 1

> Username: pgerell  
> Created at: 2020-11-10 17:22:06 UTC  
> Url: https://github.com/boostorg/graph/issues/231#issuecomment-724847210  

Searching for "if (num_vertices" in the source tree finds more algorithms that may not work as expected on filtered graphs.
