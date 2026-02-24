# #93 - `is_trivial` is deprecated in C++26 [Closed]

> Username: pdimov  
> Created at: 2025-06-08 23:26:04 UTC  
> Updated at: 2025-06-09 00:16:10 UTC  
> Closed at: 2025-06-09 00:16:10 UTC  
> Url: https://github.com/boostorg/iterator/issues/93  

```  
./boost/iterator/iterator_facade.hpp:378:18: error: 'template<class _Tp> struct std::is_trivial' is deprecated: use 'is_trivially_default_constructible && is_trivially_copyable' instead [-Werror=deprecated-declarations]  
  378 |             std::is_trivial< ValueType >,  
      |                  ^~~~~~~~~~  
```
