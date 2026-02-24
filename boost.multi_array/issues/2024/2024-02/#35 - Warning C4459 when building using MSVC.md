# #35 - Warning C4459 when building using MSVC [Open]

> Username: mikekaganski  
> Created at: 2024-02-23 08:59:52 UTC  
> Updated at: 2026-01-27 07:49:50 UTC  
> Url: https://github.com/boostorg/multi_array/issues/35  

Using multi_array, MSVC (VS 2019, VS 2022) emits the following warnings (using Boost release 1.82.0, but current code in the repo includes the same global variable and argument name):  
  
`C:\lo\build\workdir\UnpackedTarball\boost\boost/multi_array/multi_array_ref.hpp(615): warning C4459: declaration of 'extents' hides global declaration  
  
 C:\lo\build\workdir\UnpackedTarball\boost\boost/multi_array/base.hpp(69): note: see declaration of 'boost::`anonymous-namespace'::extents'`  
  
This prevents warning-free builds, or requires pollution of the code with pragmas. Would it be possible to rename the constructor argument name?

---

## Comment 1

> Username: glenfe  
> Created at: 2024-02-23 11:00:30 UTC  
> Url: https://github.com/boostorg/multi_array/issues/35#issuecomment-1961123578  

@mikekaganski  Yes, for the 1.86 release (not the upcoming 1.85 release).

---

## Comment 2

> Username: brandl-muc  
> Created at: 2026-01-27 07:49:50 UTC  
> Url: https://github.com/boostorg/multi_array/issues/35#issuecomment-3803640265  

This is still present in Boost 1.89.0, any chance this can make it soon?  
The proposed fix seems really minor, I'd be willing to supply a PR if desired.  
(in another Boost project a similar issue was fixed with Copilot in minutes, do you have access to that, too?)
