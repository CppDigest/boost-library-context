# #535 Add support for std::format. [Open]

> Username: jzmaddock  
> Created at: 2023-02-19 12:19:36 UTC  
> Updated at: 2024-02-29 17:03:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/535  

This is a tentative first step towards supporting C++20's std::format.  
  
Integers are all working and feature complete other than the usual undiscovered bugs and corner cases.  
  
Other number types will require some refactoring of the code in format.hpp, though exactly how/what remains to be seen.  
  
This also ties in with https://github.com/boostorg/multiprecision/issues/297 as it adds binary string formatting.  
  
Currently only latest msvc supports `<format>`, for gcc we will have to wait for gcc-13 later in the year, likewise clang/libc++.  
  
Comments welcome as always.

---

## Comment 1

> Username: DouisLavid  
> Created_at: 2024-02-29 16:42:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/535#issuecomment-1971534684  

Hello,  
Thanks for the awesome work in Boost.Math :)  
I was wondering if there was any plan to merge this PR in the foreseeable future, as it is a feature that would be quite useful for me. If not, would you say that there is any reason that would make me *not* want to use your changes locally ? (I'm ok with the "usual undiscovered bugs and corner cases" as nothing display related is critical in my use case, and i'm ok with breaking changes when the feature gets officially supported in the future)

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-02-29 17:03:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/535#issuecomment-1971578020  

Please do go ahead and use this and report back: we need to revisit this now that latest GCC supports std::format and finish this support off.

---
