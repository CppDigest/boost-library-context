# #47 - Unreal Engine 5 build error [Closed]

> Username: egorpugin  
> Created at: 2022-11-14 22:36:38 UTC  
> Updated at: 2022-11-26 00:42:54 UTC  
> Closed at: 2022-11-26 00:42:54 UTC  
> Url: https://github.com/boostorg/exception/issues/47  

https://github.com/boostorg/exception/blob/develop/include/boost/exception/detail/exception_ptr.hpp#L29  
  
All lines with GNUC ifdef like this fails with error:  
```  
boost\exception\exception.hpp(22): error C4668: '__GNUC__' is not defined as a preprocessor macro, replacing with '0' for '#if/#elif'  
boost\exception\exception.hpp(22): error C4668: '__GNUC_MINOR__' is not defined as a preprocessor macro, replacing with '0' for '#if/#elif'  
```  
  
Possible fix - https://github.com/boostorg/smart_ptr/pull/98  
Such change should be implemented with all occurences of `__GNUC__`  
  
Probably this repo is also affected.  
https://github.com/boostorg/throw_exception

---

## Comment 1

> Username: zajo  
> Created at: 2022-11-14 23:17:07 UTC  
> Url: https://github.com/boostorg/exception/issues/47#issuecomment-1314533889  

This is likely due to excessive warnings level. It is standard and correct behavior that undefined macros used in preprocessor expressions evaluate to 0.
