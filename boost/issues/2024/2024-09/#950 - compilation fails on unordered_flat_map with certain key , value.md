# #950 - compilation fails on unordered_flat_map with certain key , value [Closed]

> Username: kantan2015  
> Created at: 2024-09-13 04:53:03 UTC  
> Updated at: 2024-09-13 04:56:10 UTC  
> Closed at: 2024-09-13 04:56:10 UTC  
> Url: https://github.com/boostorg/boost/issues/950  

unordered_flat_map::try_emplace fails to compile with certain key, value type.  
Key : boost::flyweight<std::string>  
Value: std::vector with move only type  
  
version: gcc13.2, boost 1.85  
  
```  
#include <unordered_map>  
#include <boost/unordered/unordered_map.hpp>  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <string>  
#include <boost/flyweight.hpp>  
  
template<class Map, class Key>   
void try_emplace(Key key) {  
    Map map;  
    map.try_emplace(key);  
}   
  
  
using move_only_vec = std::vector<std::unique_ptr<int>>;  
 try_emplace<boost::unordered_flat_map<int, move_only_vec>>(1);   // ok  
  
 boost::flyweight<std::string> s("1");   
 try_emplace<std::unordered_map<boost::flyweight<std::string>, move_only_vec>>(s); // ok  
 try_emplace<boost::unordered_map<boost::flyweight<std::string>, move_only_vec>>(s); // ok    
 //try_emplace<boost::unordered_flat_map<boost::flyweight<std::string>, move_only_vec>>(s); // ng     
 try_emplace<boost::unordered_flat_map<boost::flyweight<std::string>, std::unique_ptr<int>>>(s); // ok    
```
