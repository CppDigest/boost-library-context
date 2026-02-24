# #197 - breadth_first_visit substitues null BFS visitor [Closed]

> Username: jeffythedragonslayer  
> Created at: 2020-01-08 00:49:11 UTC  
> Updated at: 2020-03-27 01:29:50 UTC  
> Closed at: 2020-03-27 01:29:50 UTC  
> Url: https://github.com/boostorg/graph/issues/197  

breadth_first_visit ignores the visitor object passed in and uses a null one instead.  To recreate:  
```  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/properties.hpp>  
#include <boost/graph/breadth_first_search.hpp>  
#include <iostream>  
using namespace std;  
  
typedef boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS, boost::property<boost::vertex_index_t, uint>, boost::property<boost::edge_index_t, uint>> Graph;  
typedef boost::graph_traits<Graph>::vertex_descriptor                       vertex_t;  
typedef boost::graph_traits<Graph>::edge_descriptor                         edge_t;  
typedef boost::graph_traits<Graph>::vertex_descriptor                       vertex_t;  
  
  
struct CollectBFSVisitor : public boost::default_bfs_visitor  
{  
        virtual void initialize_vertex(vertex_t v, Graph const& g) {cout << "init vertex\n";};  
        virtual void discover_vertex(vertex_t v, Graph const& g) {};  
        virtual void examine_vertex(vertex_t v, Graph const& g) {};  
        virtual void finish_vertex(vertex_t v, Graph const& g) {};  
        virtual void black_target(edge_t, Graph const& g) {};  
        virtual void gray_target(edge_t, Graph const& g) {};  
        virtual void tree_target(edge_t, Graph const& g) {};  
        virtual void tree_edge(edge_t, Graph const& g) {};  
        virtual void non_tree_edge(edge_t, Graph const& g) {};  
};  
  
struct VertBuffer : boost::queue<vertex_t>  
{  
};  
  
int main()  
{  
        Graph g;  
        vertex_t x = add_vertex(g);  
        auto indexmap = boost::get(boost::vertex_index, g);  
        auto colormap = boost::make_vector_property_map<boost::default_color_type>(indexmap);  
  
        VertBuffer q;  
  
        CollectBFSVisitor v;  
        breadth_first_visit(g, x, q, v, colormap); // the cout doesn't run, but if you change it to breadth_first_search it does  
}  
```  
  
  
Problem line in boost/graph/breadth_first_search.hpp  
  
    breadth_first_visit  
      (ng, s,  
       choose_param(get_param(params, buffer_param_t()), boost::ref(Q)).get(),  
       choose_param(get_param(params, graph_visitor),  
                    make_bfs_visitor(null_visitor())),  
       choose_pmap(get_param(params, vertex_color), ng, vertex_color)  
       );

---

## Comment 1

> Username: jeffythedragonslayer  
> Created at: 2020-03-27 01:29:50 UTC  
> Url: https://github.com/boostorg/graph/issues/197#issuecomment-604768678  

My bad, I tried putting in a cout << "discover vertex\n"; and found that the visitor object is in fact not being ignored.  The line from breadth_first_search.hpp I posted seems to choose a default visitor if none was provided.
