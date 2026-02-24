# #195 - breadth_first_search segfaults on graphs with no edges [Open]

> Username: jeffythedragonslayer  
> Created at: 2020-01-01 06:20:12 UTC  
> Updated at: 2021-04-20 13:35:10 UTC  
> Url: https://github.com/boostorg/graph/issues/195  

breath_first_search appears to not be robust against degenerate graphs, such as a single vertex with no edges.  Adding an edge connecting this single vertex to itself seems to be a workaround.  
  
To reproduce:  
```  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/properties.hpp>  
#include <boost/graph/breadth_first_search.hpp>  
  
typedef boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS, boost::property<boost::vertex_index_t, uint>, boost::property<boost::edge_index_t, uint>> Graph;  
typedef boost::graph_traits<Graph>::vertex_descriptor                       vertex_t;  
  
  
struct BFSVisitor : boost::default_bfs_visitor  
{  
};  
  
int main()  
{  
        Graph g;  
        vertex_t x;  
  
        add_vertex(x, g);  
  
        //add_edge(x, x, g); // uncomment this line for workaround  
  
        BFSVisitor v;  
        breadth_first_search(g, x, boost::visitor(v));  
}  
```  
  
On Fedora this crashes with:  
  
boostbug: /usr/local/boost/boost/graph/two_bit_color_map.hpp:87: void boost::put(const boost::two_bit_color_map<IndexMap>&, typename boost::property_traits<PMap>::key_type, boost::two_bit_color_type) [with IndexMap = boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_index_t, unsigned int>, long unsigned int>; typename boost::property_traits<PMap>::key_type = long unsigned int]: Assertion `(std::size_t)i < pm.n' failed.  
[1]    14139 abort (core dumped)  ./boostbug

---

## Comment 1

> Username: jeffythedragonslayer  
> Created at: 2020-03-26 21:40:50 UTC  
> Url: https://github.com/boostorg/graph/issues/195#issuecomment-604701526  

The workaround does not work on MSVC, the value of x is all C's in hex and it calls _Xlength_error("vector too long")  
  
    // Here we override the directed_graph_helper add_edge() function  
    // so that the number of vertices is automatically changed if  
    // either u or v is greater than the number of vertices.  
    template <class Graph, class Config, class Base>  
    inline std::pair<typename Config::edge_descriptor, bool>  
    add_edge(typename Config::vertex_descriptor u,  
             typename Config::vertex_descriptor v,  
             const typename Config::edge_property_type& p,  
             vec_adj_list_impl<Graph, Config, Base>& g_)  
    {  
      BOOST_USING_STD_MAX();  
      typename Config::vertex_descriptor x = max BOOST_PREVENT_MACRO_SUBSTITUTION(u, v);  
      if (x >= num_vertices(g_))  
        g_.m_vertices.resize(x + 1);

---

## Comment 2

> Username: Amritha16  
> Created at: 2021-04-17 15:52:10 UTC  
> Url: https://github.com/boostorg/graph/issues/195#issuecomment-821844060  

Changing the graph's basic structure because it doesn't work for an edge case doesn't seem right. It would make more sense to add initial trivial checks in boost's bfs implementation itself.

---

## Comment 3

> Username: jeffythedragonslayer  
> Created at: 2021-04-17 17:37:21 UTC  
> Url: https://github.com/boostorg/graph/issues/195#issuecomment-821859059  

I agree, that is why I called adding an edge a workaround and not a bugfix.  The workaround was just to get me unstuck until the bfs implementation is fixed.  I'd to prefer to call this situation the 'single vertex case' not an 'edge case' because originally there were no edges in that graph.

---

## Comment 4

> Username: Valliammai19  
> Created at: 2021-04-20 13:35:10 UTC  
> Url: https://github.com/boostorg/graph/issues/195#issuecomment-823279265  

I think I can take this issue up and add a trivial check
