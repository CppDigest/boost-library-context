# #574 Add macro to disable MSVC has_denorm deprecation warning [Closed]

> Username: mborland  
> Created at: 2023-12-06 11:14:10 UTC  
> Updated at: 2023-12-19 09:12:34 UTC  
> Closed at: 2023-12-08 11:28:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/574  

Closes: #573

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-12-07 09:33:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/574#issuecomment-1844997056  

Are we between a rock and a hard place here?  The problem with _SILENCE_CXX23_DENORM_DEPRECATION_WARNING is that it changes the std lib header and silences the warning everywhere including in user code.  We may also eventually have this issue with other std lib's as well.  
  
I wonder what the correct thing to do is here, members like has_denorm is still required to be present in C++23 but is "a candidate for removal" in a later std.  So we do need to define those members even in C++23 :(  I wonder if there any #pragma warning(disable:XXXX) that would work in this case?

---

## Comment 2

> Username: mborland  
> Created_at: 2023-12-07 09:43:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/574#issuecomment-1845013010  

> Are we between a rock and a hard place here? The problem with _SILENCE_CXX23_DENORM_DEPRECATION_WARNING is that it changes the std lib header and silences the warning everywhere including in user code. We may also eventually have this issue with other std lib's as well.  
>   
> I wonder what the correct thing to do is here, members like has_denorm is still required to be present in C++23 but is "a candidate for removal" in a later std. So we do need to define those members even in C++23 :( I wonder if there any #pragma warning(disable:XXXX) that would work in this case?  
  
We are between a rock and a hard place. I tried disabling the warning, and it still propagates: https://godbolt.org/z/bz1a54jf7

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-12-07 16:11:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/574#issuecomment-1845628081  

Shucks :(  
  
The macro doesn't work either if <limits> has already been included: https://godbolt.org/z/r6Pdn71We

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-12-07 16:17:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/574#issuecomment-1845636801  

The #pragma does seem to work in some situations and not others... will experiment.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2023-12-07 16:48:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/574#issuecomment-1845687196  

This seems to get it: https://github.com/boostorg/multiprecision/pull/575

---
