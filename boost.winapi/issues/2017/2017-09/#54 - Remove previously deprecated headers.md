# #54 - Remove previously deprecated headers [Closed]

> Username: jeking3  
> Created at: 2017-09-16 11:52:35 UTC  
> Updated at: 2017-09-16 18:09:00 UTC  
> Closed at: 2017-09-16 18:08:54 UTC  
> Url: https://github.com/boostorg/winapi/issues/54  

The following headers print a deprecation warning, and given they were already deprecated in a previous release, they can now be removed:  
  
```  
include/boost/detail/winapi/GetCurrentProcess.hpp  
include/boost/detail/winapi/GetCurrentThread.hpp  
include/boost/detail/winapi/GetLastError.hpp  
include/boost/detail/winapi/GetProcessTimes.hpp  
include/boost/detail/winapi/GetThreadTimes.hpp  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2017-09-16 18:09:00 UTC  
> Url: https://github.com/boostorg/winapi/issues/54#issuecomment-329985475  

Done.
