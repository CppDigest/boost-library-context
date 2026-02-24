# #131 Fix for sprintf deprecation warning [Merged]

> Username: mborland  
> Created at: 2022-12-06 04:37:22 UTC  
> Updated at: 2022-12-07 23:31:26 UTC  
> Merged at: 2022-12-07 23:31:25 UTC  
> Closed at: 2022-12-07 23:31:25 UTC  
> Url: https://github.com/boostorg/core/pull/131  

Use same method as https://github.com/boostorg/assert/commit/601ee4ba7ae9d9b532dab5f7e757f4187dce6300.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-12-06 04:38:40 UTC  
> Url: https://github.com/boostorg/core/pull/131#issuecomment-1338742318  

@pdimov Can you please approve the CI run since I am a first time contributor to this library? Thanks.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-12-06 09:17:16 UTC  
> Url: https://github.com/boostorg/core/pull/131#issuecomment-1339012682  

Why is the condition here not the same as the one in Assert?

---

## Comment 3

> Username: Lastique  
> Created_at: 2022-12-06 09:46:04 UTC  
> Url: https://github.com/boostorg/core/pull/131#issuecomment-1339047064  

I'd prefer a function instead of a macro, something like [this](https://github.com/boostorg/log/blob/3f22dbe131908e6d72c47c9d17b221ddae873bfc/include/boost/log/detail/snprintf.hpp). I can make a PR moving that header from Boost.Log to Boost.Core.  
  
And if it is a macro, at least make it private (i.e. `BOOST_CORE_DETAIL_SNPRINTF`).

---

## Comment 4

> Username: mborland  
> Created_at: 2022-12-06 15:56:26 UTC  
> Url: https://github.com/boostorg/core/pull/131#issuecomment-1339592006  

> Why is the condition here not the same as the one in Assert?  
  
I saw that you have C++98 and 03 in the ci.yaml file, and `snprintf` is from C++11. If it is superfluous I can remove it.

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-12-06 16:00:42 UTC  
> Url: https://github.com/boostorg/core/pull/131#issuecomment-1339597663  

I also have C++03 in Assert's CI.  
  
The condition is technically correct, as snprintf is C99 and therefore C++11, but checking `__cplusplus` like this is true for every MSVC, because they still define it to 199711L.  
  
Since the condition in Assert works, I assume that all supported compilers do provide `std::snprintf` even in C++98/C++03 mode.  
  
Also, you should `#undef` the macro afterwards, as it's done in Assert.

---

## Comment 6

> Username: mborland  
> Created_at: 2022-12-06 16:13:47 UTC  
> Url: https://github.com/boostorg/core/pull/131#issuecomment-1339617522  

Corrections have been made if you could please kick off the CI run again. The undef should allay @Lastique concerns as well.

---

## Comment 7

> Username: mborland  
> Created_at: 2022-12-07 20:05:03 UTC  
> Url: https://github.com/boostorg/core/pull/131#issuecomment-1341524968  

Pending further review this should be good to merge.

---
