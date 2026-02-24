# #975 - (README) QuickLook contradicts library documentation on header-only usage [Closed]

> Username: daniel-canario  
> Created at: 2026-02-12 16:14:52 UTC  
> Updated at: 2026-02-20 02:42:25 UTC  
> Closed at: 2026-02-20 02:42:25 UTC  
> Url: https://github.com/boostorg/url/issues/975  

The [QuickLook section of the README](https://github.com/boostorg/url#quick-look) states:  
  
> Boost.URL is a compiled library. You need to install binaries in a location that can be found by your linker and link your program with the Boost.URL built library  
  
The[ library documentation ](https://www.boost.org/library/latest/url/)states:  
  
> To use as header-only; that is, to eliminate the requirement to link a program to a static or dynamic Boost.URL library, simply place the following line in exactly one new or existing source file in your project.  
> `#include <boost/url/src.hpp>`  
  
These are mutually exclusive statements.  
  
I would gladly submit a pull request to fix this, although some guidance would be appreciated.   
  
It seems to me that the README should be from the same source of truth as the library documentation, although that evidently cannot be the case.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2026-02-12 16:18:13 UTC  
> Url: https://github.com/boostorg/url/issues/975#issuecomment-3891888836  

Oh... The website get that from:  
  
https://github.com/boostorg/url/blob/develop/doc/library-detail.adoc  
  
instead of README.adoc.  
  
Thank you for letting us know
