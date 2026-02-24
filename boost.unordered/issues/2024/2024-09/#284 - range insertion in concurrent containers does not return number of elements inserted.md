# #284 - range insertion in concurrent containers does not return number of elements inserted [Closed]

> Username: joaquintides  
> Created at: 2024-09-14 09:18:25 UTC  
> Updated at: 2024-09-28 03:24:09 UTC  
> Closed at: 2024-09-28 03:24:09 UTC  
> Url: https://github.com/boostorg/unordered/issues/284  

`boost::concurrent_flat_map` range insertion (from a pair of iterators or a `std::initializer_list`) is [documented](https://www.boost.org/doc/libs/1_86_0/libs/unordered/doc/html/unordered.html#concurrent_flat_map_insert_iterator_range) as returning the number of elements inserted, yet the implementation's return type is `void`:  
  
https://github.com/boostorg/unordered/blob/4c0aea983ebec4a9491381e7e4f3a9da844d19ba/include/boost/unordered/concurrent_flat_map.hpp#L421-L427  
  
Same for visitation-enabled variants, and for all concurrent containers.
