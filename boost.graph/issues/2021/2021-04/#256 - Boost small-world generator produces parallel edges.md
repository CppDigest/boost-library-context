# #256 - Boost small-world generator produces parallel edges [Open]

> Username: ThGaskin  
> Created at: 2021-04-23 08:57:16 UTC  
> Updated at: 2021-04-23 08:57:16 UTC  
> Url: https://github.com/boostorg/graph/issues/256  

The Boost small-world generator as defined in [small_world_generator.hpp](https://github.com/boostorg/graph/blob/develop/include/boost/graph/small_world_generator.hpp) can produce parallel edges.   
  
The problem lies in lines 74–76:  
  
```cpp  
if (x < prob)  
        {  
            vertices_size_type lower = (source + n - k / 2) % n;  
            vertices_size_type upper = (source + k / 2) % n;  
            do  
            {  
                current.second = rand_vertex_gen(*gen);  
            } while ((current.second >= lower && current.second <= upper)    // <---- L74   
                || (upper < lower    
                    && (current.second >= lower || current.second <= upper)));  
        }  
else  
        {  
            current.second = target;  
        }  
```  
  
While this guarantees that parallel edges cannot be created between neighbouring vertices in range < k, it does not prevent edges being rewired to _already rewired edges_, i.e. edges outside the distance-k-neighbourhood.   
  
**Proposed fix:**  
  
Inserting a check à la `if not boost::edge(v, w, g).second` should solve the issue
