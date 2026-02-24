# #47 avoid closed_plus in dijkstra_shortest_path [Closed]

> Username: ahhz  
> Created at: 2015-10-23 22:41:13 UTC  
> Updated at: 2015-10-23 22:46:22 UTC  
> Closed at: 2015-10-23 22:46:22 UTC  
> Url: https://github.com/boostorg/graph/pull/47  

dijkstra_shortest_path requires boost::closed_plus because of redundant checks in the relax function. By using a new relax_target() function it avoids combining weights with infinite distances and std::plus can be used.

---
