# #92 Fixed compilation with MSVC-8. [Merged]

> Username: Lastique  
> Created at: 2016-07-16 16:58:15 UTC  
> Updated at: 2016-08-10 07:52:17 UTC  
> Merged at: 2016-08-10 05:43:37 UTC  
> Closed at: 2016-08-10 05:43:37 UTC  
> Url: https://github.com/boostorg/thread/pull/92  

Make sure _WIN32_WINNT is defined when windows.h is included. This is  
achieved by (a) making sure boost/detail/winapi/config.hpp is included  
before any other headers and (b) BOOST_USE_WINDOWS_H is defined so that  
the header defines _WIN32_WINNT based on the default target Windows  
version. This ensures that all APIs used by the implementation are  
available.  
  
Also extracted WIN32_LEAN_AND_MEAN definition to the Jamfile so that it is  
consistently defined for all translation units instead of only tss*.cpp.

---

## Comment 1

> Username: Lastique  
> Created_at: 2016-08-08 22:54:14 UTC  
> Url: https://github.com/boostorg/thread/pull/92#issuecomment-238402496  

It would be great if this is merged before the 1.62 release. Is there any problem with the patch?

---

## Comment 2

> Username: viboes  
> Created_at: 2016-08-10 05:43:57 UTC  
> Url: https://github.com/boostorg/thread/pull/92#issuecomment-238771259  

Thanks for working on this issue.

---
