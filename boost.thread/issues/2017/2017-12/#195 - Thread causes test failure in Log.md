# #195 - Thread causes test failure in Log [Closed]

> Username: pdimov  
> Created at: 2017-12-17 17:28:26 UTC  
> Updated at: 2018-02-26 15:58:17 UTC  
> Closed at: 2017-12-17 18:19:21 UTC  
> Url: https://github.com/boostorg/thread/issues/195  

```  
compile-c-c++ ..\..\bin.v2\libs\log\test\util_ipc_reliable_mq.test\msvc-14.1\deb  
ug\threadapi-win32\threading-multi\run\util_ipc_reliable_mq.obj  
util_ipc_reliable_mq.cpp  
C:\Projects\boost-git\boost\boost/thread/win32/thread_heap_alloc.hpp(25): error  
C2039: 'HeapAlloc': is not a member of 'boost::detail::winapi'  
C:\Projects\boost-git\boost\boost/log/utility/permissions.hpp(37): note: see dec  
laration of 'boost::detail::winapi'  
C:\Projects\boost-git\boost\boost/thread/win32/thread_heap_alloc.hpp(25): error  
C2039: 'GetProcessHeap': is not a member of 'boost::detail::winapi'  
C:\Projects\boost-git\boost\boost/log/utility/permissions.hpp(37): note: see dec  
laration of 'boost::detail::winapi'  
C:\Projects\boost-git\boost\boost/thread/win32/thread_heap_alloc.hpp(35): error  
C2039: 'HeapFree': is not a member of 'boost::detail::winapi'  
C:\Projects\boost-git\boost\boost/log/utility/permissions.hpp(37): note: see dec  
laration of 'boost::detail::winapi'  
C:\Projects\boost-git\boost\boost/thread/win32/thread_heap_alloc.hpp(35): error  
C2039: 'GetProcessHeap': is not a member of 'boost::detail::winapi'  
C:\Projects\boost-git\boost\boost/log/utility/permissions.hpp(37): note: see dec  
laration of 'boost::detail::winapi'  
```  
  
https://github.com/boostorg/log/issues/44

---

## Comment 1

> Username: pdimov  
> Created at: 2017-12-17 18:19:21 UTC  
> Url: https://github.com/boostorg/thread/issues/195#issuecomment-352274754  

The issue has been fixed on Log's side.
