# #20 Remove unused bad include [Merged]

> Username: MarcelRaad  
> Created at: 2016-07-14 09:37:30 UTC  
> Updated at: 2016-07-14 20:06:17 UTC  
> Merged at: 2016-07-14 18:46:33 UTC  
> Closed at: 2016-07-14 18:46:33 UTC  
> Url: https://github.com/boostorg/algorithm/pull/20  

boost/exception/errinfo_errno.hpp, included in boost/exception/all.hpp, pushes the warning level to 1 on MSVC, but emits a level 1 warning with /sdl. Only exception.hpp and info.hpp are necessary.

---
