# #420 - Vertex property `vertex_index_t` silently discarded when `vecS` selector used vertex list [Open]

> Username: user202729  
> Created at: 2025-02-15 08:24:21 UTC  
> Updated at: 2025-02-15 08:24:21 UTC  
> Url: https://github.com/boostorg/graph/issues/420  

As in the title.  
  
Consider the following code  
  
```cpp  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/property_map/property_map.hpp>  
#include <type_traits>  
#include <vector>  
  
typedef int v_index;  
typedef long e_index;  
  
typedef boost::adjacency_list<  
    boost::vecS,              // OutEdgeList  
    boost::vecS,              // VertexList  
    boost::undirectedS,       // DirectedS  
    boost::property<boost::vertex_index_t, v_index>, // Vertex property.  
    boost::no_property,       // Edge property (can be enhanced if needed).  
    boost::no_property,       // Graph property.  
    boost::vecS               // EdgeList  
> Graph;  
  
typedef boost::property_map<Graph, boost::vertex_index_t>::type vertex_to_int_map;  
  
static_assert(std::is_same_v<typename vertex_to_int_map::value_type, v_index>,  
              "The vertex property map's value type must be v_index");  
```  
  
The static assertion fails. Changing `VertexList` to e.g. `boost::listS` makes it pass.  
  
This causes a little problem in generic code which assumes the type returned by the `vertex_to_int_map` property map is equal to `v_index`. For example https://github.com/sagemath/sage/pull/39526 .  
  
Not sure what's the best course of action here. I think the easiest one would be to make the property map just cast the result to the correct type (assume it can be casted of course, but what if the user specifies something weird like `std::string`?)
