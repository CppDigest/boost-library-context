# #575 Disable warnings from deprecated numeric_limits members. [Merged]

> Username: jzmaddock  
> Created at: 2023-12-07 16:47:26 UTC  
> Updated at: 2025-05-27 00:15:29 UTC  
> Merged at: 2023-12-08 11:28:23 UTC  
> Closed at: 2023-12-08 11:28:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/575  

When building with MSVC in C++23 mode.  
Fixes https://github.com/boostorg/multiprecision/issues/573  
Refs https://github.com/boostorg/multiprecision/pull/574

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2023-12-07 16:56:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/575#pullrequestreview-1770629457  

📁 include/boost/multiprecision/cpp_bin_float.hpp

```diff
2163 |+ #pragma warning(pop)
2164 |+ #endif
2165 |    static constexpr bool               has_denorm_loss                                                                                                 = false;
```

> Username: mborland  
> Created_at: 2023-12-07 16:56:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/575#discussion_r1419292765  

Do the instances of `has_denorm_loss` need to go in between the pragmas since it's deprecated as well?

> Username: jzmaddock  
> Created_at: 2023-12-07 17:01:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/575#discussion_r1419300860  

I don't believe so... it's a simple bool so no warning is generated, it's our use of `float_denorm_style` that was causing the issue I believe.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-12-08 11:27:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/575#issuecomment-1847012622  

Single failure is a clone/network issue, merging...

---

## Comment 3

> Username: pps83  
> Created_at: 2025-05-27 00:15:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/575#issuecomment-2910800227  

Seems like clang now also complains if c++23 is used.  
Something like that fixes the issue:  
  
All the places where this was added:  
```c++  
#ifdef _MSC_VER  
#pragma warning(push)  
#pragma warning(disable:4996)  
#endif  
```  
could be changed to:  
```c++  
#if defined(__clang__) && defined(__has_warning)  
# if __has_warning("-Wdeprecated-declarations")  
#  pragma clang diagnostic push  
#  pragma clang diagnostic ignored "-Wdeprecated-declarations"  
# endif  
#elif defined(_MSC_VER)  
# pragma warning(push)  
# pragma warning(disable:4996)  
#endif  
```  
  
and similary:  
```c++  
#ifdef _MSC_VER  
#pragma warning(pop)  
#endif  
```  
can be changed to:  
```c++  
#if defined(__clang__) && defined(__has_warning)  
# if __has_warning("-Wdeprecated-declarations")  
#  pragma clang diagnostic pop  
# endif  
#elif defined(_MSC_VER)  
# pragma warning(pop)  
#endif  
```

---
