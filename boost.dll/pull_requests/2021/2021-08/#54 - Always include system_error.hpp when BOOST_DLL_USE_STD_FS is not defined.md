# #54 Always include system_error.hpp when BOOST_DLL_USE_STD_FS is not defined [Merged]

> Username: Naios  
> Created at: 2021-08-13 23:13:51 UTC  
> Updated at: 2021-09-10 16:31:27 UTC  
> Merged at: 2021-09-10 16:31:17 UTC  
> Closed at: 2021-09-10 16:31:17 UTC  
> Url: https://github.com/boostorg/dll/pull/54  

> include\boost/dll/config.hpp(68,22): error C2039: 'system_error': is not a member of 'boost::system' (compiling source file ...)  
  
It seems like boost DLL causes an error due to a missing inclusion in the current boost 1.77 release when BOOST_DLL_USE_STD_FS is not defined (and system_error.hpp is not included already in the compilation unit).  
This PR fixes the inclusion under those circumstances so that system_error is always included before use.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-09-10 16:31:27 UTC  
> Url: https://github.com/boostorg/dll/pull/54#issuecomment-917040922  

Many thanks for the fix!

---
