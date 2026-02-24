# #309 - The 3-parameter overload of `boykov_kolmogorov_max_flow` does not compile [Closed]

> Username: sebrockm  
> Created at: 2022-10-21 22:21:40 UTC  
> Updated at: 2022-10-23 20:35:50 UTC  
> Closed at: 2022-10-23 20:35:50 UTC  
> Url: https://github.com/boostorg/graph/issues/309  

According to the [documentation](https://www.boost.org/doc/libs/1_80_0/libs/graph/doc/boykov_kolmogorov_max_flow.html), there is a minimalistic overload of `boykov_kolmogorov_max_flow` that only takes the graph, a source, and a sink. All other parameters are set to their defaults (as stated in the section "Named Parameters").  
  
However, this overload doesn't compile. Minimal reproducible example:  
  
``` C++  
#include <boost/graph/graph_traits.hpp>  
#include <boost/graph/properties.hpp>  
#include <boost/graph/boykov_kolmogorov_max_flow.hpp>  
  
  
#include <utility>  
  
  
namespace my_graph  
{  
  
struct Graph  
{  
    // graph concept  
    using vertex_descriptor = size_t;  
    using edge_descriptor = std::pair<size_t, size_t>;  
    using directed_category = boost::directed_tag;  
    using edge_parallel_category = boost::disallow_parallel_edge_tag;  
    struct traversal_category : boost::vertex_list_graph_tag,  
                                boost::edge_list_graph_tag,  
                                boost::incidence_graph_tag,  
                                boost::adjacency_matrix_tag  
    {  
    };  
  
    static vertex_descriptor null_vertex() { return 0; }  
  
    // incidence graph concept  
    using out_edge_iterator = std::nullptr_t;  
    using degree_size_type = size_t;  
  
    // vertex list graph concept  
    using vertex_iterator = std::nullptr_t;  
    using vertices_size_type = size_t;  
  
    // edge list graph concept  
    using edge_iterator = std::nullptr_t;  
    using edges_size_type = size_t;  
};  
  
// incidence graph concept  
  
Graph::vertex_descriptor source(const Graph::edge_descriptor&, const Graph&) { return {}; }  
  
Graph::vertex_descriptor target(const Graph::edge_descriptor&, const Graph&) { return {}; }  
  
std::pair<Graph::out_edge_iterator, Graph::out_edge_iterator> out_edges(  
    Graph::vertex_descriptor, const Graph&)  
{  
    return { {}, {} };  
}  
  
Graph::degree_size_type out_degree(Graph::vertex_descriptor, const Graph&) { return 0; }  
  
// vertex list graph concept  
  
std::pair<Graph::vertex_iterator, Graph::vertex_iterator> vertices(const Graph&)  
{  
    return { {}, {} };  
}  
  
Graph::vertices_size_type num_vertices(const Graph&) { return 0; }  
  
// edge list graph concept  
  
Graph::edges_size_type num_edges(const Graph&) { return 0; }  
  
std::pair<Graph::edge_iterator, Graph::edge_iterator> edges(const Graph&) { return { {}, {} }; }  
  
// adjacency matrix graph concept  
  
std::pair<Graph::edge_descriptor, bool> edge(  
    Graph::vertex_descriptor, Graph::vertex_descriptor, const Graph&)  
{  
    return { {}, {} };  
}  
  
// property maps for boykov_kolmogorov_max_flow  
  
auto get(boost::edge_capacity_t, Graph&)  
{  
    return boost::make_constant_property<Graph::edge_descriptor>(0);  
}  
  
auto get(boost::edge_residual_capacity_t, Graph&)  
{  
    return boost::make_constant_property<Graph::edge_descriptor>(0);  
}  
  
auto get(boost::edge_reverse_t, const Graph&)  
{  
    return boost::make_constant_property<Graph::edge_descriptor>(Graph::edge_descriptor {});  
}  
  
auto get(boost::vertex_predecessor_t, Graph&)  
{  
    return boost::make_constant_property<Graph::vertex_descriptor>(Graph::edge_descriptor {});  
}  
  
auto get(boost::vertex_color_t, Graph&)  
{  
    return boost::make_constant_property<Graph::vertex_descriptor>(boost::default_color_type {});  
}  
  
auto get(boost::vertex_distance_t, Graph&)  
{  
    return boost::make_constant_property<Graph::vertex_descriptor>(0);  
}  
  
auto get(boost::vertex_index_t, Graph&)  
{  
    return boost::make_constant_property<Graph::vertex_descriptor>(0);  
}  
  
}  
  
int main()  
{  
    my_graph::Graph G;  
  
    boost::boykov_kolmogorov_max_flow(G, 0, 1);  
}  
```  
  
[Live on compiler-explorer.](https://compiler-explorer.com/z/6sTej3axx)

---

## Comment 1

> Username: sebrockm  
> Created at: 2022-10-23 20:35:50 UTC  
> Url: https://github.com/boostorg/graph/issues/309#issuecomment-1288195803  

It does work, if you do it correctly.  
My error was that I did not specify overloads for `property_map` like so:  
``` C++  
namespace boost  
{  
    template<>  
    struct property_map<my_graph::Graph, boost::edge_capacity_t>  
    {  
        using type = constant_writable_property_map<my_graph::Graph::edge_descriptor, int>;  
        using const_type = constant_writable_property_map<my_graph::Graph::edge_descriptor, int>;  
    };  
}  
```  
An updated compiling example is here: https://compiler-explorer.com/z/PjP6MWsWv
