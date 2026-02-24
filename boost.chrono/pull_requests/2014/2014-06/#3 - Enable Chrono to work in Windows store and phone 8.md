# #3 [winrt support] Enable Chrono to work in Windows store and phone 8 [Merged]

> Username: stgates  
> Created at: 2014-06-11 02:59:52 UTC  
> Updated at: 2014-06-12 17:18:32 UTC  
> Merged at: 2014-06-12 16:51:54 UTC  
> Closed at: 2014-06-12 16:51:54 UTC  
> Url: https://github.com/boostorg/chrono/pull/3  

All the changes are around avoiding APIs that are unavailable in the Windows Runtime. Some of the APIs have no replacements so the feature is disabled. For process clocks only 'real cpu' is enabled and thread clocks are disabled.  
  
These changes have no affect in terms of functionality for Windows desktop.

---

## Comment 1

> Username: viboes  
> Created_at: 2014-06-11 05:47:10 UTC  
> Updated_at: 2014-06-11 06:10:50 UTC  
> Url: https://github.com/boostorg/chrono/pull/3#issuecomment-45704224  

Where BOOST_WINAPI_FAMILY or BOOST_WINDOWS_RUNTIME is defined?  
  
Forget this. I see that it has been renamed BOOST_PLAT_WINDOWS_DESKTOP.

---

## Comment 2

> Username: viboes  
> Created_at: 2014-06-11 06:10:05 UTC  
> Updated_at: 2014-06-11 06:11:22 UTC  
> Url: https://github.com/boostorg/chrono/pull/3#issuecomment-45705465  

I've extracted the stopwatches to a separated module. Please, could you merge the develop branch.

---

## Comment 3

> Username: stgates  
> Created_at: 2014-06-11 07:18:01 UTC  
> Url: https://github.com/boostorg/chrono/pull/3#issuecomment-45709485  

Hi Vicente,  
  
I already did a merge and I double checked the stopwatches, the files look correct containing my changes.  
  
Thanks,  
Steve

---
