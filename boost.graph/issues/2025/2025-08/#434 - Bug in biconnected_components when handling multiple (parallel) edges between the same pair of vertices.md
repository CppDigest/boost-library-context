# #434 - Bug in biconnected_components when handling multiple (parallel) edges between the same pair of vertices [Open]

> Username: GlebGrishuhin  
> Created at: 2025-08-04 05:00:14 UTC  
> Updated at: 2025-10-07 22:47:31 UTC  
> Url: https://github.com/boostorg/graph/issues/434  

### Version of Boost  
  
1.88.0  
  
### Problem description  
  
In a block of parallel edges (e.g., three edges between A and B), only one edge receives the correct biconnected component label.  
The rest are incorrectly assigned component ID 0 — even though they belong to the same biconnected component.  
  
### Expected behavior  
All edges in a block of parallel edges should have the same non-zero component ID  
  
### Actual behavior  
Only one edge gets the correct component ID; others are assigned 0 — which is wrong  
  
  
### Reproducible example  
  
Problem can be reproduced with such program.  
```C++  
#include <boost/config.hpp>  
#include <vector>  
#include <list>  
#include <boost/graph/biconnected_components.hpp>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/properties.hpp>  
#include <boost/property_map/property_map.hpp>  
#include <iterator>  
#include <iostream>  
  
namespace boost  
{  
    struct edge_component_t  
    {  
        typedef edge_property_tag kind;  
    } edge_component;  
}  
  
int main()  
{  
    using namespace boost;  
    typedef adjacency_list<vecS, vecS, undirectedS,  
        property<boost::vertex_index_t, std::size_t>,  
        property<edge_component_t, std::size_t>    
    > graph_t;  
  
    typedef graph_traits<graph_t>::vertex_descriptor vertex_t;  
    typedef graph_traits<graph_t>::vertices_size_type size_type;  
  
    std::vector<std::pair<size_t, size_t>> graph_edges = {  
        {0, 1}, {0, 1}, {0, 1}, // block of parallel edges  
        {1, 2}, // bridge  
        {2, 3}, {2, 3}, {2, 3}, // block of parallel edges  
        {3, 4}, // bridge  
        {4, 5} // bridge  
    };  
  
    graph_t g(graph_edges.size());  
  
    for (const auto& pair : graph_edges)  
        add_edge(pair.first, pair.second, g);  
  
    property_map<graph_t, edge_component_t>::type comp_map = get(edge_component, g);  
  
    std::vector<vertex_t> art_points;   
  
    auto num_comps = biconnected_components(g, comp_map, std::back_inserter(art_points));  
  
    std::cerr << "Found " << num_comps.first << " biconnected components.\n";  
    std::cerr << "Found " << art_points.size() << " articulation points.\n";  
  
    graph_traits<graph_t>::edge_iterator ei, ei_end;  
    for (boost::tie(ei, ei_end) = edges(g); ei != ei_end; ++ei)  
        std::cout << (char)(source(*ei, g) + 'A') << " -- "  
        << (char)(target(*ei, g) + 'A')  
        << " " << comp_map[*ei] << "\n";  
  
    return 0;  
}  
```  
  
### Actual output  
  
The proram output contains block index for each edge of graph:  
```  
Found 5 biconnected components.  
Found 4 articulation points.  
A -- B 4  
A -- B 0  
A -- B 0  
B -- C 3  
C -- D 2  
C -- D 0  
C -- D 0  
D -- E 1  
E -- F 0  
```  
  
Only one of parallel edges `A-B` has non-zero block index. Same stands for edges `C-D`.   
  
Picture shows the graph from example. Red vertices indicate articulation points. Each edge is labeled with index of biconnected component (block) to which it belongs Picture makes clear that output of example is incorrect - each group of parallel edges should form a distinct block.  
  
<img width="1082" height="163" alt="Image" src="https://github.com/user-attachments/assets/2d39a04c-3a4c-4222-b732-dc7cd9ac80d9" />  
  
### Expected output  
  
```  
Found 5 biconnected components.  
Found 4 articulation points.  
A -- B 4  
A -- B 4  
A -- B 4  
B -- C 3  
C -- D 2  
C -- D 2  
C -- D 2  
D -- E 1  
E -- F 0  
```

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2025-08-11 06:38:45 UTC  
> Url: https://github.com/boostorg/graph/issues/434#issuecomment-3173437649  

Thanks for the detailed explanation. Can you please make a pull request that just fixes the unit tests for this algorithm so that they fail on this case? Then it should be pretty easy for me, or you, or anyone else, to fix the actual bug.

---

## Comment 2

> Username: GlebGrishuhin  
> Created at: 2025-08-13 18:19:28 UTC  
> Url: https://github.com/boostorg/graph/issues/434#issuecomment-3185006880  

Thanks for the reply. I've added a unit test that reproduces the issue. See PR #435 - `test_graph_with_multi_edges` in `\test\biconnected_components_test.cpp`. The test currently fails, demonstrating the bug with multi-edge component indexing.
