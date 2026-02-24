# #351 - Improve docs for ResourceExtensionFunction in r_c_shortest_paths [Open]

> Username: andrea-cassioli-maersk  
> Created at: 2023-09-13 08:55:04 UTC  
> Updated at: 2025-12-12 19:54:51 UTC  
> Url: https://github.com/boostorg/graph/issues/351  

The ResourceExtensionFunction in r_c_shortest_paths takes a new and an old resource container.   
  
To me, in the docs it is unclear whether   
  
* the new Resource_Container is a copy of the old,   
* or a fresh one constructed via default constructor.  
  
In the former case I do not fully see the use of the old.  
  
Could anyone clarify this?

---

## Comment 1

> Username: andrea-cassioli-maersk  
> Created at: 2025-06-10 13:19:03 UTC  
> Url: https://github.com/boostorg/graph/issues/351#issuecomment-2959237962  

Anyone does have a clue? It seems from this constructor   
  
```  
 r_c_shortest_paths_label(const unsigned long n,  
        const Resource_Container& rc = Resource_Container(),  
        const boost::shared_ptr<  
            r_c_shortest_paths_label< Graph, Resource_Container > >  
            pl  
        = boost::shared_ptr<  
            r_c_shortest_paths_label< Graph, Resource_Container > >(),  
        const typename graph_traits< Graph >::edge_descriptor& ed  
        = graph_traits< Graph >::edge_descriptor(),  
        const typename graph_traits< Graph >::vertex_descriptor& vd  
        = graph_traits< Graph >::vertex_descriptor())  
    : num(n)  
    , cumulated_resource_consumption(rc)  
    , p_pred_label(pl)  
    , pred_edge(ed)  
    , resident_vertex(vd)  
    , b_is_dominated(false)  
    , b_is_processed(false)  
    {  
    }  
```  
  
  
that one might pass a default resource container. But the code seems to pass the old container and thus make a copy. This seems conflicting to me.   
  
Since the new and the old containers are passed, I would imagine the new to be pristine and to be set using the old as starting point for the extension. But because the new is a copy of the old, the old is redundant.  
  
Any thoughts?

---

## Comment 2

> Username: andreacassioli  
> Created at: 2025-12-12 19:54:20 UTC  
> Updated at: 2025-12-12 19:54:51 UTC  
> Url: https://github.com/boostorg/graph/issues/351#issuecomment-3647933397  

I have done some digging and   
  
- the implementation does copy the current container and pass the current and the new both to the extension function  
- In general the extension function does not need the current container, but just the new one. The only explanation I have is that the new container might be not fully initialized by the copy constructor, and thus passing the current container is necessary.  
  
From my side this issue can be closed. @jeremy-murphy
