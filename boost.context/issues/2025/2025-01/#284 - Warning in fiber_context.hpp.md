# #284 - Warning in fiber_context.hpp [Closed]

> Username: anarthal  
> Created at: 2025-01-24 11:53:49 UTC  
> Updated at: 2025-01-24 16:05:45 UTC  
> Closed at: 2025-01-24 16:05:45 UTC  
> Url: https://github.com/boostorg/context/issues/284  

Most compilers now issue this warning:  
  
```  
.\boost/context/fiber_fcontext.hpp(374): warning C4101: 'exstate': unreferenced local variable  
```  
  
Can be fixed by `static_cast<void>(exstate)` or using `boost::ignore_unused`.
