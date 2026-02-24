# #37 Fix CryptReleaseContext signature for Windows 2000 (#11896) [Closed]

> Username: gjasny  
> Created at: 2017-01-16 07:59:17 UTC  
> Updated at: 2017-01-16 22:00:12 UTC  
> Closed at: 2017-01-16 21:40:28 UTC  
> Url: https://github.com/boostorg/winapi/pull/37  

See [#11896](https://svn.boost.org/trac/boost/ticket/11896).

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-01-16 11:00:02 UTC  
> Url: https://github.com/boostorg/winapi/pull/37#issuecomment-272831702  

The ticket says the problem is specific to VS2012, yet the preprocessor check does not reflect that.  
  
Also, are you using Windows SDK that comes with the compiler?

---

## Comment 2

> Username: gjasny  
> Created_at: 2017-01-16 13:35:49 UTC  
> Updated_at: 2017-01-16 13:36:05 UTC  
> Url: https://github.com/boostorg/winapi/pull/37#issuecomment-272863327  

I does not have anything to do with VS version, just the `_WIN32_WINNT` setting matters. I created the simple test case with VS2013:  
  
```  
#define _WIN32_WINNT 0x0500  
#include <windows.h>  
#include <boost/uuid/uuid.hpp>  
#include <boost/uuid/uuid_generators.hpp>   
```  
  
When I change the value from Win2000 / 0x0500 to XP / 0x0501 the error vanished. You can see in the Windows SDK that the function signature for `CryptReleaseContext` changes between 2000 and XP. So my patch just reflects that in the Boost winapi header as well.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-01-16 21:40:28 UTC  
> Url: https://github.com/boostorg/winapi/pull/37#issuecomment-272968254  

Should be fixed in https://github.com/boostorg/winapi/commit/d1ce3b2d3ff45f2958c43d0a8d4296995aca053d.

---

## Comment 4

> Username: gjasny  
> Created_at: 2017-01-16 22:00:12 UTC  
> Url: https://github.com/boostorg/winapi/pull/37#issuecomment-272971667  

Thank you for taking care. I guess you did not image going down that rabbit hole 👍

---
