# #263 - Bug in boost isomorphism with Associative Property Map [Open]

> Username: joemalle  
> Created at: 2021-06-02 22:15:27 UTC  
> Updated at: 2021-06-02 22:15:27 UTC  
> Url: https://github.com/boostorg/graph/issues/263  

Given two graphs that are the same and only contain a single vertex, boost isomorphism returns the wrong property map.  
  
```  
#include <cassert>  
#include <unordered_map>  
  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/isomorphism.hpp>  
  
using namespace boost;  
  
int main() {  
  using Graph = adjacency_list<vecS, vecS, directedS>;  
  using Vtx = Graph::vertex_descriptor;  
  
  Graph g1, g2;  
  add_vertex(g1);  
  add_vertex(g2);  
    
  std::unordered_map<Vtx, Vtx> vtx2vtxMap;  
  
  bool hasIso = isomorphism(g1, g2, isomorphism_map(make_assoc_property_map(vtx2vtxMap)));  
  
  assert(hasIso);  
  assert(vtx2vtxMap.size() == 1);  
  auto [k, v] = *vtx2vtxMap.begin();  
  assert(k == 0);  
  assert(v == 0); // output.s: /app/example.cpp:28: int main(): Assertion `v == 0' failed.  
    
  return 0;  
}  
```  
  
I'd expect the `vtx2vtxMap` to map `0 => 0` since there is only a 0 vertex in each graph.  Instead, it maps 0 to 18446744073709551615.  I've only seen this problem for this simple input graph.  
  
I'm running this with GCC 11.1 and Boost 1.64 on godbolt.org.
