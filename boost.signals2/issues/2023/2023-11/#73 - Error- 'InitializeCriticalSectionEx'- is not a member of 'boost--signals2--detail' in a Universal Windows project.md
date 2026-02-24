# #73 - Error: 'InitializeCriticalSectionEx': is not a member of 'boost::signals2::detail' in a Universal Windows project [Closed]

> Username: FrankHeimes  
> Created at: 2023-11-28 09:41:19 UTC  
> Updated at: 2024-05-31 13:59:37 UTC  
> Closed at: 2024-05-31 13:59:37 UTC  
> Url: https://github.com/boostorg/signals2/issues/73  

**Steps to reproduce:**  
Open the attached solution [BoostSignals2.zip](https://github.com/boostorg/signals2/files/13486108/BoostSignals2.zip) using Visual Studio and compile the project.  
  
```  
#include <boost/predef/platform.h>  
#include <boost/signals2.hpp>  
```  
  
This results in the following error  
_boost\signals2\detail\lwm_win32_cs.hpp(96,34): error C2039: 'InitializeCriticalSectionEx': is not a member of 'boost::signals2::detail'_  
  
Building on Windows 10 Enterprise LTSC (1809), OS build 17763.5122  
Using Visual Studio 2022, 17.7.6  
for WindowsTargetPlatformVersion = 10.0.22621.0  
    WindowsTargetPlatformMinVersion = 10.0.17134.0  
  
**Diagnosis:**  
The problem first appeared when I included `<boost/regex.hpp>` before including `<boost/signals2.hpp>`.  
The former indirectly includes the deprecated header `<boost/predef/platform/windows_runtime.h>`  
That eventually sets `BOOST_PLAT_WINDOWS_RUNTIME = 1`  
  
This is the chain of includes for `regex.hpp` and `signals2.hpp`  
```  
  boost/regex.hpp  
   boost/regex/config.hpp  
    boost/predef.h  
     boost/predef/platform.h  
      boost/predef/platform/windows_runtime.h  
  
  boost/signals2.hpp  
   boost/signals2/deconstruct.hpp  
    boost/signals2/mutex.hpp  
     boost/signals2/detail/lwm_win32_cs.hpp  
```  
The following change is to blame: [Adapt changes from smart_ptr commit id](https://github.com/boostorg/signals2/commit/2ecf1b53bc970dd2b5e5d0f36fe8adf5d2181638)  
It removed the declaration of `boost::signals2::detail::InitializeCriticalSectionEx` but failed to remove its use in line 96.  
  
**How to fix**  
- Do a partial rollback by recreating the declaration of `InitializeCriticalSectionEx`  
- Replace symbol `BOOST_PLAT_WINDOWS_RUNTIME` in line 96 with another symbol that is not deprecated  
- Remove lines 95 - 97  
  
I cannot assess the side effects of the aforementioned possible fixes.  
So I'd like to kindly ask [fmhess](https://github.com/boostorg/signals2/commits?author=fmhess) to pick one.  
  
Frank

---

## Comment 1

> Username: fmhess  
> Created at: 2023-11-28 15:51:20 UTC  
> Url: https://github.com/boostorg/signals2/issues/73#issuecomment-1830136195  

I've removed lines 95-97 on the develop branch.
