# #232 - Boost max flow algorithms do not compile [Closed]

> Username: rudenkornk  
> Created at: 2020-11-09 18:01:21 UTC  
> Updated at: 2022-07-25 01:44:50 UTC  
> Closed at: 2022-07-25 01:44:50 UTC  
> Url: https://github.com/boostorg/graph/issues/232  

Boost provides three different algorithms for finding max flow in directed graphs: **boykov_kolmogorov**, **edmonds_karp** and **push_relabel**. All of them have named and non-named parameter versions. Parameter sets they use are also very similar.  
Despite that, with same parameters some of these algorithms compile and some of them do not. This is the case only for graph without **edge_capacity_t** interior property.  
  
**push_relabel** compiles nicely with both named and non-named version:  
``` c++  
 using Graph =  
    boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS,  
                        VertexProperty, EdgeProperty>;  
  auto props = boost::capacity_map(capacity)  
               .residual_capacity_map(residual_capacity)  
               .reverse_edge_map(reverse_edge_map)  
               .vertex_index_map(vertex_index_map)  
               .color_map(color_map)  
               .predecessor_map(predcessor_map)  
               .distance_map(distance_map);  
  boost::push_relabel_max_flow(g, s, t, props);  
  boost::push_relabel_max_flow(g, s, t, capacity, residual_capacity,  
                   reverse_edge_map, vertex_index_map);  
```  
**boykov_kolmogorov** compiles with non-named version:  
```c++  
  boost::boykov_kolmogorov_max_flow(g, capacity, residual_capacity,  
                                    reverse_edge_map,  
                                    vertex_index_map, s, t);  
```  
But fails with named version:  
```c++  
 boost::boykov_kolmogorov_max_flow(g, s, t, props);  
```  
> /celibs/boost_1_73_0/boost/graph/detail/adjacency_list.hpp:2768:17: error: forming reference to void  
  
**edmonds_karp** fails with both named and non-named versions with same error:  
```c++  
boost::edmonds_karp_max_flow(g, s, t, props);  
boost::edmonds_karp_max_flow(g, s, t, capacity, residual_capacity, reverse_edge_map,  
                          color_map, predcessor_map);  
```  
  
> /celibs/boost_1_73_0/boost/concept_check.hpp:147:9: error: use of deleted function  
  
This issue was also [asked on StackOverflow](https://stackoverflow.com/questions/64734193/boost-max-flow-algorithms-do-not-compile-error-forming-reference-to-void). User sehe suggested that the problem is connected to some subtle error in `choose_const_pmap` function  
  
Full example:  
[Live on godbolt](https://godbolt.org/z/dvjfec)  
```c++  
#include <boost/config.hpp>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/graph_utility.hpp>  
#include <boost/property_map/function_property_map.hpp>  
#include <boost/graph/boykov_kolmogorov_max_flow.hpp>  
#include <boost/graph/edmonds_karp_max_flow.hpp>  
#include <boost/graph/push_relabel_max_flow.hpp>  
  
int main(){   
  struct VertexProperty final {};  
  struct EdgeProperty final {};  
  using Graph =  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS,  
                            VertexProperty, EdgeProperty>;  
  using Edge = boost::graph_traits<Graph>::edge_descriptor;  
  using Vertex = boost::graph_traits<Graph>::vertex_descriptor;  
  auto g = Graph{};  
  auto s = Vertex{};  
  auto t = Vertex{};  
  
  auto residualCapacityMap = std::vector<int>{};  
  auto reverseEdgeMap = std::vector<Edge>{};  
  auto colorMap = std::vector<boost::default_color_type>{};  
  auto predcessorMap = std::vector<Edge>{};  
  auto distanceMap = std::vector<int>{};  
  
  
  auto vertex_index_map = boost::make_function_property_map<Vertex>(  
      [&](auto const &v) { return 0; });  
  auto edge_index_map = boost::make_function_property_map<Edge>(  
      [&](auto const &e) { return 0; });  
  auto capacity = boost::make_function_property_map<Edge>(  
      [&](auto const &e) -> int { return 0; });  
  auto residual_capacity = boost::make_iterator_property_map(  
      residualCapacityMap.begin(), edge_index_map);  
  auto reverse_edge_map =  
      boost::make_iterator_property_map(reverseEdgeMap.begin(), edge_index_map);  
  auto color_map =  
      boost::make_iterator_property_map(colorMap.begin(), vertex_index_map);  
  auto predcessor_map = boost::make_iterator_property_map(predcessorMap.begin(),  
                                                    vertex_index_map);  
  auto distance_map =  
      boost::make_iterator_property_map(distanceMap.begin(), vertex_index_map);  
  
  auto props = boost::capacity_map(capacity)  
                   .residual_capacity_map(residual_capacity)  
                   .reverse_edge_map(reverse_edge_map)  
                   .vertex_index_map(vertex_index_map)  
                   .color_map(color_map)  
                   .predecessor_map(predcessor_map)  
                   .distance_map(distance_map);  
  
  // push_relabel compiles nicely  
  boost::push_relabel_max_flow(g, s, t, props);  
  boost::push_relabel_max_flow(g, s, t, capacity, residual_capacity,  
                       reverse_edge_map, vertex_index_map);  
  
  // As well as boykov_kolmogorov_max_flow with non-named version  
  boost::boykov_kolmogorov_max_flow(g, capacity, residual_capacity,  
                                    reverse_edge_map,  
                                    vertex_index_map, s, t);  
  // But named version boykov_kolmogorov fails  
#if 1  
  boost::boykov_kolmogorov_max_flow(g, s, t, props);  
#endif  
  
  // Non-named and named versions of edmonds_karp fail with same error  
#if 0  
  boost::edmonds_karp_max_flow(g, s, t, props);  
#endif  
#if 0  
  boost::edmonds_karp_max_flow(g, s, t, capacity, residual_capacity, reverse_edge_map,  
                              color_map, predcessor_map);  
#endif  
}  
```

---

## Comment 1

> Username: sebrockm  
> Created at: 2022-07-25 00:40:35 UTC  
> Updated at: 2022-07-25 00:42:00 UTC  
> Url: https://github.com/boostorg/graph/issues/232#issuecomment-1193434708  

@rudenkornk I found the issue with the `boykov_kolmogorov_max_flow` call and provided a fix (#304).  
  
I also found the issue with your `edmonds_karp_max_flow` calls. However, it is totally unrelated to the issue with `boykov_kolmogorov_max_flow`:  
The issue is that `edmonds_karp_max_flow` [internally](https://github.com/boostorg/graph/blob/fcfc152f85cd65f12a68d6977025c4f4817b769a/include/boost/graph/edmonds_karp_max_flow.hpp#L38) uses a [`filtered_graph`](https://www.boost.org/doc/libs/1_79_0/libs/graph/doc/filtered_graph.html) for filtering residual edges. As you can see in the docs, the predicates of the `filtered_graph` must be default constructible, but yours isn't.  
This `filtered_graph` uses an edge predicate that stores the residual capacity property map. In your case, the residual property map stores a copy of `edge_index_map` which in turn stores a lambda that captures. Lambdas are default constructible since C++20, but only if they don't capture. Yours does and hence the entire chain, up to the `out_edge_iterator` of the `filtered_graph`, is not default constructible, which is what the error is telling you.  
  
Two possible solutions I can think of:  
1. Remove the `&` from your `edge_index_map` lambda: https://godbolt.org/z/oKo3cG46T  
2. Don't have any lambdas involved in your residual capacity map
