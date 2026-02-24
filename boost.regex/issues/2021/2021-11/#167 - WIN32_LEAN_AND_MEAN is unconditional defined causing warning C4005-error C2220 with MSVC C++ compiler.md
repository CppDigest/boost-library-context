# #167 - WIN32_LEAN_AND_MEAN is unconditional defined causing warning C4005/error  C2220 with MSVC C++ compiler [Closed]

> Username: vbaderks  
> Created at: 2021-11-11 10:17:47 UTC  
> Updated at: 2022-12-02 17:07:28 UTC  
> Closed at: 2022-12-02 17:07:27 UTC  
> Url: https://github.com/boostorg/regex/issues/167  

The macro WIN32_LEAN_AND_MEAN is defined by a Boost include header:  
  
https://github.com/boostorg/regex/blob/a9f18c2c03a5e64745ec8fcdb8247dbb2a84e9df/include/boost/regex/v4/w32_regex_traits.hpp#L41  
  
This causes compiler C4005 warning (or an error if the option warnings are errors are enabled) when WIN32_LEAN_AND_MEAN is already globally defined: warning C4005: 'WIN32_LEAN_AND_MEAN': macro redefinition  
  
Many other Boost include files use the following pattern to prevent this:  
  
```  
#ifndef WIN32_LEAN_AND_MEAN  
#define WIN32_LEAN_AND_MEAN  
#define BOOST_REGEX_WIN32_LEAN_AND_MEAN  
#endif   //#ifndef WIN32_LEAN_AND_MEAN  
  
#include <windows.h>  
  
#ifdef BOOST_REGEX_WIN32_LEAN_AND_MEAN  
#undef WIN32_LEAN_AND_MEAN  
#undef BOOST_REGEX_WIN32_LEAN_AND_MEAN  
#endif   //#ifdef BOOST_REGEX_WIN32_LEAN_AND_MEAN  
  
```  
  
Note 1: We are using Boost 1.76 and in the latest regex/v5/w32_regex_traits.hpp file this issue is already resolved by removing the dependency on windows.h completely , which may be for v4 also a better solution.  
  
Note 2: The macro VC_EXTRALEAN is used by Microsoft ATL\MFC header files to define additional NO... macros. It is not used by windows.h itself. It could also be removed.  
https://github.com/boostorg/regex/blob/a9f18c2c03a5e64745ec8fcdb8247dbb2a84e9df/include/boost/regex/v4/w32_regex_traits.hpp#L40

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-12-02 17:07:27 UTC  
> Url: https://github.com/boostorg/regex/issues/167#issuecomment-1335547899  

This is now obsolete for the latest code as <windows.h> is no longer included.
