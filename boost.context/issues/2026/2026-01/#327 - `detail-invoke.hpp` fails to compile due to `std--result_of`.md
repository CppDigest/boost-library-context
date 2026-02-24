# #327 - `detail/invoke.hpp` fails to compile due to `std::result_of` [Open]

> Username: vittorioromeo  
> Created at: 2026-01-29 08:54:42 UTC  
> Updated at: 2026-01-29 08:54:42 UTC  
> Url: https://github.com/boostorg/context/issues/327  

```  
external/boost.context+/include/boost/context/detail/invoke.hpp:29:19: error: no type named 'result_of' in namespace 'std'  
   29 |     typename std::result_of< Fn &&( Args && ... ) >::type  
      |     ~~~~~~~~~~~~~~^~~~~~~~~  
external/boost.context+/include/boost/context/detail/invoke.hpp:30:1: error: expected unqualified-id  
   30 | >::type  
      | ^  
external/boost.context+/include/boost/context/detail/invoke.hpp:38:19: error: no type named 'result_of' in namespace 'std'  
   38 |     typename std::result_of< Fn &&( Args && ... ) >::type  
      |     ~~~~~~~~~~~~~~^~~~~~~~~  
external/boost.context+/include/boost/context/detail/invoke.hpp:39:1: error: expected unqualified-id  
   39 | >::type  
      | ^  
```
