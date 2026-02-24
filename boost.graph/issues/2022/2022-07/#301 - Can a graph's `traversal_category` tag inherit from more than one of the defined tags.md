# #301 - Can a graph's `traversal_category` tag inherit from more than one of the defined tags? [Closed]

> Username: jesseli2002  
> Created at: 2022-07-15 01:21:07 UTC  
> Updated at: 2022-07-19 00:19:39 UTC  
> Closed at: 2022-07-19 00:19:39 UTC  
> Url: https://github.com/boostorg/graph/issues/301  

I have a graph which implements both `VertexListGraph` and `IncidenceGraph`. The [documentation](https://www.boost.org/doc/libs/1_79_0/libs/graph/doc/Graph.html) for the `Graph` concept says that the `traversal_category` must be one of the predefined types, while the [documentation for graph traits](https://www.boost.org/doc/libs/1_79_0/libs/graph/doc/graph_traits.html) notes that a custom tag can be created, but does not indicate whether such a tag can inherit from more than one tag at a time:  
> ... You can also create your own tag which should inherit from one of the above.  
  
If more than one tag can be inherited, maybe change it to say "one or more of the above"?   
  
(Incidentally, the documentation for graph traits mentions `vertex_and_edge_list_graph_tag`, which doesn't seem to exist?)

---

## Comment 1

> Username: sebrockm  
> Created at: 2022-07-16 07:42:43 UTC  
> Url: https://github.com/boostorg/graph/issues/301#issuecomment-1186110318  

Yes, it looks like you can absolutely do that.  
For example, `adjacency_matrix` is doing just that:  
https://github.com/boostorg/graph/blob/5f4ce096f8edf83ae15ab40f284e45a102769166/include/boost/graph/adjacency_matrix.hpp#L432-L438  
https://github.com/boostorg/graph/blob/5f4ce096f8edf83ae15ab40f284e45a102769166/include/boost/graph/adjacency_matrix.hpp#L495  
So, the actual `traversal_category` is not only `adjacency_matrix_tag` but also all the others you see there.  
Note that it uses `virtual` inheritance.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2022-07-19 00:19:39 UTC  
> Url: https://github.com/boostorg/graph/issues/301#issuecomment-1188461438  

Great answer, @sebrockm , thanks.
