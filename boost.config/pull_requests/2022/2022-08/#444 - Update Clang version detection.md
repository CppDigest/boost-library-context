# #444 Update Clang version detection [Merged]

> Username: pdimov  
> Created at: 2022-08-29 17:20:51 UTC  
> Updated at: 2022-11-20 17:45:58 UTC  
> Merged at: 2022-08-29 20:20:50 UTC  
> Closed at: 2022-08-29 20:20:50 UTC  
> Url: https://github.com/boostorg/config/pull/444  

1. Use `__clang_patchlevel__ % 100`, because `__clang_patchlevel__` is sometimes higher than 100 and the version comes out incorrect. (Reported in https://github.com/boostorg/assert/issues/28.)  
2. Update Apple Clang version detection for XCode 13.3, 13.4, 14.0 from https://en.wikipedia.org/wiki/Xcode#Toolchain_versions.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-08-29 17:44:43 UTC  
> Url: https://github.com/boostorg/config/pull/444#issuecomment-1230644297  

Looks good to me, one failure so far is a spurious clone-failure, please go ahead and merge when you're happy!

---
