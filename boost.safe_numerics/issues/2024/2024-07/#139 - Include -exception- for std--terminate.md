# #139 - Include <exception> for std::terminate [Open]

> Username: francoisk  
> Created at: 2024-07-19 09:35:18 UTC  
> Updated at: 2024-07-19 09:35:18 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/139  

The header `checked_result_operations.hpp` calls `std::terminate` which is in `<exception>` but the latter is not included, causing compilation failures with clang 18 and libc++:  
  
```  
include/boost/safe_numerics/checked_result_operations.hpp:40:14: error: no member named 'terminate' in namespace 'std'; did you mean 'template'?  
   40 |         std::terminate();  
      |         ~~~~~^~~~~~~~~  
      |              template  
```
