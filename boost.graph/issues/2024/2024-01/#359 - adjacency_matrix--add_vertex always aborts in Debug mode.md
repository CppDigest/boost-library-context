# #359 - adjacency_matrix::add_vertex always aborts in Debug mode ? [Open]

> Username: Becheler  
> Created at: 2024-01-22 13:32:31 UTC  
> Updated at: 2024-01-23 10:52:43 UTC  
> Url: https://github.com/boostorg/graph/issues/359  

I found this in `graph/adjacency_matrix` ligne 966 :  
  
```cpp  
template < typename D, typename VP, typename EP, typename GP, typename A >  
inline typename adjacency_matrix< D, VP, EP, GP, A >::vertex_descriptor  
add_vertex(adjacency_matrix< D, VP, EP, GP, A >& g)  
{  
    // UNDER CONSTRUCTION  
    BOOST_ASSERT(false);  
    return *vertices(g).first;  
}  
```  
Does it mean there is no way to add vertices to an already-constructed adjacency_matrix ? Am i missing something ?

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2024-01-22 23:45:10 UTC  
> Url: https://github.com/boostorg/graph/issues/359#issuecomment-1905034359  

Yeah, weird. Looks like Jeremy Siek labelled it as `UNDER CONSTRUCTION` back in 2001, then Jeremiah Willcock added the assertion in 2011 as a way of enforcing it. So yes, looks adding and removing vertices was never completed. Something to put at the top of the TO-DO list!

---

## Comment 2

> Username: Becheler  
> Created at: 2024-01-23 10:52:43 UTC  
> Url: https://github.com/boostorg/graph/issues/359#issuecomment-1905785877  

Wonderful !
