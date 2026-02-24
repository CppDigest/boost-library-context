# #76 - Compiling with MSVC for ARM64 emits warning C4302 (truncation from 'void *const ' to 'long') [Closed]

> Username: wyattoday  
> Created at: 2019-05-31 13:40:41 UTC  
> Updated at: 2019-05-31 14:35:52 UTC  
> Closed at: 2019-05-31 14:35:52 UTC  
> Url: https://github.com/boostorg/winapi/issues/76  

When compiling with the latest Visual Studio (2019 -- 16.1.1) and targeting the app for ARM64, a warning is emitted when using a `boost::mutex` instance. Namely, here's the warning:  
  
```  
boost\thread\win32\basic_timed_mutex.hpp(271,1): warning C4302:  'type cast': truncation from 'void *const ' to 'long'  
```  
  
The fix is in `boost/detail/interlocked.hpp`. Namely, on line 73 in interlocked.hpp, change this:  
  
```  
# if defined(_M_IA64) || defined(_M_AMD64) || defined(__x86_64__) || defined(__x86_64)  
```  
  
  
To this:  
  
```  
# if defined(_M_IX86) || defined(_M_IA64) || defined(_M_AMD64) || defined(__x86_64__) || defined(__x86_64) || defined(_M_ARM) || defined(_M_ARM64)  
```  
  
Note that `_M_IX86`, `_M_ARM`, and `_M_ARM64` are all checked and use intrinsics. This follows the documentation from Microsoft: **[_InterlockedCompareExchangePointer Intrinsic Functions](https://docs.microsoft.com/en-us/cpp/intrinsics/interlockedcompareexchangepointer-intrinsic-functions?view=vs-2019)**  
  
  
This change both fixes the warning for ARM64 apps, as well as uses the available and documented intrinsics for x86 apps.  
  
  
Originally reported over here: https://github.com/boostorg/thread/issues/285
