# #322 - provide checked form of vertex/edge property access method [Open]

> Username: zhuoqiang  
> Created at: 2023-01-09 08:48:08 UTC  
> Updated at: 2023-01-09 08:48:08 UTC  
> Url: https://github.com/boostorg/graph/issues/322  

in stl, there are both checked and unchecked method to access the value from container  
  
```cpp  
vector[0]; // unchecked, may UB  
vector.at(1); // checked, may throw  
  
map["key1"]; // auto add missing key  
map.at("key2"); // checked, may throw  
```  
  
graph as a container, however, does not provide a checked method to access the vertex/edge property using vertex/edge id  
  
```cpp  
graph[vertex_id]; // unchecked, may UB  
graph[edge_id]; // unchecked, may UB  
```  
  
How about providing a checked access method that throw exception in case of missing id. Also helps to keep the API consist with STL containers  
  
```cpp  
graph.at(vertex_id); // unchecked, may throw  
graph.at(edge_id); // unchecked, may throw  
```  
  
and in general, not sure if I missed it but could not find API to check if the id is valid or not:  
  
```cpp  
graph.contains(vertex_id); // not sure if it exists  
```
