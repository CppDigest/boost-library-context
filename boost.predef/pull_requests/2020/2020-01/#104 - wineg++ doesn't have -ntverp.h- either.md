# #104 wineg++ doesn't have <ntverp.h> either [Merged]

> Username: puetzk  
> Created at: 2020-01-30 05:30:27 UTC  
> Updated at: 2020-03-01 02:59:30 UTC  
> Merged at: 2020-03-01 02:59:30 UTC  
> Closed at: 2020-03-01 02:59:30 UTC  
> Url: https://github.com/boostorg/predef/pull/104  

Upon upgrading to from 1.67.0 to boost 1.72.0, I just discovered some change has broken builds for us, albiet on a fairly exotic platform (using spirit::qi in a [winelib](https://wiki.winehq.org/Winelib) project)  
  
I haven't figured out exactly which change caused us to start hitting it since this code exists in 1.67.0 as well, but the failure is that that shared_ptr is unconditionally dragging in <boost/predef/platform/windows_runtime>, and this leads to issues because wine (like mingw, they shared a lot of the headers) doesn't provide an `<ntverp.h>`  
  
> In file included from .../include/boost/predef/platform/windows_phone.h:14:0,  
                 from .../include/boost/predef/platform/windows_runtime.h:14,  
                 from .../include/boost/smart_ptr/detail/yield_k.hpp:28,  
                 from .../include/boost/smart_ptr/detail/spinlock_std_atomic.hpp:18,  
                 from .../include/boost/smart_ptr/detail/spinlock.hpp:47,  
                 from .../include/boost/smart_ptr/detail/spinlock_pool.hpp:25,  
                 from .../include/boost/smart_ptr/shared_ptr.hpp:36,  
                 from .../include/boost/shared_ptr.hpp:17,  
.../include/boost/predef/platform/windows_uwp.h:36:23: fatal error: ntverp.h: No such file or directory  
  
I believe the solution is just to treat wine the same as mingw32, as their win32 headers are very similar (with changes routinely merged both ways).

---
