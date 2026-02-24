# #25 - Build fails on Windows [Closed]

> Username: abdes  
> Created at: 2018-05-22 16:07:58 UTC  
> Updated at: 2022-09-17 20:59:19 UTC  
> Closed at: 2022-09-17 20:59:19 UTC  
> Url: https://github.com/boostorg/locale/issues/25  

In ``https://github.com/boostorg/locale/blob/develop/src/win32/lcid.cpp``  
  
use of boost::unique_lock without including <boost/thread/lock_types.hpp> makes the compilation fail.  
  
Visual Studio 2017  
boost::locale latest (boost 1.67.0)

---

## Comment 1

> Username: lc-Arvin  
> Created at: 2018-11-25 09:35:40 UTC  
> Url: https://github.com/boostorg/locale/issues/25#issuecomment-441427342  

actually https://github.com/boostorg/locale/pull/30/commits will fix this this issue.

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-09-17 20:59:19 UTC  
> Url: https://github.com/boostorg/locale/issues/25#issuecomment-1250139626  

Fixed by #85
