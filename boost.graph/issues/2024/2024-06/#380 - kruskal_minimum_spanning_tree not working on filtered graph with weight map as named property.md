# #380 - kruskal_minimum_spanning_tree not working on filtered graph with weight map as named property [Open]

> Username: AlvoDa  
> Created at: 2024-06-13 07:20:07 UTC  
> Updated at: 2024-06-13 07:20:07 UTC  
> Url: https://github.com/boostorg/graph/issues/380  

Hi everyone,  
  
i cant use kruskal's alg on a filtered graph with a weight_map as named property.  
  
```  
#include <boost/graph/adjacency_list.hpp>  
#include "boost/graph/graph_utility.hpp"  
#include <boost/graph/filtered_graph.hpp>  
#include <boost/property_map/function_property_map.hpp>  
#include <boost/graph/kruskal_min_spanning_tree.hpp>  
  
using Graph  = boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS>;  
using Vertex = boost::graph_traits<Graph>::vertex_descriptor;  
using Edge   = boost::graph_traits<Graph>::edge_descriptor;  
  
int main()  
{  
  Graph G;  
    
  // create a complete graph with 4 nodes.  
  for (int i = 0; i < 5 ; ++i)  
  {  
    for (int j = i+1; j< 5; ++j)  
      boost::add_edge(i, j, G);  
  }  
  boost::print_graph(G);  
  /*  0 <--> 1 2 3 4  
      1 <--> 0 2 3 4  
      2 <--> 0 1 3 4  
      3 <--> 0 1 2 4  
      4 <--> 0 1 2 3  */  
  
  struct Filter  
  {  
    bool operator()(Vertex const & v) const  
    {  
      if (v != 0 && v != 2)  
        return true;  
      return false;  
    }  
  };  
  boost::filtered_graph G_f(G, boost::keep_all(), Filter());  
  boost::print_graph(G_f);  
  /* 1 <--> 3 4  
     3 <--> 1 4  
     4 <--> 1 3  */  
  
  auto wmap = boost::make_function_property_map<Edge, double>([](Edge const & e){ return 1.0; });  
  std::vector<Edge> mst;  
  
  // Works:  
  kruskal_minimum_spanning_tree(G, std::back_inserter(mst), boost::weight_map(wmap));  
    
  // Does not compile:  
  kruskal_minimum_spanning_tree(G_f, std::back_inserter(mst), boost::weight_map(wmap));  
    
  double result = 0;  
  for (auto const & e : mst)  
    result += wmap[e];  
  std::cout << result << "\n";  // prints 4  
}  
````  
  
Gives following errors:  
  
```  
error C2039: 'type': is not a member of 'boost::vertex_property_type<Graph>'  
error C2039:         [  
error C2039:         with  
error C2039:             Graph=boost::filtered_graph<Graph,boost::keep_all,main::Filter>  
error C2039:         ]  
error C3203: 'type': unspecialized class template can't be used as a template argument for template parameter 'Property', expected a real type  
```  
  
Workaround:  
`kruskal_minimum_spanning_tree(G_f, std::back_inserter(mst), boost::weight_map(wmap).vertex_index_map(get(boost::vertex_index, G_f)));`  
  
More details on stackoverflow (Thanks to user sehe who helped me out):  
https://stackoverflow.com/questions/78612600/boost-graph-using-kruskal-algorithm-on-filtered-graph-doesnt-compile
