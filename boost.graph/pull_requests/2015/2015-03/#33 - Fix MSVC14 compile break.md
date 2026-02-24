# #33 Fix MSVC14 compile break [Merged]

> Username: MarcelRaad  
> Created at: 2015-03-02 17:49:49 UTC  
> Updated at: 2015-12-21 10:35:28 UTC  
> Merged at: 2015-05-15 01:43:03 UTC  
> Closed at: 2015-05-15 01:43:03 UTC  
> Url: https://github.com/boostorg/graph/pull/33  

array_binary_tree_node::children_type::iterator pretends to be a bidirectional iterator but does not define operator--, which results in compile breaks with Visual C++ 2015:  
http://www.boost.org/development/tests/develop/developer/output/teeks99-08f-win2012R2-64on64-boost-bin-v2-libs-graph-test-dijkstra_heap_performance-test-msvc-14-0-debug-threading-multi.html

---
