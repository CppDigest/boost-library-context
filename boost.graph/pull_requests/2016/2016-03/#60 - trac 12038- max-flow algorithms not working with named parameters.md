# #60 trac 12038: max-flow algorithms not working with named parameters. [Closed]

> Username: maelvls  
> Created at: 2016-03-02 15:00:34 UTC  
> Updated at: 2016-04-26 09:31:55 UTC  
> Closed at: 2016-03-02 17:27:31 UTC  
> Url: https://github.com/boostorg/graph/pull/60  

UPDATE: moved this commit to PR #61. This fix had to go with two other fixes.   
  
The named parameter "Capacity" was not working. I just had to reverse the order of `get_param_type` parameters.  
  
Here are the max-flow algorithms that are curretly not working with the named parameter "Capacity":  
- edmonds_karp_max_flow,  
- push_relabel_max_flow,  
- boykov_kolmogorov_max_flow.  
  
Fixes https://svn.boost.org/trac/boost/ticket/12038  
  
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
  
        // XXX The "named parameter version" (producing errors)  
        // XXX I chose to show the error with edmonds_karp_max_flow().  
        flow = edmonds_karp_max_flow(g, s, t,  
                        capacity_map(capacity)  
                        .residual_capacity_map(residual_capacity)  
                        .reverse_edge_map(rev)  
                        .color_map(col)  
                        .predecessor_map(pred));  
  
        return EXIT_SUCCESS;  
}  
```

---
