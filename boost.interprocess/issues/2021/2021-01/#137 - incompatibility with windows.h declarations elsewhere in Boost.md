# #137 - incompatibility with windows.h declarations elsewhere in Boost [Closed]

> Username: slymz  
> Created at: 2021-01-31 16:54:51 UTC  
> Updated at: 2021-02-02 05:11:13 UTC  
> Closed at: 2021-02-02 05:11:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/137  

The following two liner:  
  
```  
#include <boost/interprocess/ipc/message_queue.hpp>  
#include <boost/thread/thread.hpp>  
```  
(See https://godbolt.org/z/jvahcn)  
  
is a compilation error with MSVC compilers in `/permissive-` mode.  
  
```  
c:/data/libraries/installed/x64-windows/include\boost/winapi/semaphore.hpp(25): error C2116: 'boost::interprocess::winapi::CreateSemaphoreA': function parameter lists do not match between declarations  
C:/data/libraries/installed/x64-windows/include\boost/interprocess/detail/win32_api.hpp(925): note: see declaration of 'boost::interprocess::winapi::CreateSemaphoreA'  
C:/data/libraries/installed/x64-windows/include\boost/winapi/semaphore.hpp(25): error C2733: 'CreateSemaphoreA': you cannot overload a function with 'extern "C"' linkage  
C:/data/libraries/installed/x64-windows/include\boost/interprocess/detail/win32_api.hpp(925): note: see declaration of 'boost::interprocess::winapi::CreateSemaphoreA'  
C:/data/libraries/installed/x64-windows/include\boost/winapi/system.hpp(31): error C2116: 'boost::interprocess::winapi::GetSystemInfo': function parameter lists do not match between declarations  
C:/data/libraries/installed/x64-windows/include\boost/interprocess/detail/win32_api.hpp(932): note: see declaration of 'boost::interprocess::winapi::GetSystemInfo'  
C:/data/libraries/installed/x64-windows/include\boost/winapi/system.hpp(31): error C2733: 'GetSystemInfo': you cannot overload a function with 'extern "C"' linkage  
C:/data/libraries/installed/x64-windows/include\boost/interprocess/detail/win32_api.hpp(932): note: see declaration of 'boost::interprocess::winapi::GetSystemInfo'  
Compiler returned: 2  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-01 14:00:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/137#issuecomment-770877192  

Which boost version are you using? Boost 1.74 uses winapi, which should solve this issue.

---

## Comment 2

> Username: slymz  
> Created at: 2021-02-02 05:11:03 UTC  
> Updated at: 2021-02-02 05:11:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/137#issuecomment-771366341  

Duplicate of #99
