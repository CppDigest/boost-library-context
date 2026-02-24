# #277 - -Wundef warning in boost/interprocess/detail/workaround.hpp [Closed]

> Username: pdimov  
> Created at: 2026-01-04 23:36:19 UTC  
> Updated at: 2026-01-05 01:10:02 UTC  
> Closed at: 2026-01-05 01:10:02 UTC  
> Url: https://github.com/boostorg/interprocess/issues/277  

One more, sorry:  
  
```  
In file included from ./boost/interprocess/allocators/allocator.hpp:23:  
./boost/interprocess/detail/workaround.hpp:89:9: error: '_XOPEN_SOURCE' is not defined, evaluates to 0 [-Werror,-Wundef]  
   89 |    #if (_XOPEN_SOURCE >= 700 || _POSIX_C_SOURCE >= 200809L)  
      |         ^  
```  
  
This one only appears on macOS.  
  
https://github.com/boostorg/unordered/actions/runs/20694312818/job/59416261269#step:12:1457
