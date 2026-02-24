# #157 Reduction of included Windows SDK headers [Merged]

> Username: mabrarov  
> Created at: 2024-03-11 19:10:00 UTC  
> Updated at: 2024-04-16 08:48:06 UTC  
> Merged at: 2024-04-16 08:47:28 UTC  
> Closed at: 2024-04-16 08:47:29 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/157  

Avoid inclusion of rarely used Windows SDK headers which can cause conflict with other code using Windows SDK.  
  
Refer to issue #155 for example of conflict with Boost.Asio caused by Winsock 1.x header (winsock.h) being included through windows.h in Boost.Stacktrace and conflicting with Winsock 2 header (winsock2.h). Refer to https://github.com/chriskohlhoff/asio/issues/1441 for details.

---

## Review 1 [Commented]

> Username: mabrarov  
> Created_at: 2024-03-11 19:18:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/157#pullrequestreview-1928916896  

📁 include/boost/stacktrace/detail/frame_msvc.ipp

```diff
  30 |+ #endif
  31 |+ 
  32 | #include "dbgeng.h"
```

> Username: mabrarov  
> Created_at: 2024-03-11 19:18:29 UTC  
> Updated_at: 2024-03-11 19:19:16 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/157#discussion_r1520304497  

Even dbgeng.h includes windows.h:  
  
dbgeng.h -> objbase.h -> rpc.h:  
  
```cpp  
#if !defined( RPC_NO_WINDOWS_H ) && !defined( MAC ) && !defined( _MAC ) && !defined(_KRPCENV_)  
/*  
 * Pull in WINDOWS.H if necessary  
 */  
#ifndef _INC_WINDOWS  
#include <windows.h>  
#endif /* _INC_WINDOWS */  
#endif // RPC_NO_WINDOWS_H  
```  
  
So it's hard to include only needed Windows SDK headers without inclusion of windows.h.


---

## Review 2 [Commented]

> Username: mabrarov  
> Created_at: 2024-03-11 19:45:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/157#pullrequestreview-1929024506  

📁 include/boost/stacktrace/detail/frame_msvc.ipp

```diff
  25 |+ // Prevent inclusion of extra Windows SDK headers which can cause conflict
  26 |+ // with other code using Windows SDK
  27 |+ #define WIN32_LEAN_AND_MEAN
```

> Username: mabrarov  
> Created_at: 2024-03-11 19:45:47 UTC  
> Updated_at: 2024-03-11 19:51:20 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/157#discussion_r1520343497  

The downside of this solution is following:  
  
If both conditions are met:  
  
1. Boost.Stacktrace user's compilation unit includes windows.h header file after inclusion of Boost.Stacktrace header files.  
1. Boost.Stacktrace user's compilation unit doesn't have WIN32_LEAN_AND_MEAN macro defined (IMHO, it is a rare case).  
   
then the compilation unit loses "extra" Windows SDK headers (cderr.h, dde.h, ddeml.h, dlgs.h, lzexpand.h, mmsystem.h, nb30.h, shellapi.h, winperf.h, winsock.h, wincrypt.h, winefs.h, winscard.h, winspool.h, ole.h or ole2.h, commdlg.h), because windows.h header is not going to be included one more time into the same compilation unit, i.e. this solution potentially can cause regression for the users of Boost.Stacktrace, but that regression can be solved by including "extra" Windows SDK header files explicitly.


---

## Comment 3

> Username: apolukhin  
> Created_at: 2024-04-16 08:48:04 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/157#issuecomment-2058568149  

Many thanks for the workaround!

---
