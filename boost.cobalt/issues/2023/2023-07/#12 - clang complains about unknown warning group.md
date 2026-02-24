# #12 - clang complains about unknown warning group [Closed]

> Username: anarthal  
> Created at: 2023-07-10 16:22:48 UTC  
> Updated at: 2023-07-28 04:52:56 UTC  
> Closed at: 2023-07-28 04:52:56 UTC  
> Url: https://github.com/boostorg/cobalt/issues/12  

```  
[build] In file included from /home/ruben/workspace/async/include/boost/async/io/resolver.hpp:11:  
[build] /home/ruben/workspace/async/include/boost/async/io/endpoint.hpp:35:32: warning: unknown warning group '-Wsubobject-linkage', ignored [-Wunknown-warning-option]  
[build] #pragma GCC diagnostic ignored "-Wsubobject-linkage"  
```  
  
Note that clang may define `__GNUC__`
