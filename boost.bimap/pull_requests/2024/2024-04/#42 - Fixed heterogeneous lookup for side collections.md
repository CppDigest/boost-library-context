# #42 Fixed heterogeneous lookup for side collections [Merged]

> Username: joaquintides  
> Created at: 2024-04-01 16:27:40 UTC  
> Updated at: 2024-04-01 17:00:08 UTC  
> Merged at: 2024-04-01 17:00:08 UTC  
> Closed at: 2024-04-01 17:00:08 UTC  
> Url: https://github.com/boostorg/bimap/pull/42  

Fixed heterogeneous lookup for side collections. This feature probably never worked (there were no tests exercising it).  
  
**Explanation and solution**  
  
`boost::bimap` is basically a wrapper over a `boost::multi_index_container` with one index for the left part and another one for the right part. The way it's generated, these internal indexes have `const` key types: for instance, for  `bimap<unordered_set_of<std::string, ...>, ...>`, the `key_type` typedef of the left index is `const std::string` rather than plain `std::string` . Now, the following internal class is called whenever Boost.Bimap passes a key argument to Boost.MultiIndex:  
  
https://github.com/boostorg/bimap/blob/e5c2657a9e2d6184622ad4ccd1373e6c60a7efca/include/boost/bimap/container_adaptor/detail/identity_converters.hpp#L155-L181  
  
The specialization starting in L168 is supposed to be the default one, but it's not selected when calling `find` etc. because of the constness of the index's key. Changing L168-169 to  
  
```cpp  
template< class Key >  
struct key_to_base_identity< Key, const Key >  
```  
solves the problem.

---
