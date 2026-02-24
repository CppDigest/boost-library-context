# #92 - Fiber requirements fail when cross-compiling [Closed]

> Username: ClaymorePT  
> Created at: 2016-10-28 15:10:27 UTC  
> Updated at: 2016-10-28 21:20:15 UTC  
> Closed at: 2016-10-28 16:55:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/92  

If I try to cross-compile Boost.Fiber for the armv6 architecture, the cxx11_\* requirements in the Jamfile.v2 will fail, because the building system will try to execute the binaries on the host.  
  
This should not happen.   
The fact that the binaries are compiled is enough to guarantee the cxx11_\* support by the compiler.

---

## Comment 1

> Username: olk  
> Created at: 2016-10-28 16:55:38 UTC  
> Updated at: 2016-10-28 16:56:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/92#issuecomment-256972249  

this is a problem of boost.build - boost.build/boost.config evaluate/provide cxx11_*  
there is nothing boost.fiber can do

---

## Comment 2

> Username: ClaymorePT  
> Created at: 2016-10-28 21:20:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/92#issuecomment-257031465  

Oh! I'm sorry then. Thank you for the enlightment. :)  
  
Em 28/10/2016 17:55, "Oliver Kowalke" notifications@github.com escreveu:  
  
> Closed #92 https://github.com/boostorg/fiber/issues/92.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/fiber/issues/92#event-840324534, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/AHTtIWxnXD1j7xyTbok7Bu6NwBBqDG8Qks5q4ikLgaJpZM4Kjix_  
> .
