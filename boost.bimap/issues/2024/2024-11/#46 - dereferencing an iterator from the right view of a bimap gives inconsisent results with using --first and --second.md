# #46 - dereferencing an iterator from the right view of a bimap gives inconsisent results with using ->first and ->second [Open]

> Username: hoorayphyer  
> Created at: 2024-11-22 17:34:17 UTC  
> Updated at: 2024-11-22 22:56:49 UTC  
> Url: https://github.com/boostorg/bimap/issues/46  

**Boost version:** 1.86  
**fmt version:** 11.0  
**g++ version:** Apple clang version 15.0.0 (clang-1500.3.9.4)  
**OS:**  macOS 14.1  
  
**Issue:**  
In the following code snippet  
```  
 boost::bimap<int, std::string> m;  
 m.insert({1, "one"});  
  
 for (auto iter = m.right.begin(); iter != m.right.end(); ++iter) {  
    auto [k, v] = *iter;  
    fmt::println("key = {}, val = {}", k, v);  
    fmt::println("first = {}, second = {}", iter->first, iter->second);  
  }  
```  
I'd expect the two ways to print the info regarding the order. Yet I observed  
```  
key = 1, val = one  
first = one, second = 1  
```  
where apprently using structured binding gives the "wrong" result in the right view.  
  
**Expected behavior:**  
Dereferencing a right iterator should give consistent results with `->first` and `->second`. Otherwise, it leads to subtle errors to use `bimap.right` in any generic code where a map is being traversed using the structured binding, i.e. `for (auto [k, v] : generic_map)`.
