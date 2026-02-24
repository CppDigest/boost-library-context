# #66 - incompatibilty with ISOLATION_AWARE_ENABLED [Closed]

> Username: treh  
> Created at: 2018-01-03 16:29:55 UTC  
> Updated at: 2018-01-03 17:08:30 UTC  
> Closed at: 2018-01-03 17:08:30 UTC  
> Url: https://github.com/boostorg/winapi/issues/66  

if a project is compiled with `ISOLATION_AWARE_ENABLED`, some declarations in boost.winapi are incompatible with `<windows.h>`  
  
```  
#include <windows.h>  
#include <boost/winapi/dll.hpp>  
```  
yields  
```  
[...]\boost\winapi\dll.hpp(38): error C2375: 'IsolationAwareLoadLibraryA': redefinition; different linkage  
[...]\winbase.inl(287): note: see declaration of 'IsolationAwareLoadLibraryA'  
```  
because under `ISOLATION_AWARE_ENABLED`,  the functions are `inline` and not `__declspec(dllimport)`

---

## Comment 1

> Username: Lastique  
> Created at: 2018-01-03 17:08:30 UTC  
> Url: https://github.com/boostorg/winapi/issues/66#issuecomment-355067274  

I think if you want to use the isolation feature then you should use the SDK functions that actually come from SDK. As such, you should define `BOOST_USE_WINDOWS_H` when compiling your project.
