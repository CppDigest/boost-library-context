# #98 Add missing ifdef check. [Merged]

> Username: egorpugin  
> Created at: 2022-10-10 19:27:36 UTC  
> Updated at: 2022-10-10 23:55:52 UTC  
> Merged at: 2022-10-10 23:55:52 UTC  
> Closed at: 2022-10-10 23:55:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/98  

Unreal Engine 5 gives following error.  
```  
boost\smart_ptr\intrusive_ptr.hpp(248): error C4668: 'GNUC' is not defined as a preprocessor macro, replacing with '0' for '#if/#elif'  
```

---
