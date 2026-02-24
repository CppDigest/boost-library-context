# #35 - Warnings on msvc-14 [Closed]

> Username: vinniefalco  
> Created at: 2018-12-20 03:33:20 UTC  
> Updated at: 2018-12-20 14:54:28 UTC  
> Closed at: 2018-12-20 14:54:28 UTC  
> Url: https://github.com/boostorg/coroutine/issues/35  

Still getting these:  
```  
C:\projects\boost-root\boost/coroutine/detail/coroutine_context.hpp(43): warning C4251: 'boost::coroutines::detail::coroutine_context::palloc_': struct 'boost::coroutines::detail::preallocated' needs to have dll-interface to be used by clients of class 'boost::coroutines::detail::coroutine_context'  
C:\projects\boost-root\boost/coroutine/detail/preallocated.hpp(25): note: see declaration of 'boost::coroutines::detail::preallocated'  
```
