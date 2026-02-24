# #59 trac 11374: find_flow_cost() not working with bundled properties. [Closed]

> Username: maelvls  
> Created at: 2016-03-02 14:42:38 UTC  
> Updated at: 2016-04-26 09:31:52 UTC  
> Closed at: 2016-03-02 17:27:46 UTC  
> Url: https://github.com/boostorg/graph/pull/59  

UPDATE: moved this commit to PR #61. This fix had to go with two other fixes.   
  
When using bundled for Weight and Cost properties, find_flow_cost() coudln't work because the properties had been "hard coded" instead of using generic types.  
  
Fixes https://svn.boost.org/trac/boost/ticket/11374  
  
---  
  
Minimal not-working example:  
  
``` cpp  
#include <boost/config.hpp>  
#include <iostream>  
#include <string>  
#include <boost/graph/edmonds_karp_max_flow.hpp>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/read_dimacs.hpp>  
#include <boost/graph/graph_utility.hpp>  
#include <boost/graph/find_flow_cost.hpp>  
  
using namespace boost;  
  
typedef adjacency_list_traits<vecS,vecS,directedS> traits;  
struct edge_t {  
    double capacity;  
    float cost;  
    float residual_capacity;  
    traits::edge_descriptor reversed_edge;  
};  
struct node_t {  
    std::string name;  
    boost::default_color_type color;  
    traits::edge_descriptor predecessor;  
};  
typedef adjacency_list < listS, vecS, directedS, node_t, edge_t > Graph;  
  
int main()  
{  
    Graph g;  
    property_map < Graph, double edge_t::* >::type capacity = get(&edge_t::capacity, g);  
    property_map < Graph, float edge_t::* >::type cost = get(&edge_t::cost, g);  
    property_map < Graph, float edge_t::* >::type residual_capacity = get(&edge_t::residual_capacity, g);  
    property_map < Graph, traits::edge_descriptor edge_t::* >::type rev = get(&edge_t::reversed_edge, g);  
    property_map < Graph, std::string node_t::* >::type name = get(&node_t::name, g);  
    property_map < Graph, boost::default_color_type node_t::* >::type col = get(&node_t::color, g);  
    property_map < Graph, traits::edge_descriptor node_t::* >::type pred = get(&node_t::predecessor, g);  
    traits::vertex_descriptor s, t;  
    read_dimacs_max_flow(g, capacity, rev, s, t);  
  
    long flow, flow_cost;  
  
    // XXX The "bundled properties" version (producting errors)  
    find_flow_cost(g,capacity,residual_capacity,cost);  
  
    return EXIT_SUCCESS;  
}  
```

---
