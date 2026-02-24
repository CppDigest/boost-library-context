# #186 Add reproducer for Clang < 15 bug with string_view & C++11 char types [Open]

> Username: Flamefire  
> Created at: 2025-01-04 11:49:17 UTC  
> Updated at: 2025-01-04 16:37:34 UTC  
> Url: https://github.com/boostorg/core/pull/186  

This is the test case for #185  to reproduce the issue such that it can be verified that it works. Made as a separate PR so CI runs and the state for the commit gets shown.  
  
As explained in #185 converting this string_view to a std string the bug is triggered too but cannot be fixed without removing the conversion to `std::basic_string_view`. Hence the condition to exclude it in Clang < 15

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-01-04 15:08:23 UTC  
> Url: https://github.com/boostorg/core/pull/186#issuecomment-2571329375  

I see no CI failures for Clang 13/14 here.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-01-04 16:37:33 UTC  
> Url: https://github.com/boostorg/core/pull/186#issuecomment-2571350686  

Seems using libstdc++ 12 and up requires Clang 15. Using libstdc++ 11 works also locally.  
  
I'd say the test still makes sense to find potential regressions with the unicode char types.     
And the workaround in #185 doesn't make anything worse:  `end() == data() + size()` which the `std::basic_string` ctor does too if `data(), size()` is passed. But it helps in one (even if now less relevant) case:

---
