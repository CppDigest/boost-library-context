# #334 - boost/progress.hpp is deprecated [Closed]

> Username: daankolthof  
> Created at: 2023-04-23 17:52:12 UTC  
> Updated at: 2023-04-30 21:17:33 UTC  
> Closed at: 2023-04-30 21:17:33 UTC  
> Url: https://github.com/boostorg/graph/issues/334  

From the build output:  
```  
In file included from ../example/fr_layout.cpp:19:0:  
  
../../../boost/progress.hpp:23:3: error: #error This header is deprecated and will be removed. (You can define BOOST_TIMER_ENABLE_DEPRECATED to suppress this error.)  
  
error This header is deprecated and will be removed. (You can define BOOST_TIMER_ENABLE_DEPRECATED to suppress this error.)  
```  
  
Fixed in https://github.com/boostorg/graph/pull/335.
