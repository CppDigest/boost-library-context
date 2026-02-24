# #4 Use __has_attribute(unused) macro as a check [Closed]

> Username: Houndie  
> Created at: 2015-04-13 14:42:12 UTC  
> Updated at: 2015-04-13 19:07:58 UTC  
> Closed at: 2015-04-13 19:07:58 UTC  
> Url: https://github.com/boostorg/concept_check/pull/4  

While currently the gcc marketing version number is used to check for the **attribute**((unused)) feature of the language, clang has its "gcc compatibility version number" set to 4.2, causing warnings to appear when compiling with clang and -Wunused-local-typedefs  
  
Using the __has_attribute(unused) macro will satisfy the clang compiler, and is also supported in gcc starting with 5.0

---

## Comment 1

> Username: Houndie  
> Created_at: 2015-04-13 19:07:58 UTC  
> Url: https://github.com/boostorg/concept_check/pull/4#issuecomment-92464482  

Pulling this because I went too fast and this doesn't actually compile with GCC.  My bad.  
  
I'll be back with something that actually works.

---
