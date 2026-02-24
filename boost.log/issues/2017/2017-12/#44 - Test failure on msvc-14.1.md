# #44 - Test failure on msvc-14.1 [Closed]

> Username: pdimov  
> Created at: 2017-12-17 17:03:37 UTC  
> Updated at: 2017-12-17 18:21:41 UTC  
> Closed at: 2017-12-17 17:20:20 UTC  
> Url: https://github.com/boostorg/log/issues/44  

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

---

## Comment 1

> Username: Lastique  
> Created at: 2017-12-17 17:20:20 UTC  
> Url: https://github.com/boostorg/log/issues/44#issuecomment-352270977  

The errors are coming from Boost.Thread.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-12-17 17:22:58 UTC  
> Url: https://github.com/boostorg/log/issues/44#issuecomment-352271130  

Right, sorry. :-)

---

## Comment 3

> Username: pdimov  
> Created at: 2017-12-17 17:47:54 UTC  
> Url: https://github.com/boostorg/log/issues/44#issuecomment-352272711  

Thread's tests don't fail though. :-)

---

## Comment 4

> Username: Lastique  
> Created at: 2017-12-17 18:11:00 UTC  
> Url: https://github.com/boostorg/log/issues/44#issuecomment-352274278  

I think I found the problem. Boost.Thread accesses the symbols as `winapi::HeapAlloc`, which should normally resolve to `boost::winapi::HeapAlloc` or `boost::detail::winapi::HeapAlloc` (if the legacy Boost.WinAPI header is included). In Boost.Log, I had an outdated forward-declaration in `boost::detail::winapi` without including the legacy header, so the second name was used and there is no such symbol.  
  
I have fixed the forward-declaration in 525c53b1a735f21d9b51d432b93a79f4f8b73c6b, which should fix the problem. However, the fact that Boost.Thread is implemented directly in namespace `boost` and `boost::detail` will likely cause us more headache in the future.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-12-17 18:14:14 UTC  
> Url: https://github.com/boostorg/log/issues/44#issuecomment-352274461  

I should close the Thread issue then?

---

## Comment 6

> Username: Lastique  
> Created at: 2017-12-17 18:14:54 UTC  
> Url: https://github.com/boostorg/log/issues/44#issuecomment-352274499  

If it fixes the compilation, sure.

---

## Comment 7

> Username: Lastique  
> Created at: 2017-12-17 18:19:12 UTC  
> Url: https://github.com/boostorg/log/issues/44#issuecomment-352274747  

Is this problem critical enough to request inclusion in 1.66?

---

## Comment 8

> Username: pdimov  
> Created at: 2017-12-17 18:21:41 UTC  
> Url: https://github.com/boostorg/log/issues/44#issuecomment-352274890  

Yes, this fixes it. No, not critical at all. I just encountered it while testing the `boost-install` pull request as I run the tests before and after per Best Practices.
