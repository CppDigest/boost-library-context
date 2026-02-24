# #129 boostorg#123: Fixes for calling convention collision errors when compiling with /clr [Closed]

> Username: millerrt3  
> Created at: 2019-07-16 20:56:38 UTC  
> Updated at: 2019-07-16 21:02:41 UTC  
> Closed at: 2019-07-16 20:59:05 UTC  
> Url: https://github.com/boostorg/type_traits/pull/129  

- Changed `__CLR_VER` to `_MANAGED` in accordance with [Microsoft's guidance on `/clr` detection](https://docs.microsoft.com/en-us/cpp/dotnet/how-to-detect-clr-compilation?view=vs-2019)  
- Modified preprocessor conditional checks to more explicitly control the effect on `/clr` compilation on enabled calling conventions (`__clrcall` will be the enabled if and only if `_MANAGED` is set; standard logic will be used in all other cases)  
  
I've tested these changes using MSVC to build both 32- and 64-bit projects with and without the `/clr` flag enabled.

---

## Comment 1

> Username: millerrt3  
> Created_at: 2019-07-16 20:59:05 UTC  
> Url: https://github.com/boostorg/type_traits/pull/129#issuecomment-511984332  

Fix was mistakenly applied to 'master' branch rather than 'develop'; aborting this PR to migrate changes to the correct branch and resubmit

---
